# Lab 5 — San Francisco Bay Vessel Movement Map

**Live map:** https://stacyliao.github.io/geog458-labs/lab5/stacyliao-lab5.html

## 1. Study Area and Dates

This project visualizes vessel movements in San Francisco Bay, California, using Automatic Identification System (AIS) data from NOAA MarineCadastre's AccessAIS platform.

- **Study area:** San Francisco Bay and surrounding coastal waters, including the Golden Gate entrance, San Francisco waterfront, Oakland, San Pablo Bay, and nearby Pacific Ocean.
- **Data source:** [NOAA AccessAIS](https://marinecadastre.gov/accessais/)
- **Visualization tools:** Mapbox GL JS and deck.gl TripsLayer.
- **Map controls:** a vessel-type dropdown (Passenger, Tanker, Cargo, Other vessels), a Play/Stop button, a date/time display in Seattle local time, and a time slider.

### Date range and time zones

AccessAIS timestamps are in **UTC**, and I requested the data in UTC. The map's clock and slider display **Pacific Daylight Time (PDT, UTC−7)**, which is Seattle and San Francisco local time in July. The same 48 hours looks like different dates in the two time zones, so both are shown here:

| | UTC (AccessAIS data) | PDT (map clock and slider) |
|---|---|---|
| **Start** | July 16, 2025, 00:00:00 | July 15, 2025, 5:00:00 PM |
| **End** | July 17, 2025, 23:59:59 | July 17, 2025, 4:59:59 PM |
| **Length** | 48 hours | 48 hours |

The slider starts at 5:00 PM PDT on July 15, and the animation opens one hour later, at about 6:00 PM PDT. In the rest of this README, **dates and times followed by "PDT" are the ones shown on the map clock**, and dates labeled "UTC" are the original data dates.

### Why I chose this area and period

I chose San Francisco Bay because it is a busy maritime transportation region with a variety of vessel activities, including passenger ferries, cargo ships, and tankers. The Golden Gate connects the Pacific Ocean to the bay, while San Francisco, Oakland, and the surrounding waterfront areas support different types of maritime transportation. I thought this would be an interesting region for comparing how different vessels use the same waterways.

I selected a relatively short time period (48 hours) to keep the dataset manageable while still capturing movements across different times of day, including a full day and night cycle on each of two days.

### Data preparation

The original AIS data was downloaded as a CSV and converted into JSON using the course Google Colab notebook. The observations were grouped by vessel identifier (MMSI), with coordinates and timestamps organized into trajectories for animation. According to the conversion metadata:

- The CSV had **353,151 rows**. The notebook removed 1,659 duplicate vessel/time rows and no invalid rows, leaving **351,492 observations**.
- These belong to **554 vessel records**, of which **549 have at least two recorded positions** and can be displayed as animated paths.
- Timestamps are stored as seconds since a shared UTC origin (`time_origin_utc` = July 16, 2025, 00:00:00 UTC in `ais_trips_metadata.json`). The map reads this origin and converts it to Pacific time for the clock and slider.

## 2. Interpretation

The animation revealed several differences in movement patterns between vessel types. The four filters helped separate the trajectories and made it easier to identify patterns that would otherwise overlap. Vessel categories follow the AIS ship-type codes in the [USCG AIS Encoding Guide](https://www.navcen.uscg.gov/sites/default/files/pdf/AIS/USCG_Encoding_Guide.pdf): Passenger is 60–69, Cargo is 70–79, Tanker is 80–89, and Other vessels is every remaining code.

### Routes and areas by vessel type

**Passenger vessels (53 paths)** showed several recognizable connections around the San Francisco waterfront. Many paths appeared to converge near downtown San Francisco, with routes extending toward Oakland, Marin County, and northern parts of the bay. Some of these connections appeared repeatedly throughout the animation, suggesting regular passenger transportation routes. This is consistent with the public ferry network: San Francisco Bay Ferry runs routes from the San Francisco Ferry Building to Oakland and Alameda, Richmond, and Vallejo ([San Francisco Bay Ferry](https://sanfranciscobayferry.com/node/9); [overview of routes](https://en.wikipedia.org/wiki/San_Francisco_Bay_Ferry)), and Golden Gate Ferry serves Marin County destinations such as Sausalito, Tiburon and Larkspur ([ferry guide](https://www.nomadotravel.app/en/guides/san-francisco-ferry-guide)). I did not match individual paths to named routes, so these are possible explanations, not confirmed matches. Compared with the other categories, passenger movements formed a more connected network between waterfront destinations.

**Cargo vessels (29 paths)** showed a different pattern. Their movements appeared more concentrated near commercial port areas, particularly Oakland and Richmond. Some trajectories were relatively short and clustered around the waterfront, while others extended into longer connections across the central bay during the observed animation frames. The Oakland pattern fits the Port of Oakland's role as a major container port that handles many vessel calls ([Port of Oakland](https://www.portofoakland.com/port-oakland-steps-ships-carry-bigger-loads-ever)); the Port's own newsletter reports 95 vessel calls in July 2025 ([Port of Oakland Maritime News, Aug 2025](https://content.govdelivery.com/attachments/CAPOAK/2025/08/26/file_attachments/3367105/Maritime%20News-Aug%202025.pdf)). That figure covers the whole month and the whole port, so it is context for the pattern, not something my 48-hour map measures.

**Tanker vessels (21 paths)** showed noticeable activity around Martinez and the Carquinez Strait. Several longer trajectories extended across the study area, including toward the Pacific Ocean. A possible explanation is the concentration of marine oil terminals in this area: California State Lands Commission documents list several terminals along the Carquinez Strait (including Martinez and Benicia) and others in San Pablo Bay at Rodeo and Selby, where tankers transfer crude oil and petroleum products ([State Lands Commission, Amorco Marine Oil Terminal EIR](https://www.slc.ca.gov/wp-content/uploads/sites/355/2018/09/2.0_PD_FEIR.pdf)). My data only shows where tankers moved; it does not show what they were carrying or why. Terminal and refinery operations have also changed over time, so I have not confirmed which facilities were active in July 2025. Tanker movements appeared less focused on short connections between waterfront destinations and more associated with longer shipping corridors.

**Other vessels (446 paths)** displayed the most varied and complex patterns. Numerous small clusters appeared around San Francisco, Oakland, Richmond, and San Rafael, while other paths extended across larger sections of the bay. Unlike passenger vessels, which showed relatively recognizable routes, this category combined many different movement patterns. The larger number of trajectories also created more visual overlap, making individual movements harder to distinguish.

### Patterns over time

To look at time of day, I used the time slider and the Pacific-time clock, and I also counted how many vessels in each category were moving (reported speed of at least 1 knot) during each hour of the dataset. All times below are **PDT**, as shown on the map clock, and dates are PDT calendar dates unless marked UTC.

- **Passenger vessels have the clearest daily rhythm.** Almost no passenger vessels were moving between about 10 PM and 5 AM (0 to 1 vessels in the early-morning hours of July 16 and 17). Activity ramped up around 5 to 7 AM (for example, 6 vessels at 5 AM and 13 at 6 AM on July 16), stayed high through the day at roughly 19 to 28 vessels from about 8 AM to 7 PM, and peaked at 28 vessels around 11 AM on July 16 and 30 vessels around 1 PM on July 17. It dropped sharply in the evening (16 vessels at 8 PM, 7 at 9 PM, and 1 at 10 PM on July 16). The same shape appeared on both days, which fits a repeated daily service pattern. This is consistent with daytime ferry service, though I did not compare it with actual ferry timetables.
- **Cargo vessels were busiest at night and in the early morning.** On July 16, 5 to 8 cargo vessels were moving between midnight and 8 AM, which dropped to 1 to 4 between about 10 AM and 4 PM, then rose again to 5 to 7 vessels from about 5 to 9 PM. This is almost the reverse of the passenger pattern, so cargo vessels may use the same waterways at different times of day. On July 17 very few cargo vessels were moving (0 to 3 per hour).
- **Tanker movement changed between the two days.** On July 16 only 1 to 4 tankers were moving in most hours (with a burst of 6 around midnight), but on July 17 the count climbed from 1 at midnight to 6 or 7 by late morning and afternoon. With only 21 tanker paths and two days, I treat this as an observation, not a regular pattern.
- **Other vessels follow daytime activity.** Counts rose from about 20 vessels overnight to a peak of about 60 to 70 in the afternoon and early evening (70 at about 6 PM on July 16), and fell again after dark. This category includes fishing, sailing, pleasure craft and tugs, so a daytime peak is plausible, but I did not check vessel subtypes.
- **July 15 evening (5 to 11 PM PDT):** The first hours of the data show many passenger and Other vessels still moving (about 20 passenger and about 40 Other vessels at 5 to 7 PM), which then decline toward the late evening. This is the end of a day I only partly captured.

These counts are small for cargo and tankers, and the data covers only two days, so the time-of-day patterns are suggestive rather than conclusive. They also depend on which vessels happen to be reporting speed.

### Comparing categories

One interesting observation was how differently the same geographic area appeared when switching vessel types. Passenger routes emphasized connections between destinations, cargo and tanker routes highlighted commercial waterways and port areas, and Other vessels revealed a more dispersed network of activities. This demonstrated how filtering can make large movement datasets easier to understand.

### Limitations

- **Gaps between observations:** Some trajectories, especially in the Tanker and Other vessels categories, appeared as unusually long, straight lines. These may be caused by gaps between AIS observations because the visualization connects recorded positions without reconstructing the actual route between them. Therefore, the displayed lines do not always represent the exact paths traveled.
- **Overlap:** Overlapping trajectories and the large number of vessels in the Other category can make it difficult to distinguish individual movements.
- **Self-reported types:** Vessel categories depend on the ship-type code each vessel broadcasts, which is entered by the vessel's crew or operator ([MarineTraffic: AIS ship types](https://support.marinetraffic.com/en/articles/9552866)), so some vessels may be categorized imperfectly.
- **Short time window:** The map covers only 48 hours, ending at about 5 PM PDT on July 17, so the last evening is not shown and the patterns may not represent other days.
- **Time zones:** The data is stored in UTC and the map displays Pacific time, so a vessel report at 00:00 UTC on July 16 appears as 5:00 PM PDT on July 15.

## 3. Feature Proposal for a Potential User

A potential user of this visualization could be a maritime transportation planner who wants to understand how different vessel types use San Francisco Bay, and who needs to identify *which specific vessels* are behind a pattern they notice, such as a repeated ferry crossing or a long tanker transit.

The feature I would propose is **hover and click-to-inspect on individual vessel paths**. Right now the map shows many orange trails, but a user cannot tell which vessel a trail belongs to. With this feature, hovering over a trail would show a tooltip with the vessel name, MMSI, and AIS type code, and clicking a trail would highlight that vessel and show details such as its first and last observation times (in Seattle local time) and how many position reports it has.

I reviewed the [deck.gl interactivity guide](https://deck.gl/docs/developer-guide/interactivity), which explains that a layer can be made `pickable` and that `onHover`, `onClick`, and `getTooltip` receive an info object containing the object under the pointer. This informed my proposal because each path in `ais_trips.json` is already one vessel object with `name`, `mmsi`, and `type` fields, so the TripsLayer could be made pickable and the existing data could populate the tooltip without any new data processing. The existing vessel-type dropdown and time slider would still work alongside it.

This would be useful because a planner could move from "there is a regular passenger pattern between San Francisco and Oakland" to "these specific vessels make that crossing," and could check whether a long straight line belongs to one vessel with sparse AIS reports instead of a real route. It would turn the map from a general overview into a tool for investigating individual movements.

## Sources

- NOAA MarineCadastre. [AccessAIS](https://marinecadastre.gov/accessais/).
- U.S. Coast Guard Navigation Center. [USCG AIS Encoding Guide](https://www.navcen.uscg.gov/sites/default/files/pdf/AIS/USCG_Encoding_Guide.pdf).
- MarineTraffic Help. [What is the significance of the AIS Shiptype or Vessel type number?](https://support.marinetraffic.com/en/articles/9552866)
- San Francisco Bay Ferry. [Downtown San Francisco terminal and routes served](https://sanfranciscobayferry.com/node/9); [San Francisco Bay Ferry overview](https://en.wikipedia.org/wiki/San_Francisco_Bay_Ferry).
- Nomado Travel. [San Francisco Ferry Guide](https://www.nomadotravel.app/en/guides/san-francisco-ferry-guide) (Golden Gate Ferry routes).
- Port of Oakland. [Port of Oakland steps up as ships carry bigger loads than ever](https://www.portofoakland.com/port-oakland-steps-ships-carry-bigger-loads-ever); [Maritime News, August 2025](https://content.govdelivery.com/attachments/CAPOAK/2025/08/26/file_attachments/3367105/Maritime%20News-Aug%202025.pdf).
- California State Lands Commission. [Amorco Marine Oil Terminal Lease Consideration, Final EIR](https://www.slc.ca.gov/wp-content/uploads/sites/355/2018/09/2.0_PD_FEIR.pdf).
- deck.gl. [Adding Interactivity](https://deck.gl/docs/developer-guide/interactivity); [TripsLayer](https://deck.gl/docs/api-reference/geo-layers/trips-layer).