# CrisisLens
AI-powered emergency decision and response platform
# CrisisLens --- AI Emergency Decision & Response Platform

## Implementation Specification for an AI Coding Agent

Version: 1.0 Status: Hackathon MVP → scalable prototype Primary stack:
Python + Streamlit + Groq-compatible LLM API Deployment target:
Streamlit Community Cloud Primary initial country: Pakistan Initial
emergency categories: Fire, Flood, Medical, Crime/Security, Earthquake,
Extreme Heat

------------------------------------------------------------------------

## 1. Product Definition

### One-line pitch

CrisisLens is a GenAI emergency decision-support platform that converts
an unstructured emergency description into a structured risk assessment,
prioritized action plan, missing-information checklist, verified
emergency-service contacts, and ready-to-send emergency communication.

### Core problem

During an emergency, people may have incomplete information, be under
stress, and not know:

-   what the immediate hazards are;
-   what should be prioritized;
-   what information emergency responders need;
-   which verified emergency service to contact;
-   what number to call;
-   what to tell the responder;
-   how to communicate the situation clearly.

CrisisLens addresses the information and communication problem. It does
NOT replace emergency dispatchers, medical professionals, police,
firefighters, or official emergency instructions.

### Core principle

Use GenAI for interpretation and natural-language generation.

Use deterministic software and verified data for:

-   emergency numbers;
-   country/service mapping;
-   risk rules;
-   validation;
-   contact actions;
-   auditability.

Never ask an LLM to invent or determine an emergency phone number.

------------------------------------------------------------------------

# 2. Main User Journey

1.  User opens CrisisLens.
2.  User selects country.
3.  User optionally selects region/city.
4.  User describes what is happening in natural language.
5.  User optionally provides:
    -   number of people;
    -   vulnerable people;
    -   location;
    -   injuries;
    -   hazards;
    -   preferred language.
6.  User clicks `Analyze Emergency`.
7.  LLM extracts structured facts.
8.  Backend validates the extracted JSON.
9.  Deterministic risk engine calculates severity.
10. System identifies missing critical information.
11. System selects relevant verified emergency services from the
    database.
12. LLM generates a concise, structured action plan.
13. UI displays:

-   emergency type;
-   severity;
-   detected hazards;
-   immediate priorities;
-   what to avoid;
-   missing information;
-   emergency contacts;
-   call buttons;
-   emergency message.

14. User can:

-   call/dial a number;
-   copy an emergency message;
-   open an email draft;
-   optionally authorize Gmail and send the email;
-   share/copy the emergency summary;
-   switch language.

------------------------------------------------------------------------

# 3. Example

Input:

> Heavy rain has flooded our street. Water is entering our house.
> Electricity is still on. There are six people inside and my father
> uses a wheelchair. The main road is blocked.

Expected analysis:

Emergency type: - Flood

Detected hazards: - Flood water - Active electricity - Blocked route -
Vulnerable person

Severity: - HIGH

Critical missing information: - Water depth - Safe alternate exit -
Structural condition - Whether electricity can be safely isolated

Immediate priorities: 1. Avoid contact with floodwater where electrical
hazards may exist. 2. Prioritize the safety of vulnerable people. 3.
Identify a safe route away from the hazard. 4. Contact appropriate
emergency services if immediate assistance is required.

For Pakistan/Punjab, the initial verified emergency dataset can
include: - Police: 15 - Rescue Service: 1122 - Fire Brigade: 16 - Edhi
Main Control Room: 115

These values must be stored in the application database and verified
against authoritative sources before release. Punjab Police currently
publishes these helplines on its official emergency-help page.

------------------------------------------------------------------------

# 4. Important Safety Boundary

CrisisLens is NOT:

-   an emergency dispatcher;
-   a medical diagnosis system;
-   a replacement for emergency services;
-   a guarantee that an evacuation route is safe;
-   a system that automatically contacts police/ambulance/fire services
    without user confirmation.

