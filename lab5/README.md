# Lab 5 — San Francisco Bay Vessel Movement Map

## 1. Study Area and Dates

This project visualizes vessel movements in San Francisco Bay, California, using Automatic Identification System (AIS) data from NOAA MarineCadastre's AccessAIS platform.

- **Study area:** San Francisco Bay and surrounding coastal waters, including the Golden Gate entrance, San Francisco waterfront, Oakland, San Pablo Bay, and nearby Pacific Ocean.
- **Date range:** July 16, 2025, 00:00 UTC to July 17, 2025, 23:59:59 UTC (48 hours).
- **Data source:** [NOAA AccessAIS](https://marinecadastre.gov/accessais/)
- **Visualization tools:** Mapbox GL JS and deck.gl TripsLayer.

I chose San Francisco Bay because it is a busy maritime transportation region with a variety of vessel activities, including passenger ferries, cargo ships, and tankers. The Golden Gate connects the Pacific Ocean to the bay, while San Francisco, Oakland, and the surrounding waterfront areas support different types of maritime transportation. I thought this would be an interesting region for comparing how different vessels use the same waterways.

I selected a relatively short time period to keep the dataset manageable while still capturing movements across different times of day. The original AIS data was downloaded as a CSV and converted into JSON using the course Google Colab notebook. The observations were grouped by vessel identifier (MMSI), with coordinates and timestamps organized into trajectories for animation.

The converted dataset contains 554 vessel records, of which 549 have at least two recorded positions and can be displayed as animated paths. The timestamps are measured relative to a shared UTC origin, and the converted data spans approximately 48 hours.

## 2. Interpretation

The animation revealed several differences in movement patterns between vessel types. The four filters helped separate the trajectories and made it easier to identify patterns that would otherwise overlap.

**Passenger vessels (53 paths)** showed several recognizable connections around the San Francisco waterfront. Many paths appeared to converge near downtown San Francisco, with routes extending toward Oakland, Marin County, and northern parts of the bay. Some of these connections appeared repeatedly throughout the animation, suggesting regular passenger transportation routes. Compared with the other categories, passenger movements formed a more connected network between waterfront destinations.

**Cargo vessels (29 paths)** showed a different pattern. Their movements appeared more concentrated near commercial port areas, particularly Oakland and Richmond. Some trajectories were relatively short and clustered around the waterfront, while others extended farther through San Pablo Bay. Compared with passenger vessels, cargo routes appeared less dense and did not show as many repeated connections across the central bay during the observed animation frames.

**Tanker vessels (21 paths)** showed noticeable activity around Martinez and the Carquinez Strait. Several longer trajectories extended across the study area, including toward the Pacific Ocean. This may be related to the petroleum and industrial facilities located around Martinez. Tanker movements appeared less focused on short connections between waterfront destinations and more associated with longer shipping corridors.

**Other vessels (446 paths)** displayed the most varied and complex patterns. Numerous small clusters appeared around San Francisco, Oakland, Richmond, and San Rafael, while other paths extended across larger sections of the bay. Unlike passenger vessels, which showed relatively recognizable routes, this category combined many different movement patterns. The larger number of trajectories also created more visual overlap, making individual movements harder to distinguish.

One interesting observation was how differently the same geographic area appeared when switching vessel types. Passenger routes emphasized connections between destinations, cargo and tanker routes highlighted commercial waterways and port areas, and Other vessels revealed a more dispersed network of activities. This demonstrated how filtering can make large movement datasets easier to understand.

There were also limitations in interpreting the animation. Some trajectories, especially in the Tanker and Other vessels categories, appeared as unusually long, straight lines. These may be caused by gaps between AIS observations because the visualization connects recorded positions without reconstructing the actual route between them. Therefore, the displayed lines do not always represent the exact paths traveled. Additionally, overlapping trajectories and the large number of vessels in the Other category can make it difficult to distinguish individual movements.

## 3. Feature Proposal for a Potential User

A potential user of this visualization could be a maritime transportation planner who wants to understand how different vessel types use San Francisco Bay and identify when certain waterways experience more activity.

One feature I would propose is an **interactive timeline slider** that allows users to select a specific time rather than only watching the animation play continuously. The current map includes a Play/Stop button, but users cannot easily jump to a particular moment or compare vessel activity at different times of day.

With a timeline slider, a transportation planner could move between morning, afternoon, and evening periods to examine when passenger, cargo, or tanker activity is most noticeable. This could be especially useful for exploring whether different vessel categories use the same waterways at different times.

I reviewed the [deck.gl TripsLayer documentation](https://deck.gl/docs/api-reference/geo-layers/trips-layer), which explains how the `currentTime` property controls which portions of animated paths are displayed. This informed my proposal because a timeline slider could update `currentTime` directly based on the user's selected time. The existing vessel-type dropdown could remain available, allowing users to combine category filtering with temporal exploration.

This feature would make the map more useful for investigating specific patterns instead of relying entirely on continuous playback. It would also help users compare activity across time periods while keeping the visualization focused and interactive.