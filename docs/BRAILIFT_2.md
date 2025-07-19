# Brainlift 2: Human Body Tracking and Interactive Challenges

## Interaction Obstacles and Player Engagement

Yesterday and today revealed some pretty heavy challenges with player interaction systems. The core issue centered around objectives and pylons that players needed to either interact with or physically reach. I couldn't get the timers to stop or restart when I reached the pylons. After doing a bunch of research, I found two paths to take, in which human body tracking was the most effective and accurate decision.  VPS would have been severely inaccurate.

## Pivoting to Human Body Tracking

I made the decision to explore human body tracking and interaction systems. This approach just seemed like the more intuitive and immersive player experience; however, this pivot introduced unexpected complexities, particularly with AR camera configuration and integration.

## Technical Challenges and Camera Configuration

The integration of human body tracking created immediate conflicts with the existing AR camera setup. The complexity of managing multiple tracking systems simultaneously required extensive research into proper configuration methods. Current docs proved insufficient for addressing the specific interaction between body tracking and camera management, requiring a deeper dive into the underlying systems.

## Custom Testing Scripts and C# Development

To resolve the camera configuration issues, I developed custom testing scripts in C#. This approach allowed for precise control over the tracking initialization sequence and provided real-time feedback on system behavior. The custom scripts became essential debugging tools, enabling systematic testing of different configuration approaches until a stable solution emerged.

## Breakthrough in Body Tracking Implementation

After iterative testing and refinement, the human body tracking system began functioning correctly. The breakthrough moment came when hand motions, hand tracking, and foot tracking all achieved reliable detection and response. This success validated the decision to pursue body tracking as a more natural interaction paradigm for the AR experience.

## Inspiration for Soccer Ball Interaction

The successful implementation of foot tracking sparked an exciting new possibility: integrating a virtual soccer ball that players could physically kick while wearing AR glasses or using a mobile device. This concept represents a perfect fusion of the game's geospatial nature with intuitive physical interaction, potentially creating a unique gameplay mechanic that leverages the full potential of body tracking technology.

## Hardware Adaptation and Display Optimization

Significant progress was made in hardware compatibility, successfully getting the application to function on AR glasses and achieving horizontal screen operation. However, a persistent challenge emerged: the application continues to render using iPhone screen dimensions rather than utilizing the full display capabilities of the glasses.

## Next Steps: Wrapper Development

To address the screen dimension limitations, development focus has shifted to creating a proper wrapper system. This wrapper will enable the application to dynamically adapt to the native screen dimensions of AR glasses, ensuring optimal utilization of the available display real estate and providing a truly immersive experience.

## Technical Evolution and Future Outlook

Today's session demonstrated the importance of flexibility in AR development. The transition from simple interaction points to sophisticated body tracking represents a significant technical evolution that opens new possibilities for player engagement. The successful integration of hand and foot tracking, combined with the vision for soccer ball interaction, positions the project for innovative gameplay mechanics that could set new standards for geospatial AR experiences.

This progression from interaction challenges to breakthrough body tracking implementation illustrates the iterative nature of cutting-edge AR development, where obstacles often lead to more sophisticated and engaging solutions.