The application must avoid statements such as:

-   "You are definitely safe."
-   "The building is safe."
-   "You do not need emergency services."
-   "This is definitely not life-threatening."

Prefer:

-   "Based on the information provided..."
-   "Potential hazard detected..."
-   "The system cannot determine..."
-   "If there is immediate danger, contact the appropriate emergency
    service."

For critical situations, the UI should clearly direct the user toward
official emergency services.

------------------------------------------------------------------------

# 5. Product Architecture

``` text
                         CRISISLENS
                             |
                    +--------+--------+
                    |                 |
                 Country          Situation
                    |                 |
                    |                 v
                    |           LLM Parser
                    |                 |
                    |                 v
                    |          Structured JSON
                    |                 |
                    +--------> Validator
                                      |
                                      v
                                Risk Engine
                                      |
                         +------------+------------+
                         |            |            |
                         v            v            v
                       Risk        Hazards      Missing Info
                         |            |            |
                         +------------+------------+
                                      |
                                      v
                            Emergency Database
                                      |
                         +------------+------------+
                         |            |            |
                       Police       Rescue       Fire
                         |            |            |
                         +------------+------------+
                                      |
                                      v
                              Response Generator
                                      |
               +----------------------+----------------------+
               |             |             |                |
               v             v             v                v
          Action Plan     Call/Dial     Email Draft      Share/Copy
```

------------------------------------------------------------------------

# 6. Recommended Repository Structure

``` text
CrisisLens/
│
├── app.py
├── requirements.txt
├── README.md
├── .env.example
├── .gitignore
│
├── backend/
│   ├── __init__.py
│   │
│   ├── ai/
│   │   ├── __init__.py
│   │   ├── client.py
│   │   ├── extractor.py
│   │   └── planner.py
│   │
│   ├── risk/
│   │   ├── __init__.py
│   │   ├── engine.py
│   │   └── rules.py
│   │
│   ├── emergency/
│   │   ├── __init__.py
│   │   ├── contacts.py
│   │   └── service_router.py
│   │
│   ├── communication/
│   │   ├── __init__.py
│   │   ├── message_generator.py
│   │   └── email_service.py
│   │
│   └── validation/
│       ├── __init__.py
│       └── schemas.py
│
├── data/
│   ├── emergency_services.json
│   ├── countries.json
│   └── regions.json
│
├── prompts/
│   ├── extraction.txt
│   ├── planning.txt
│   └── communication.txt
│
├── tests/
│   ├── test_risk_engine.py
│   ├── test_contacts.py
│   ├── test_validation.py
│   └── test_scenarios.py
│
└── docs/
    ├── architecture.md
    └── emergency-data-sources.md
```

Do not create unnecessary microservices. The initial version should be a
single Streamlit/Python application.

------------------------------------------------------------------------

# 7. Frontend Requirements

Use Streamlit.

## Home screen

Components:

-   CrisisLens logo/title
-   short explanation
-   country selector
-   region/city selector
-   emergency description textarea
-   optional people count
-   optional vulnerable-person selector
-   optional location field
-   language selector
-   `Analyze Emergency` button

Example:

``` text
CRISISLENS
AI Emergency Decision & Response Assistant

Country
[ Pakistan ]

Region / City
[ Rawalpindi ]

What is happening?
[ __________________________________ ]

People involved
[ 6 ]

Vulnerable people
[ Elderly / Wheelchair / Child ]

Language
[ English ]

[ ANALYZE EMERGENCY ]
```

------------------------------------------------------------------------

# 8. Result Screen

Use visually distinct sections.

## Situation

``` text
Emergency: Flood
Severity: HIGH
Confidence: Moderate
```

## Detected Hazards

-   Floodwater
-   Electricity
-   Blocked route
-   Vulnerable person

## Immediate Actions

Numbered and concise.

## Avoid

Show unsafe actions the user should not take.

