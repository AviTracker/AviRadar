AviRadar

AviRadar is a browser-based live aircraft radar centered on AVILUS Radar HQ in Ismaning, Germany. It combines public ADS-B and MLAT data with a Cloudflare Worker, Cloudflare D1 storage and a static GitHub Pages frontend.

The application is designed as an operational-style situational awareness interface for publicly available flight data. It is not intended for navigation, air traffic control or operational decision-making.

Features

Live aircraft map

Live aircraft positions within a fixed 200 NM radius around Ismaning

Aircraft icons based on aircraft category

Dedicated helicopter top-view icon

Altitude, speed and vertical rate in metric units

Distance from AVILUS Radar HQ

MUC inbound and outbound estimation

Military and emergency highlighting

Aircraft registration country with flag

Mexico-related aircraft highlighted in #ff4500

Aircraft details

Registration as the primary title

Registration country displayed next to the registration

Callsign, aircraft type and heading

Altitude in metres

Speed in kilometres per hour

Climb and descent rate in metres per second

Route information where available

Aircraft Intelligence assessment

Aircraft photo with strict registration verification

Direct link to Planespotters

Aircraft Intelligence

AviRadar combines several sources and fallback methods to identify aircraft:

Confirmed external aircraft database data

Local D1 aircraft intelligence records

Registration pattern recognition

ICAO hex country blocks

Airline callsign prefixes

Military callsign patterns

ICAO aircraft type code translation

Flight-profile-based classification

Examples:

DLH715 → Lufthansa

A359 → Airbus A350-900

54+42 + GAF253 + A400 → German Air Force Airbus A400M

22-03472 + H64 → United States military helicopter / AH-64 family

Assessments are marked with a confidence level:

CONFIRMED

HIGH

MEDIUM

LOW

Unconfirmed identifications are estimates and may be incorrect.

Flight tracks

Persistent track storage in Cloudflare D1

Only the currently selected aircraft track is shown

The previous aircraft track disappears when another aircraft is selected

Tracks are split into current flight segments

A new segment begins after:

a callsign change

a longer data gap

a detected ground phase followed by a new departure

This prevents previous legs of frequently operating aircraft from appearing as one continuous route.

MUC departures

Central detection of likely departures from Munich Airport

Persistent storage in D1

Callsign, aircraft, destination and status where available

Mexico-related departures highlighted in #ff4500

The departure list is based on position, direction, altitude and vertical movement. It is not an official flight information display.

Airports and control zones

AVILUS Radar HQ shown in Ismaning

Selected larger airports and airfields with paved runways

Simplified control-zone overview

Airport layer can be enabled or disabled

Airport and control-zone data are for visual orientation only and must not be used for navigation or flight planning.

Live weather

The weather panel displays current conditions only:

temperature

weather condition

wind

gusts

precipitation

No weather forecast is shown.

Live users

The frontend sends a heartbeat to the Worker every 30 seconds.

A user is considered active when the browser tab has contacted the Worker within the last 90 seconds.

Notes:

multiple tabs may count as multiple users

a closed tab may remain visible for up to 90 seconds

the figure is an approximation, not a unique-person count

Architecture

GitHub Pages frontend
        |
        v
Cloudflare Worker
        |
        +--> ADSB.lol live aircraft data
        +--> ADSBDB route and aircraft data
        +--> Open-Meteo current weather
        +--> Cloudflare D1

Repository structure

/
├── index.html
└── README.md

The Cloudflare Worker is deployed separately through the Cloudflare dashboard.

Cloudflare Worker endpoints

Health

GET /health

Returns service status and binding availability.

Aircraft

GET /aircraft

Returns live aircraft data within the configured radius.

Route

GET /route?callsign=DLH715

Returns route information and caches the result in D1.

Track

GET /track?icao=3c64a7&minutes=60

Returns persistent track points for one aircraft.

Departures

GET /departures?hours=3

Returns likely departures detected from Munich Airport.

Presence

GET /presence?session=SESSION_ID

Registers a browser heartbeat and returns the estimated number of active users.

Aircraft identification

GET /identify?icao=3e95ca&registration=14%2B05&callsign=GAF629&type=GLEX

Returns the best available identification from confirmed data, cached records and heuristic fallback logic.

Cloudflare D1

The Worker binding must be named:

DB

Recommended database name:

marvin-radar

The application uses the following tables:

ingest_runs
aircraft_state
track_points
route_cache
departures
active_sessions
aircraft_intelligence
aircraft_lookup_cache

The exact set of tables depends on the deployed Worker version.

Deployment

1. Deploy the Cloudflare Worker

Open Cloudflare Dashboard

Go to Workers & Pages

Open the AviRadar Worker

Select Edit code

Replace the complete Worker code

Click Deploy

2. Add the D1 binding

In the Worker settings, create the following binding:

Type: D1 database
Variable name: DB
Database: marvin-radar

3. Add the Cron Trigger

Create this Cron Trigger:

* * * * *

This runs the ingest process once per minute and stores tracks and departure data even when the website is not open.

4. Test the Worker

Open:

https://aviradar-api.joschko-hammermann.workers.dev/health

Expected result:

{
  "ok": true,
  "bindings": {
    "database": true
  }
}

Then test:

https://aviradar-api.joschko-hammermann.workers.dev/aircraft

5. Deploy the frontend

Open the GitHub repository

Replace the complete index.html

Commit the changes

GitHub Pages deploys automatically

Configuration

The frontend uses the following Worker URL:

const AIRCRAFT_API = "https://aviradar-api.joschko-hammermann.workers.dev";

The radar center is configured for Ismaning:

const CENTER = {
  lat: 48.226,
  lon: 11.675
};

The current radius is fixed at:

const RADAR_RADIUS_NM = 200;

Data sources

AviRadar uses public and third-party data sources, including:

ADSB.lol for live ADS-B and MLAT data

ADSBDB for route and aircraft metadata

Open-Meteo for current weather

Wikimedia Commons for aircraft images

Planespotters as an external photo reference

OpenStreetMap-derived or curated airport data for map orientation

Data completeness and accuracy vary by source and aircraft.

Known limitations

Military aircraft often suppress or omit registration and type data

Callsigns may change between missions

Aircraft type codes may be incomplete or incorrect

Route data may be unavailable

Photos are only shown when a sufficiently reliable registration match is found

MUC inbound and outbound status is geometrically estimated

Departure detection is not an official airport data feed

Airport control zones are simplified and not navigation-grade

Live-user counting is session-based and approximate

ADS-B and MLAT coverage depends on public receiver availability

Privacy

AviRadar does not require user accounts.

The live-user counter stores temporary anonymous session identifiers and timestamps. It does not identify individual people.

Safety notice

AviRadar displays publicly available aviation data for informational and demonstration purposes only.

Do not use AviRadar for:

navigation

air traffic control

flight planning

operational command

safety-critical decisions

Project status

AviRadar is under active development. Features, data sources and classification logic may change as the project evolves.
