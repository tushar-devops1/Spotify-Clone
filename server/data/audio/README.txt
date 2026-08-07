HOW TO ADD YOUR OWN (legally-owned) MP3s
========================================

The app plays real audio for a song when a file with the matching name
exists in this folder.

1. Put an MP3 file here named:

   <title>-<artist>.mp3

   using lowercase letters, numbers and dashes (spaces, dots, & and '
   become dashes).

2. Examples:
   - Tum Hi Ho by Arijit Singh  ->  tum-hi-ho-arijit-singh.mp3
   - Shape of You by Ed Sheeran ->  shape-of-you-ed-sheeran.mp3
   - Neon Horizon by Pulse Theory -> neon-horizon-pulse-theory.mp3

3. Restart the server (or just refresh — file checks run per request),
   and the app will stream YOUR file instead of the placeholder demo audio.

To list every song's expected filename, visit:
   GET /api/audio-manifest   (requires a login token)