## Missing Information

Display information that could materially change the risk assessment.

## Emergency Services

Each service should have:

-   service name;
-   verified number;
-   service category;
-   country/region;
-   source;
-   last verified date;
-   `Call` button;
-   optional `Copy Number`.

------------------------------------------------------------------------

# 9. Calling / Dialing Feature

## Required behavior

On a mobile-capable device, the application should provide a
user-initiated telephone link:

``` text
tel:1122
```

The button can be rendered as:

``` text
[ CALL RESCUE 1122 ]
```

Clicking should invoke the device/browser's telephone handler where
supported.

### Critical technical limitation

A normal web/Streamlit application cannot silently place a telephone
call on behalf of the user.

The safe and realistic behavior is:

``` text
User clicks CALL
        |
        v
tel:number
        |
        v
Device/browser opens dialer
        |
        v
User confirms/calls
```

Do NOT implement automatic background calling.

Desktop browsers may not have a phone handler. The UI should gracefully
display:

> "Calling is available on supported mobile devices. You can copy the
> number if your device cannot place calls."

------------------------------------------------------------------------

# 10. Emergency Contact Data

Start with Pakistan.

Example data model:

``` json
{
  "country_code": "PK",
  "country_name": "Pakistan",
  "regions": {
    "Punjab": {
      "services": [
        {
          "id": "police_15",
          "name": "Police Emergency",
          "category": "police",
          "number": "15",
          "source": "Punjab Police",
          "source_url": "https://www.punjabpolice.gov.pk/emergency_help",
          "verified_at": "YYYY-MM-DD"
        },
        {
          "id": "rescue_1122",
          "name": "Rescue Service",
          "category": "rescue",
          "number": "1122",
          "source": "Punjab Police / Rescue 1122",
          "source_url": "https://www.punjabpolice.gov.pk/emergency_help",
          "verified_at": "YYYY-MM-DD"
        },
        {
          "id": "fire_16",
          "name": "Fire Brigade",
          "category": "fire",
          "number": "16",
          "source": "Punjab Police",
          "source_url": "https://www.punjabpolice.gov.pk/emergency_help",
          "verified_at": "YYYY-MM-DD"
        },
        {
          "id": "edhi_115",
          "name": "Edhi Main Control Room",
          "category": "ambulance",
          "number": "115",
          "source": "Punjab Police",
          "source_url": "https://www.punjabpolice.gov.pk/emergency_help",
          "verified_at": "YYYY-MM-DD"
        }
      ]
    }
  }
}
```

Do not assume these contacts are universally applicable to every
Pakistani province/city without verification.

For international expansion, every country's data should be
independently verified.

------------------------------------------------------------------------

# 11. Emergency Service Routing

Do not let the LLM choose a phone number.

Use deterministic routing.

Example:

``` python
CATEGORY_TO_SERVICE = {
    "fire": "fire",
    "medical": "ambulance",
    "crime": "police",
    "security": "police",
    "flood": "rescue",
    "earthquake": "rescue",
    "building_collapse": "rescue"
}
```

Flow:

``` text
AI detects emergency_type
        |
        v
Python routing rule
        |
        v
Country + region database
        |
        v
Verified service
        |
        v
Phone number
```

If no verified service exists:

``` text
Do not guess.

Display:
"Verified emergency contact unavailable
for this location. Please use your local
official emergency directory."
```

------------------------------------------------------------------------

# 12. AI Extraction Contract

The first LLM call should return JSON only.

Suggested schema:

``` json
{
  "emergency_type": "flood",
  "severity_indicators": [
    "electricity_active",
    "water_entering_building"
  ],
  "hazards": [
    "flood_water",
    "electrical_hazard"
  ],
  "people_count": 6,
  "vulnerable_people": [
    "wheelchair_user"
  ],
  "injuries": [],
  "location_context": "house",
  "evacuation_route": "blocked",
  "immediate_danger": true,
  "missing_information": [
    "water_depth",
    "safe_exit",
    "building_condition"
  ],
  "confidence": "moderate"
}
```

