# Smart-Lock Access Manager

A web platform for holiday-rental managers to give guests **keyless access** to their properties through internet-connected smart locks. Managers create bookings (by hand or imported from Google Calendar), and each guest can open the right doors only during their stay. Every access attempt is logged and monitored.

> Team project (5 people, Scrum) for a course at ETSIT, Universidad Politécnica de Madrid, Feb – May 2025.
> This is my personal copy of the team repository, with the full commit history preserved and credentials removed.

## Features

- **Booking management:** create, edit and cancel guest bookings tied to specific locks and time windows.
- **Google Calendar integration:** sign in with Google (OAuth 2.0), sync bookings to the manager's calendar and bulk-import them from `.ics` files.
- **Guest access:** guests sign in and open their assigned door from the browser while the booking is active, with a simulated Bluetooth-proximity check.
- **Access monitoring:** a log of every access attempt, plus an email to the manager on each access.
- **Battery monitoring:** a scheduled job checks each lock's battery over WebSockets every 5 minutes, flags low levels and locks the door digitally when the battery is depleted.
- **Light/dark theme**, notifications and user settings.

## Architecture

```
┌────────────────────────────┐        WebSockets (Socket.IO)       ┌──────────────────────────┐
│  Spring Boot web app       │ ◄──────────────────────────────────► │  Lock simulator          │
│  Java 17 · Spring Security │        open / close / battery        │  Node.js + Express       │
│  JPA · Thymeleaf           │                                      │  React control panel     │
└──────────┬─────────────────┘                                      └──────────────────────────┘
           │ JDBC                         OAuth 2.0 · Calendar API
           ▼                                      ▼
       MySQL                              Google Calendar
```

| Layer | Stack |
|---|---|
| Backend | Java 17, Spring Boot 3.4 (Web, Security, Data JPA, Mail), Thymeleaf |
| Database | MySQL |
| Integrations | Google OAuth 2.0, Google Calendar API, iCal (`.ics`) parsing |
| Lock simulator | Node.js, Express, Socket.IO, React |
| Testing | JUnit, Selenium (end-to-end) |

## My contributions

I was the **top contributor** on the team. My main areas:

- **Google Calendar integration:** OAuth 2.0 sign-in, calendar sync and `.ics` import for bookings.
- **Booking backend and calendar view** (Spring Boot, JPA).
- **Lock simulator** (`lock-simulator/`): Node.js server and React panel with token authentication, plus its WebSocket connection to the main backend and the scheduled battery monitoring.
- **End-to-end Selenium tests** for guest access and access monitoring.
- Git workflow with feature branches and pull requests.

## Running locally

**Requirements:** Java 17, Maven (or the included wrapper), MySQL and Node.js.

1. Create a MySQL database called `isst_db`.
2. Set the secrets as environment variables (they are not stored in the repo):
   ```bash
   export SECRET_PASSWORD=<your MySQL password>
   export GOOGLE_CLIENT_SECRET=<your Google OAuth client secret>
   export MAIL_USERNAME=<Gmail address used to send alerts>
   ```
3. For Google Calendar, create an OAuth client in Google Cloud and copy `src/main/resources/credentials.example.json` to `credentials.json` with your own values.
4. Start the lock simulator:
   ```bash
   cd lock-simulator/ISST-Grupo25-Cerradura && npm install && node server.js   # http://localhost:3555
   cd ../isst-grupo25-cerradurafront && npm install && npm start             # http://localhost:3000
   ```
5. Start the web app:
   ```bash
   ./mvnw spring-boot:run
   ```

## Team

Inés Valcárcel Jiménez, Natalia Burguillo Martín, Mónica Ferrer Gómez-Cano, Rodrigo Tavares de Pina Simões and Francisco Gómez.
