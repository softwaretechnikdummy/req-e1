# Aufgabe A

Mittels airtables wollte ich die Requirements eintragen. Nach der Registrierung wurde ich sofort von einem Chatbot gefragt, was für ein Projekt ich erstellen möchte.
Folgender Prompt generierte mir einen Table:

## airtables prompt
```
These are my required attributes, can you create a table with requirements based on these attributes? Also this table should feature some kind of version control. I would suggest that their is one column called Version and one called Changelog. And then for a new revision of the same requirement a new row will be added with the same ID but incremented version value and a changelog message.

<list>  ID  Date  Author  Short description  Long description  Status  Reference to the use case  Reference to other documents  Who approves the requirement?  What is the acceptance criterion?  Priority  User Scope  Due date  Conflicts  Dependencies Comments  Version  Changelog  Category</list>
```

airtables war nicht in der Lage, den Table nachträglich mit Daten zu füllen, da mein Assasin Party-Game wohl gegen die Content-Richtlinien verstößt?
Also habe ich ChatGPT gebeten, anhand meiner Vision die Requirements-Tabelle zu befüllen:

## chatgpt prompt
```
These are my required attributes, can you create a table with requirements based on these attributes? Also this table should feature some kind of version control. I would suggest that their is one column called Version and one called Changelog. And then for a new revision of the same requirement a new row will be added with the same ID but incremented version value and a changelog message.

<list>  ID  Date  Author  Short description  Long description  Status  Reference to the use case  Reference to other documents  Who approves the requirement?  What is the acceptance criterion?  Priority  User Scope  Due date  Conflicts  Dependencies Comments  Version  Changelog  Category</list>

Can you please fill the table with the following description? For things like Date, Priority, Due Date, and other columns you can fill out based on context or fabricate since this is a demo.

I will describe the project in the next prompt for content, you can also split these requirements into more fine granulated ones if they are obviously containing multiple requirements. also these requirements should be very clearly formulated. if obvious requirements are entirely missing please also add them to the table.
Please output the table as a csv file.

- Assassin style game where each player on the party receives a target player and a target word. Objective is to make the target player say the target word during natural conversation
- GameMaster has different view and prepares before the party using desktop optimized interface. He can enter a list of people who will attend and a list of words. The app shall then create a circular graph connecting all players with random words chosen from the word list.
- Players need to be able to join the party and get thrown into a lobby where they each receive a 4-character code consisting of numbers and letters
- GameMaster on his phone can see players waiting in the lobby. He shall be able to link a lobby code to a player identity
- Once assigned players should see on their phone the target word and the target player
- When a player kills another player the killed player needs to reveal their agent badge to the killer. the killer can enter the badge to register their kill in the system
- Players should be able to redraw their word a maximum of once per whole game
- When a kill is registered the killed player will be removed from the circular graph and the killer will inherit the killed players target and word. the killed player will see a death screen with the player who killed them and the word that got them killed.
- When the last kill is registered, the game should end and all participants on their phone should see a leaderboard with kill counts.
- GameMaster needs to be able to remove players who cancelled and needs to be able to decide wether the word from the person before or from the person after is kept in the circular graph
- GameMaster needs to be able to dynamically add a player that arrives unexpectedly and insert them into the circular graph only after players that are not yet associated to a lobby code i.e. players that haven't arrived yet
- GameMaster needs to be able to change a players target word manually if needed
- GameMaster needs to reassociate player identitys to different lobby codes if someone loses connection
```