The backend must validate this JSON before using it.

------------------------------------------------------------------------

# 13. Risk Engine

The risk engine is deterministic.

Suggested output:

``` text
LOW
MEDIUM
HIGH
CRITICAL
```

Use explicit rules.

Example conceptual rules:

``` text
CRITICAL indicators:
- person trapped + active immediate hazard
- severe injury + unavailable safe exit
- rapidly escalating fire/smoke
- building collapse/trapping

HIGH indicators:
- active electrical hazard + flood
- vulnerable person + dangerous environment
- fire/smoke spreading
- significant injury

MEDIUM:
- hazardous situation without confirmed immediate danger

LOW:
- informational/non-emergency situation
```

The exact rules should be tested against scenario fixtures.

Do not make risk depend solely on an LLM's arbitrary score.

------------------------------------------------------------------------

# 14. Second AI Call: Action Plan

Input to planner:

-   validated structured incident;
-   deterministic risk level;
-   detected hazards;
-   missing information;
-   selected language;
-   relevant verified services.

Output:

``` json
{
  "summary": "...",
  "immediate_actions": [
    "...",
    "...",
    "..."
  ],
  "avoid": [
    "...",
    "..."
  ],
  "missing_information": [
    "..."
  ],
  "communication_summary": "..."
}
```

The planner must not invent emergency numbers.

------------------------------------------------------------------------

# 15. Emergency Email Feature

Add two levels.

## Level A --- No account/API required

Generate a ready-to-send email.

UI:

``` text
Emergency Communication

Recipient:
[ emergency@example.org ]

Subject:
[ Emergency Assistance Request ]

Message:
[ generated message ]

[ COPY EMAIL ]
[ OPEN EMAIL CLIENT ]
```

The `mailto:` URI can open the user's email client with the subject/body
populated.

This is the simplest hackathon version.

## Level B --- Optional automated Gmail sending

Add:

``` text
[ Connect Gmail ]
```

After user authorization:

``` text
CrisisLens
    |
    v
Google OAuth
    |
    v
User grants Gmail permission
    |
    v
CrisisLens
    |
    v
Gmail API
    |
    v
Send email
```

Never collect or store the user's Gmail password.

Gmail's API supports sending messages, but authorized OAuth access is
required. Google documents the `gmail.send`/`gmail.compose` scopes and
OAuth authorization flow.

For the hackathon MVP, prefer the copy/open-email-client approach
because it has less authentication complexity.

------------------------------------------------------------------------

# 16. Generated Emergency Email

Example:

Subject:

``` text
Emergency Assistance Request — Flooding — Rawalpindi
```

Body:

``` text
Dear Emergency Services,

I am requesting assistance regarding a flooding incident.

Location:
Rawalpindi, Pakistan

Situation:
Flood water is entering our house.

People affected:
6

Vulnerable person:
One wheelchair user

Known hazards:
- Floodwater
- Active electricity
- Main route blocked

Additional information:
Water depth and safe alternate exit have not yet been confirmed.

Please advise/provide assistance as appropriate.

Thank you.
```

The user must be able to edit the generated message before sending.

Do not automatically send an AI-generated emergency email without user
confirmation.

------------------------------------------------------------------------

# 17. Email Recipient Safety

Never allow the LLM to invent an email address.

Emergency email addresses must come from the verified emergency-services
database.

If no verified email exists:

``` text
No verified emergency email is available.
Use the verified phone contact instead.
```

Do not scrape random email addresses from search results and label them
official.

------------------------------------------------------------------------

# 18. Additional High-Value Features

## A. Emergency Message Generator

Generate a short message for:

-   emergency services;
-   family;
-   neighbors;
-   building management.

Buttons:

``` text
[ Emergency Services Message ]
[ Family Message ]
[ Neighbor Message ]
```

