# Bucharest to Groningen, 28 to 30 January 2027

A one-file trip planner. Pick a flight, a fare and a last mile for each direction, and the map, the
itinerary and the total follow. No build step, no API keys, no backend.

Live: https://dodoloiu1.github.io/groningen-trip/

## What it does

- Cascading pickers for the outbound and the return: flight, then fare, then the ground leg. The
  third step is filtered by the airport the flight lands at.
- The map draws the selected route. Ground legs follow their real corridor, drawn from coordinates
  stored in the JSON; flights are dashed arcs. Teal is outbound, plum is return.
- The itinerary lists every leg with times, duration, price and the catch (bag rules, peak-hour
  fares, unverified placeholders).
- A running total in EUR and RON, plus door to door time.
- Warnings that are worked out, not hard-coded: missing connections, the Wizz 09:25 problem, tight
  airport buffers, Dal Voordeel that does not apply in peak hours.
- A comparison table with four preset combinations. Load one to edit it.

## The data

All of it sits in one `<script type="application/json" id="trip-data">` block near the bottom of
`index.html`. It is plain JSON: open the file, edit the block, reload. Nothing else in the file has
to change to add a flight, a fare, a ground leg or a path.

```
meta         dates, passenger count
fx           RON per euro (also editable in the header)
assumptions  hotel cost, airport buffer, NS peak windows, overnight cutoff
nodes        airport and station coordinates, each with its UTC offset in tz
carriers     KLM fares; Wizz fare parts in RON
outbound     flights, and ground options grouped by arrival airport
return       flights, and ground options grouped by departure airport
presets      the four rows in the comparison table
paths        [lat, lng] polylines drawn on the map, keyed "FROM-TO"
```

A ground option can hold more than one leg (Schiphol to Sloterdijk, then the bus), and the planner
chains the departures so the times line up.

Schedules come in three shapes:

| Shape | Meaning |
| --- | --- |
| `{"every": 60, "anchor": "00:59", "service": ["05:30","23:30"]}` | Recurring departures |
| `{"times": ["10:05"]}` | Fixed departures only |
| `{"tbd": true, "wait": 45}` | Timetable unknown, departure estimated from the arrival |

`priceRON` prices a leg in lei instead of euro, so the exchange rate field in the header moves it.
`priceDal` is the NS Dal Voordeel price. `unverified: true` marks a price the warning panel lists.

`paths` holds the drawn route. A reverse trip reuses the same entry, reversed, and the return line is
offset a little so both stay visible. To redraw a path, replace the array with any list of
`[lat, lng]` points.

## Tiles

OpenStreetMap's own tile server refuses some networks with a 403. The map starts there and switches
on its own, first to tile.openstreetmap.de, then to Esri's grey canvas basemap. All three need no
key. Attribution sits in the map corner either way.

## What was checked and what was not

Collected by hand in late November 2026: the KLM fare table, the Wizz RON breakdown for both
directions, the NS Intercity times and full fares, the Schiphol to Sloterdijk sprinter, the 10:05
FlixBus, and the Eindhoven train patterns (:46 and :16 northbound, :18 and :48 southbound).

Not checked, and marked in the app: the Eindhoven airport bus fare and duration, the FlixBus
timetables to and from Eindhoven, the FlixBus back from Groningen, the AMS to OTP return prices and
times, and the hotel cost. The Wizz price without the bundle is an estimate built from the two fares
plus admin fees.

Train times are pattern-based: the planner assumes a departure every hour or half hour inside
service hours. Real timetables drift, especially on a Saturday in January. Check the exact trains
on ns.nl before booking.

## Running it

Open `index.html` in a browser. That is all.

Served from GitHub Pages on the `main` branch, so a push is a deploy.