ChatGPT spuckte folgende CSV aus, die ich dann bei Airtables importieren konnte:
```
ID,Version,Date,Author,Short Description,Long Description,Status,Reference to Use Case,Reference to Other Documents,Approver,Acceptance Criterion,Priority,User Scope,Due Date,Conflicts,Dependencies,Comments,Changelog,Category
GM-001,1.0,2026-05-12,Design Team,Create Party Session,GameMaster shall be able to create a new game session before the party starts,Draft,UC-Setup-01,Game Design Doc v0.1,Product Owner,New game session can be created and saved,High,GameMaster,2026-06-01,None,None,Core feature,Initial requirement,Game Setup
GM-002,1.0,2026-05-12,Design Team,Enter Player List,GameMaster shall be able to enter and edit a list of participating players using a desktop-optimized interface,Draft,UC-Setup-02,UI Spec Desktop,Product Owner,"Player names can be added, edited, and removed",High,GameMaster,2026-06-01,None,GM-001,Desktop-first UX,Initial requirement,Game Setup
GM-003,1.0,2026-05-12,Design Team,Enter Word List,GameMaster shall be able to enter and edit a list of possible target words,Draft,UC-Setup-03,UI Spec Desktop,Product Owner,Word list persists after save,High,GameMaster,2026-06-01,None,GM-001,Support bulk paste,Initial requirement,Game Setup
GM-004,1.0,2026-05-12,Design Team,Generate Circular Assignment Graph,System shall generate a circular player assignment graph with random target words,Draft,UC-Assignment-01,Algorithm Spec,Technical Lead,"Every player receives exactly one target and one pursuer",Critical,System,2026-06-05,None,"GM-002;GM-003",No self-targeting allowed,Initial requirement,Core Gameplay
GM-005,1.0,2026-05-12,Design Team,Prevent Duplicate Target Assignment,System shall prevent a player from being assigned to themselves,Draft,UC-Assignment-02,Algorithm Spec,Technical Lead,No generated graph contains self-references,Critical,System,2026-06-05,None,GM-004,Validation required,Initial requirement,Core Gameplay
PL-001,1.0,2026-05-12,Design Team,Join Party Lobby,Players shall be able to join a game lobby from their mobile device,Draft,UC-Join-01,Mobile UX Spec,Product Owner,Player successfully enters lobby,Critical,Player,2026-06-10,None,GM-001,Mobile-first design,Initial requirement,Lobby System
PL-002,1.0,2026-05-12,Design Team,Generate Lobby Code,System shall generate a unique 4-character alphanumeric lobby code for each joining player,Draft,UC-Join-02,Backend Spec,Technical Lead,No duplicate active codes exist,High,System,2026-06-10,None,PL-001,Case-insensitive,Initial requirement,Lobby System
GM-006,1.0,2026-05-12,Design Team,View Waiting Players,GameMaster shall be able to see all unassigned lobby participants on mobile,Draft,UC-Lobby-01,Mobile GM Spec,Product Owner,Waiting users appear in real-time,High,GameMaster,2026-06-12,None,PL-001,Real-time sync required,Initial requirement,Lobby System
GM-007,1.0,2026-05-12,Design Team,Associate Lobby Code to Identity,GameMaster shall be able to connect a player identity to a lobby code,Draft,UC-Lobby-02,Backend Spec,Product Owner,Assigned player receives identity successfully,Critical,GameMaster,2026-06-12,None,GM-006,Manual verification flow,Initial requirement,Lobby System
PL-003,1.0,2026-05-12,Design Team,Display Target Player,Player shall see their assigned target player after assignment,Draft,UC-Gameplay-01,Mobile Player UX,Product Owner,Target displayed correctly,Critical,Player,2026-06-15,None,GM-004,Hide from screenshots later,Initial requirement,Gameplay
PL-004,1.0,2026-05-12,Design Team,Display Target Word,Player shall see their assigned target word after assignment,Draft,UC-Gameplay-02,Mobile Player UX,Product Owner,Word displayed correctly,Critical,Player,2026-06-15,None,GM-004,Secure visibility,Initial requirement,Gameplay
PL-005,1.0,2026-05-12,Design Team,Register Kill via Badge,Killer shall be able to register a kill by entering the victim’s badge code,Draft,UC-Kill-01,Badge System Spec,Technical Lead,Valid badge confirms kill,Critical,Player,2026-06-20,None,PL-003,Anti-cheat validation needed,Initial requirement,Kill System
PL-006,1.0,2026-05-12,Design Team,Remove Dead Player from Graph,System shall remove killed players from the circular target graph,Draft,UC-Kill-02,Gameplay Logic Spec,Technical Lead,Dead player no longer appears as active,Critical,System,2026-06-20,None,PL-005,Real-time update,Initial requirement,Kill System
PL-007,1.0,2026-05-12,Design Team,Inherit Target and Word,Killer shall inherit the target and target word of the killed player,Draft,UC-Kill-03,Gameplay Logic Spec,Technical Lead,Killer receives updated mission,Critical,Player,2026-06-20,None,PL-006,Atomic transaction required,Initial requirement,Kill System
PL-008,1.0,2026-05-12,Design Team,Display Death Screen,Eliminated players shall see who killed them and which word triggered elimination,Draft,UC-Kill-04,Mobile UX Spec,Product Owner,Death information displayed immediately,Medium,Player,2026-06-20,None,PL-005,Emotional UX important,Initial requirement,Kill System
PL-009,1.0,2026-05-12,Design Team,Allow One Word Redraw,Players shall be allowed to redraw their assigned word only once per game,Draft,UC-Gameplay-03,Gameplay Rules,Product Owner,Second redraw attempt is blocked,Medium,Player,2026-06-18,None,PL-004,Prevent abuse,Initial requirement,Gameplay
SYS-001,1.0,2026-05-12,Design Team,End Game Automatically,System shall automatically end the game when only one active player remains,Draft,UC-Endgame-01,Gameplay Logic Spec,Technical Lead,End screen appears automatically,Critical,System,2026-06-25,None,PL-006,Winner determination required,Initial requirement,Endgame
SYS-002,1.0,2026-05-12,Design Team,Display Leaderboard,All participants shall see a leaderboard with kill counts after game end,Draft,UC-Endgame-02,Mobile UX Spec,Product Owner,Leaderboard visible to all players,High,All Users,2026-06-25,None,SYS-001,Sort descending,Initial requirement,Endgame
GM-008,1.0,2026-05-12,Design Team,Remove Cancelled Player,GameMaster shall be able to remove players who cancelled participation,Draft,UC-Admin-01,Admin UX Spec,Product Owner,Removed player disappears from graph,High,GameMaster,2026-06-15,None,GM-004,Requires graph regeneration logic,Initial requirement,Administration
GM-009,1.0,2026-05-12,Design Team,Select Graph Reconnection Strategy,GameMaster shall decide whether predecessor or successor word remains after removing a player,Draft,UC-Admin-02,Gameplay Rules,Technical Lead,Selected rule applied correctly,Medium,GameMaster,2026-06-15,None,GM-008,Edge cases with 2 players,Initial requirement,Administration
GM-010,1.0,2026-05-12,Design Team,Dynamically Add Late Player,GameMaster shall be able to add late-arriving players during the lobby phase,Draft,UC-Admin-03,Backend Spec,Product Owner,Player inserted successfully,High,GameMaster,2026-06-18,None,GM-006,Only before game start,Initial requirement,Administration
GM-011,1.0,2026-05-12,Design Team,Insert Late Player Before Unassigned Users,System shall insert late players only before players without lobby association,Draft,UC-Admin-04,Gameplay Logic Spec,Technical Lead,Existing active players remain stable,Medium,System,2026-06-18,None,GM-010,Preserve fairness,Initial requirement,Administration
GM-012,1.0,2026-05-12,Design Team,Change Assigned Word,GameMaster shall be able to manually change a player's target word,Draft,UC-Admin-05,Admin UX Spec,Product Owner,Updated word visible immediately,Medium,GameMaster,2026-06-20,None,PL-004,Audit log needed,Initial requirement,Administration
GM-013,1.0,2026-05-12,Design Team,Reassociate Lobby Identity,GameMaster shall be able to reassign a player identity to another lobby code,Draft,UC-Admin-06,Backend Spec,Product Owner,Player regains access successfully,High,GameMaster,2026-06-20,None,GM-007,Recovery workflow,Initial requirement,Administration
SYS-003,1.0,2026-05-12,Design Team,Real-Time Synchronization,System shall synchronize gameplay state across all connected devices in real time,Draft,UC-System-01,Architecture Spec,Technical Lead,State updates propagate within 2 seconds,Critical,System,2026-06-25,None,All gameplay modules,WebSocket recommended,Initial requirement,System
SYS-004,1.0,2026-05-12,Design Team,Persist Game State,System shall persist game state to prevent data loss during disconnects,Draft,UC-System-02,Backend Spec,Technical Lead,Session can recover after restart,High,System,2026-06-28,None,SYS-003,Database-backed,Initial requirement,System
SEC-001,1.0,2026-05-12,Design Team,Restrict Sensitive Information,Players shall only see information relevant to their own mission,Draft,UC-Security-01,Security Spec,Technical Lead,Unauthorized information inaccessible,Critical,Player,2026-06-28,None,Authentication system,Prevent cheating,Initial requirement,Security
SEC-002,1.0,2026-05-12,Design Team,Validate Kill Ownership,System shall validate that a kill badge belongs to the reported victim,Draft,UC-Security-02,Badge Spec,Technical Lead,Invalid badge entries rejected,Critical,System,2026-06-25,None,PL-005,Anti-cheat,Initial requirement,Security
UX-001,1.0,2026-05-12,Design Team,Mobile Optimized Player Interface,Player-facing interfaces shall be optimized for mobile devices,Draft,UC-UX-01,UI Guidelines,Product Owner,Interface usable on standard smartphone sizes,High,Player,2026-06-15,None,PL-001,Responsive design,Initial requirement,UX
UX-002,1.0,2026-05-12,Design Team,Desktop Optimized GM Interface,GameMaster setup interface shall be optimized for desktop usage,Draft,UC-UX-02,UI Guidelines,Product Owner,Setup workflow usable on desktop,High,GameMaster,2026-06-10,None,GM-001,Table-oriented UI,Initial requirement,UX
```

Innerhalb von Airtables konnte ich dann noch meine Daten finetunen und die Requirements zwischeneinander referenzieren.
Die Anforderung einer Historie habe ich in diesem Tabellen-Format damit gelöst, dass die Spalte "Version" und "Changelog" existieren.
Eine neue Iteration eines bestehenden Requirements kann dann als neue Zeile hinzugefügt werden mit gleicher ID und inkrementierter Versionsnummer.

## table
[https://airtable.com/appBZLtP0Y7aSzVhD/shrmjcU1kOMuBdgGw](https://airtable.com/appBZLtP0Y7aSzVhD/shrmjcU1kOMuBdgGw)

Falls der Airtable vor der Korrektur pleite gehen sollte, habe ich den Table auch als PDF heruntergeladen (Achtung: hässlich!)
[airtable-req-downloaded.pdf](./airtable-req-downloaded.pdf)