------------------------------------------------------------------------

## B. Multilingual Mode

Support:

-   English
-   Urdu
-   Arabic
-   Spanish
-   French

AI can translate the action plan.

Emergency numbers remain unchanged.

------------------------------------------------------------------------

## C. Stress Mode

Emergency UI should reduce cognitive load.

Instead of large paragraphs:

``` text
1. MOVE TO SAFETY
2. AVOID ELECTRICITY
3. CALL 1122
```

This should be a prominent optional mode.

------------------------------------------------------------------------

## D. Offline Contact Access

Emergency contact numbers should be loaded locally from JSON/SQLite.

If Groq is unavailable:

``` text
AI analysis unavailable.

Verified emergency contacts remain available.
```

This is an important reliability feature.

------------------------------------------------------------------------

## E. Incident History

Optional local history:

``` text
Previous Incidents

2026-10-04
Flood
Rawalpindi
HIGH
```

Do not store sensitive incident details by default.

Add:

``` text
[ Delete Incident ]
```

------------------------------------------------------------------------

## F. Privacy Mode

Default:

-   no account;
-   no permanent storage;
-   no personal data required;
-   no API keys exposed to browser;
-   no incident text logged.

------------------------------------------------------------------------

## G. Confidence / Uncertainty

Display:

``` text
Assessment confidence:
Moderate

Why?
The water depth and building condition
are unknown.
```

Never display fake precision such as:

``` text
Risk = 93.7%
```

unless there is a validated statistical model.

------------------------------------------------------------------------

## H. Evidence / Source Panel

Every emergency contact should show:

``` text
Source:
Punjab Police

Last verified:
2026-XX-XX

[ View Source ]
```

This improves trust.

------------------------------------------------------------------------

## I. Location Assistance

Optional future feature:

``` text
Use my location
```

Then:

-   country;
-   region;
-   city;
-   coordinates.

Do not expose precise coordinates in generated emails unless the user
explicitly chooses to include them.

------------------------------------------------------------------------

## J. Nearby Facilities

Future feature:

-   nearby hospitals;
-   fire stations;
-   police stations;
-   shelters.

Use a verified map/place data source.

Do not call a random place "nearest emergency facility" without reliable
location data.

------------------------------------------------------------------------

# 19. Suggested UI Layout

## Desktop

``` text
+-----------------------------------------------------------+
| CRISISLENS                              🌍 Pakistan       |
+-----------------------------------------------------------+
|                                                           |
| WHAT IS HAPPENING?                                        |
| +-------------------------------------------------------+ |
| | Flood water is entering our house...                  | |
| +-------------------------------------------------------+ |
|                                                           |
| [ ANALYZE EMERGENCY ]                                    |
+-----------------------------------------------------------+

After analysis:

+----------------------+------------------------------------+
| SITUATION            | EMERGENCY SERVICES                 |
|                      |                                    |
| 🌊 FLOOD             | 🚑 Rescue 1122                    |
|                      | 1122                               |
| HIGH                 | [ CALL ] [ COPY ]                |
|                      |                                    |
| Hazards              | 👮 Police                         |
| ⚡ Electricity       | 15                                 |
| 🌊 Flood water       | [ CALL ] [ COPY ]                |
| ♿ Vulnerable person |                                    |
+----------------------+------------------------------------+

+-----------------------------------------------------------+
| IMMEDIATE ACTIONS                                         |
| 1. ...                                                     |
| 2. ...                                                     |
| 3. ...                                                     |
+-----------------------------------------------------------+

+-----------------------------------------------------------+
| EMERGENCY MESSAGE                                         |
| [ generated message ]                                     |
| [ COPY ] [ OPEN EMAIL ]                                   |
+-----------------------------------------------------------+
```

------------------------------------------------------------------------

# 20. Technology Stack

## Required

