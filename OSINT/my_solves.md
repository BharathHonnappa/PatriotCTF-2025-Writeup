PatriotCTF 2025 featured a fun series of OSINT challenges titled "Where’s Legally Distinct Waldo"—four geolocation puzzles that required finding very specific spots on the George Mason University campus.

Here’s how I solved all four of them.

Challenge 1 — Where’s Legally Distinct Waldo One
Challenge:

Press enter or click to view image in full size

Challenge Name & Description

Press enter or click to view image in full size

Image Given

I began with a standard reverse image search, using Yandex for its superior performance in geolocation tasks.

Press enter or click to view image in full size

Yandex Search Result

While Yandex didn't identify the exact building, it consistently suggested George Mason University, which was enough to narrow down the search area significantly.

With a location in mind, I began a manual scan of the campus using Google Maps and Google Earth.

Press enter or click to view image in full size

GMU Aerial View / Street View Network Tower

The key details in the challenge image were a distinctive tiled walking path and a prominent tower.

Press enter or click to view image in full size

Distinctive Tiled Walking Path

After searching the campus on Google Maps, I found a perfect match for the path's unique design and the surrounding layout. The only tall building positioned correctly relative to both the path and a nearby brown building was Horizon Hall.

Flag: pctf{Horizon_Hall}

Challenge 2 — Where’s Legally Distinct Waldo Two
Challenge:

Press enter or click to view image in full size

Challenge Name & Description

We were given another image:

Press enter or click to view image in full size

Image Given

What immediately stood out to me were the distinctive roof panels.

I began searching from an aerial view, looking for buildings with similar roof panels.

Press enter or click to view image in full size

First Search Result

Press enter or click to view image in full size

I pulled the location up on Google Earth to confirm:

Press enter or click to view image in full size

View from Above | Google Earth

Press enter or click to view image in full size

A "No Parking" sign on the road and the name of the crossroad confirmed it was the correct place.

Press enter or click to view image in full size

I then took a virtual walk via Street View and found this sign on the side of the building:

Press enter or click to view image in full size

Flag: pctf{Thompson_Hall}

Challenge 3 — Where’s Legally Distinct Waldo Three
Challenge:

Press enter or click to view image in full size

Challenge Name & Description

Challenge image:

Press enter or click to view image in full size

Image Given

Since the first two challenges were on GMU’s campus, I continued searching there. This time, the giveaway was obvious: a lake or pond.

This was one of the easiest challenges.

Press enter or click to view image in full size

The challenge image also showed a large parking lot:

Press enter or click to view image in full size

From the correct angle on Google Maps, everything matched—the parking layout, trees and sidewalks.

Press enter or click to view image in full size

This view pointed directly to the building in the background.

Press enter or click to view image in full size

I went to Street View to confirm the building's exact name and found the sign:

Press enter or click to view image in full size

It was the "Center for the Arts / Concert Hall."

After a few attempts at formatting the name correctly, the accepted flag was:

Flag: pctf{Center_for_the_Arts_Concert_Hall}

Challenge 4 — Where’s Legally Distinct Waldo Four
Challenge:

Press enter or click to view image in full size

Challenge Name & Description

This one took a long time and was definitely the hardest of the four.

Challenge image:

Press enter or click to view image in full size

Again, I assumed it was somewhere on the GMU campus. This time, the only clues were partial views of three buildings:

Press enter or click to view image in full size

The distinctive trio consisted of:

A one-piece gray block structure.

An orange-ish building.

A black-ish building with vertical lines.

I spent 2–3 hours scanning the campus for this specific combination. The closest matches were near the Fenwick area or Krug Hall, so I began analyzing small structural details—zooming in to compare window spacing, roof edges, and any other distinctive features.

Press enter or click to view image in full size

I initially gave up, but later I examined a 360-degree view from above the Johnson Center.

https://maps.app.goo.gl/DvCthseVXvcsDLrXA

I had noticed this spot earlier but dismissed it too quickly. Upon my second review, I found the exact match:

Press enter or click to view image in full size

FINALLY!!!

It was a perfect match. The location was indeed Krug Hall.

Flag: pctf{Krug_Hall}