-   Python 3.11+
-   Streamlit
-   Groq/OpenAI-compatible Python SDK
-   python-dotenv
-   Pydantic
-   standard library JSON

## Optional

-   Gmail API
-   SQLite
-   geopy
-   OpenStreetMap-compatible geocoding/maps
-   weather API

Avoid adding dependencies unless they provide real value.

------------------------------------------------------------------------

# 21. requirements.txt

Initial:

``` text
streamlit
groq
python-dotenv
pydantic
```

Optional Gmail:

``` text
google-api-python-client
google-auth-httplib2
google-auth-oauthlib
```

Do not install unnecessary libraries.

------------------------------------------------------------------------

# 22. Environment Variables

`.env.example`:

``` text
GROQ_API_KEY=your_key_here
GROQ_MODEL=your_supported_model
```

Never commit:

``` text
.env
credentials.json
token.json
```

Add them to `.gitignore`.

For Streamlit deployment, store secrets in Streamlit's Secrets
configuration, not in the Git repository.

------------------------------------------------------------------------

# 23. API Design

Keep the internal service boundaries simple.

Conceptual functions:

``` python
extract_incident(text, country, region)
calculate_risk(incident)
get_emergency_services(country, region, categories)
generate_action_plan(incident, risk, services)
generate_emergency_message(incident, risk, location)
generate_email(incident, risk, services)
```

Optional future FastAPI endpoints:

``` text
POST /analyze
POST /risk
GET  /emergency-services/{country}/{region}
POST /generate-message
POST /generate-email
```

Do NOT add FastAPI in the first MVP unless the team needs a separate
frontend/backend architecture.

------------------------------------------------------------------------

# 24. Error Handling

The application must handle:

## Invalid AI JSON

``` text
AI returned invalid structured data.
Retry once.
If still invalid, show safe fallback.
```

## Groq unavailable

``` text
AI analysis is temporarily unavailable.

Verified emergency contacts are still available.
```

## Missing country

``` text
Please select your country.
```

## No verified service

``` text
No verified emergency contact is available
for this location/category.
```

## Unsupported device for calling

``` text
Your device cannot place calls from this browser.
Copy the verified number instead.
```

## Email failure

``` text
Email could not be sent.
Your generated message is still available to copy.
```

------------------------------------------------------------------------

# 25. Security Requirements

Never:

-   expose API keys in Streamlit frontend;
-   put API keys in GitHub;
-   ask users for Gmail passwords;
-   trust LLM-generated phone numbers;
-   automatically send emergency emails without confirmation;
-   automatically place calls;
-   store sensitive incident data unnecessarily.

Validate all LLM outputs.

Use allowlisted emergency-service records.

------------------------------------------------------------------------

# 26. Testing Strategy

Create fixed emergency scenarios.

## Test 1 --- Flood

Input:

``` text
Flood water is entering my house.
Electricity is on and my elderly mother is inside.
```

Expected: - flood; - electrical hazard; - vulnerable person; - high
risk; - rescue contact.

## Test 2 --- Fire

Input:

``` text
There is a kitchen fire and smoke is moving upstairs.
```

Expected: - fire; - smoke; - high/critical risk depending on additional
facts; - fire service.

## Test 3 --- Medical

Input:

``` text
My father is unconscious and not responding.
```

Expected: - medical emergency; - immediate emergency-service
recommendation; - ambulance/rescue routing; - no unsupported diagnosis.

## Test 4 --- Crime

Input:

``` text
Someone is attempting to break into our house.
```

Expected: - security/crime; - police routing.

## Test 5 --- Ambiguous

Input:

``` text
There is a strange smell in the building.
```

Expected: - ask for missing information; - do not invent a gas leak; -
do not produce a fake emergency classification.

------------------------------------------------------------------------

# 27. Hackathon Demo Scenario

Use one powerful scenario.

Input:

> "Heavy rain has flooded our street. Water is entering our house.
> Electricity is still on. My father uses a wheelchair. There are six
> people inside and the main road is blocked."

Demo flow:

1.  Select Pakistan.
2.  Select Punjab/Rawalpindi.
3.  Paste situation.
4.  Click Analyze.
5.  Show:
    -   Flood;
    -   HIGH risk;
    -   electrical hazard;
    -   vulnerable person;
    -   blocked route.
6.  Show immediate actions.
7.  Show missing information.
8.  Show verified Rescue 1122.
9.  Click `Call 1122` on a phone or demonstrate the dial action.
10. Generate emergency message.
11. Generate email.
12. Copy/open email.
13. Switch to Urdu.
14. Explain that emergency contacts come from verified data, not AI.

Do not actually call an emergency number during the demonstration.

------------------------------------------------------------------------

# 28. Winning Technical Story

The system is not:

``` text
User -> Chatbot
```

It is:

``` text
Unstructured emergency report
            |
            v
       GenAI extraction
            |
            v
    Structured incident
            |
            v
   Deterministic risk engine
            |
            v
  Verified service database
            |
            v
   GenAI communication layer
            |
            v
 Action + Contact + Communication
```

This separation is the key technical design decision.

------------------------------------------------------------------------

# 29. Development Roadmap

## Phase 1 --- Foundation

-   create GitHub repository;
-   create Streamlit app;
-   create `.env.example`;
-   connect Groq;
-   build basic UI.

## Phase 2 --- AI Extraction

-   create extraction prompt;
-   define Pydantic schema;
-   implement structured JSON output;
-   validate output;
-   create test scenarios.

## Phase 3 --- Risk Engine

-   define risk factors;
-   implement deterministic rules;
-   write unit tests;
-   connect risk engine to incident JSON.

## Phase 4 --- Emergency Database

-   create Pakistan dataset;
-   verify every number;
-   create country/service schema;
-   implement service lookup.

## Phase 5 --- Emergency UI

-   service cards;
-   call links;
-   copy buttons;
-   source information;
-   fallback behavior.

## Phase 6 --- Action Planner

-   second LLM call;
-   immediate actions;
-   avoid list;
-   missing information;
-   uncertainty.

## Phase 7 --- Communication

-   emergency message;
-   family message;
-   email draft;
-   copy;
-   `mailto:` support.

## Phase 8 --- Optional Gmail

-   Google OAuth;
-   user authorization;
-   Gmail send;
-   confirmation before sending.

## Phase 9 --- Internationalization

-   multiple countries;
-   multiple languages;
-   country-specific service mapping.

## Phase 10 --- Polish

-   mobile-first UI;
-   stress mode;
-   icons;
-   error states;
-   demo scenarios;
-   privacy information;
-   source panel.

## Phase 11 --- Deployment

-   GitHub;
-   Streamlit Cloud;
-   secrets configuration;
-   production testing;
-   mobile testing.

------------------------------------------------------------------------

# 30. MVP vs Advanced Features

## Must Have

-   Streamlit UI
-   Groq LLM
-   structured incident extraction
-   deterministic risk engine
-   Pakistan emergency contacts
-   call/dial buttons
-   action plan
-   emergency message
-   email draft
-   source verification
-   safety boundaries
-   error handling

## Should Have

-   Urdu
-   5--10 countries
-   multiple emergency categories
-   copy/share
-   stress mode
-   incident history
-   privacy mode

## Nice to Have

-   Gmail OAuth
-   location
-   nearby facilities
-   weather
-   map
-   offline/PWA-style contact access
-   voice input
-   voice output
-   SMS/WhatsApp integration where legitimately supported

------------------------------------------------------------------------

# 31. Important Feasibility Decisions

### Calling

Use user-triggered `tel:` links. A normal web application cannot
silently make a phone call. On supported mobile devices the operating
system can open the dialer; otherwise show the number/copy option.

### Email

For the MVP use:

``` text
Generate email
→ user reviews
→ Copy / Open email client
```

Automated sending is a separate OAuth integration. Gmail API supports
programmatic sending, but it requires user authorization and appropriate
OAuth scopes.

### AI

Use Groq or another compatible provider.

The project must not depend on a single model provider. Keep the AI
client behind:

``` text
backend/ai/client.py
```

so the model can be replaced later.

------------------------------------------------------------------------

# 32. Definition of Done

The MVP is complete when a user can:

1.  Select Pakistan.
2.  Enter an emergency situation.
3.  Receive structured AI analysis.
4.  Receive deterministic risk classification.
5.  See missing information.
6.  See prioritized actions.
7.  See verified emergency contacts.
8.  Click a call/dial action on a supported mobile device.
9.  Copy the number if calling is unavailable.
10. Generate an emergency message.
11. Generate an email.
12. Open/copy the email.
13. Use English/Urdu output.
14. Run the system when the AI service is temporarily unavailable and
    still access verified contacts.
15. See the source and verification date for emergency contacts.

------------------------------------------------------------------------

# 33. Final Product Positioning

### Product name

CrisisLens

### Category

Generative AI + Emergency Decision Support + Public Safety

### Target users

-   general public;
-   travelers;
-   families;
-   students;
-   communities;
-   NGOs;
-   disaster-response organizations.

### Core value

CrisisLens reduces the gap between:

``` text
"I don't know what to do."
```

and:

``` text
"Here are the immediate priorities,
here is what information is missing,
here is the verified service to contact,
and here is a clear message you can send."
```

### Critical product limitation

CrisisLens provides decision support and communication assistance. It
does not replace professional emergency services or guarantee the
correctness/safety of a recommended action.

------------------------------------------------------------------------

# 34. Authoritative Data Policy

Emergency contact records must have:

-   service name;
-   country;
-   region;
-   category;
-   number;
-   official source URL;
-   verification date;
-   optional notes;
-   active/inactive status.

Before adding a number, verify it from an authoritative source.

Initial Pakistan source: Punjab Police Emergency Helplines:
https://www.punjabpolice.gov.pk/emergency_help

The Punjab Police page currently lists Police 15, Rescue Service 1122,
Fire Brigade 16, and Edhi Main Control Room 115.

Do not copy emergency numbers from random blogs or social media.

------------------------------------------------------------------------

# 35. Instructions to the AI Coding Agent

Build this project incrementally.

Rules:

1.  Do not implement the entire project in one file.
2.  Do not hard-code emergency numbers inside UI code.
3.  Do not allow the LLM to generate phone numbers.
4.  Do not expose API keys.
5.  Do not implement silent/automatic emergency calling.
6.  Do not automatically send emergency emails without explicit user
    confirmation.
7.  Validate all LLM JSON.
8.  Use deterministic risk rules.
9.  Keep emergency contacts available independently from the LLM.
10. Write tests for risk routing and emergency-service lookup.
11. Start with Pakistan before adding international countries.
12. Do not add unnecessary dependencies.
13. Keep the application deployable on free infrastructure.
14. Make the UI mobile-friendly.
15. Include clear uncertainty and safety messaging.
16. Never claim that an action is safe when the system cannot establish
    that from the available information.
17. Do not diagnose medical conditions.
18. Do not invent missing information.
19. If no verified emergency contact exists, say so instead of guessing.
20. Every generated emergency communication must be editable before
    sending.

Start implementation in this order:

``` text
1. Repository structure
2. Emergency contact schema
3. Pakistan emergency dataset
4. Pydantic incident schema
5. Groq client
6. AI extraction
7. Risk engine
8. Service router
9. Action planner
10. Streamlit UI
11. Call buttons
12. Emergency message
13. Email draft
14. Tests
15. Mobile UI
16. Deployment
17. International countries
18. Optional Gmail OAuth
```

Do not skip directly to advanced features before the core flow works.
