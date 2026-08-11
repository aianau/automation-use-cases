## Agent 24/7 Lead Capture & Qualification
### User flow prezentare oferte in functie de criterii client
Eu ca si client nou caruia nu ii este facil sa se uite manual prin oferta de apartamente pe care le are compania in Sibiu sau Timisoara si nu vrea sa investeasca timp in asta, dau un mesaj pe WhatsApp/Email/nr de telefon in care spun:
`Salut. M-ar interesa niste apartamente rezidentiale in Sibiu si Timisoara. Aveti cumva ceva de genul in oferta voastra?`.
Agentul ar trebui sa urmeze urmatorul flow (atat in engleza cat si in romana):

```mermaid
flowchart TD
    Start(["Client trimite mesaj pe WhatsApp/Email/SMS"]) --> Input["Mesaj: Caut apartamente in Sibiu / Timisoara"]
    Input --> DB_Check{"Agent AI interogheaza<br/>Baza de Date / CRM<br/><i>(Timp raspuns &lt; 1 min)</i>"}

+
    %% Scenariu A: Exista oferte
    DB_Check -- "Exista oferte disponibile" --> Send_Offers["Agentul trimite ofertele gasite cu detalii de baza"]
    Send_Offers --> Wait1["Asteptare 5 minute"]
    Wait1 --> FollowUp1["Agent trimite Follow-up: 'Ai reusit sa te uiti peste optiuni?'"]
    
    FollowUp1 --> Client_Response1{"Raspuns Client"}
    
    Client_Response1 -- "Interesat / Detalii suplimentare" --> Qualify["Agentul aplica intrebarile de calificare:<br/>- Buget<br/>- Numar camere<br/>- Scop: locuit / investitie"]
    Qualify --> Schedule["Agentul propune vizionare / apel"]
    Schedule --> Booked{"Clientul accepta slotul?"}
    
    Booked -- Da --> CRM_Success["1. Salveaza Lead 'Calificat' in CRM<br/>2. Programeaza vizionarea in Calendar<br/>3. Notifica Agent Uman"]
    Booked -- Nu --> Nurture["Programeaza follow-up peste 3 zile"]

    Client_Response1 -- "Fara raspuns (24h)" --> ReEngage["Agentul trimite reminder automat"]
    ReEngage --> CRM_Cold["Marcheaza Lead ca 'Inactiv' in CRM"]

    %% Scenariu B: Nu exista oferte
    DB_Check -- "0 Oferte gasite" --> No_Offers["Agentul raspunde: 'Totul e vandut in Sibiu/Timisoara.<br/>Te-ar interesa alte zone sau alte criterii?'"]
    No_Offers --> Client_Response2{"Raspuns Client"}

    Client_Response2 -- "Da (Vrea alte zone/criterii)" --> Update_Criteria["Preluare noi criterii: ex. Cluj, Brasov"]
    Update_Criteria --> DB_Check

    Client_Response2 -- "Nu (Exclusiv Sibiu/Timisoara)" --> Waitlist["1. Salveaza Lead in CRM ca 'Waitlist Sibiu/Timisoara'<br/>2. Seteaza Trigger: Notificare automata la listing nou"]
    
    CRM_Success --> End(["Fin Flow"])
    CRM_Cold --> End
    Nurture --> End
    Waitlist --> End
```



### User flow preluare si programare client pentru meeting
Eu ca si client care am gasit o oferta printre cele prezentate pe site-ul web, dau click pe butonul `Schedule a meeting`. 

```mermaid
flowchart TD
    Start([Client dă click pe 'Schedule a meeting' pe site]) --> Widget[Deschidere Pop-up / Widget Conversațional AI]
    
    Widget --> Type_Select{Alegere tip întâlnire}
    Type_Select -- "Vizionare Fizică" --> Set_Type_Physical[Tip: Locație Proprietate]
    Type_Select -- "Apel Online / Video" --> Set_Type_Online[Tip: Google Meet / Zoom]
    
    Set_Type_Physical --> Calendar_Fetch[Agentul AI interoghează Calendarul Agentului Uman]
    Set_Type_Online --> Calendar_Fetch
    
    Calendar_Fetch --> Slot_Select[Clientul alege Data și Ora disponibile]
    Slot_Select --> Form_Contact[Clientul introduce: Nume, Email, Telefon]
    
    Form_Contact --> Confirm_Booking{Procesare Rezervare}
    
    Confirm_Booking --> System_Actions[1. Blocare slot în Calendar<br/>2. Salvare / Update Lead în CRM<br/>3. Trimite confirmare WhatsApp + Email conținând:<br/>   - Detalii întâlnire / Link Maps / Link Meet<br/>   - Fișier calendar .ics]
    
    System_Actions --> Notif_Agent[Notificare Agent Uman: 'Meeting Nou Programat']
    
    %% Flow Reminder
    System_Actions --> Wait_Reminder[Așteptare: Cu 24h & 2h înainte de întâlnire]
    Wait_Reminder --> Send_Reminder[Trimite Reminder Automat pe WhatsApp]
    
    Send_Reminder --> Client_Action{Răspuns / Acțiune Client}
    
    Client_Action -- "Confirmă" --> Proceed_Meeting[Întâlnirea are loc]
    Client_Action -- "Reprogramează" --> Slot_Select
    Client_Action -- "Anulează" --> Cancel_CRM[Update CRM: 'Meeting Anulat' + Slot Eliberat]

    %% Flow Post Meeting
    Proceed_Meeting --> Post_Meeting{Status introdus de Agent Uman după întâlnire}
    
    Post_Meeting -- "Purtată cu succes" --> Post_Success[AI trimite WhatsApp: 'Mulțumim! Cum ți s-a părut proprietatea?' + Pasi Următori]
    Post_Meeting -- "No-Show (Nu a apărut)" --> Post_NoShow[AI trimite WhatsApp: 'Am văzut că nu ai reușit să ajungi. Dorești să reprogramăm?']
    
    Post_NoShow --> Slot_Select
    Post_Success --> End([Fin Flow])
    Cancel_CRM --> End

```


### User flow validare client pentru meeting dpdv financiar
Eu ca si client care am gasit o oferta printre cele prezentate pe site-ul web dar e posibil sa nu am finantarea necesara, asa ca doresc o discutie cu un agent pentru a verifica daca ma incadrez pentru un credit ipotecar.

```mermaid
flowchart TD
    %% SUBGRAPH 1: INIȚIERE ȘI COLECTARE DATE
    subgraph PHASE1 [1. Captare & Intake Interactiv]
        A([Client solicită info / credit pe WhatsApp/Web]) --> B[AI inițiază conversația: 'Să verificăm gratuit încadrarea ta financiară']
        B --> C[Colectare Date Cheie:]
        C --> C1[1. Venit lunar net total familie / co-plătitor]
        C --> C2[2. Rate / Carduri de credit / Descoperit de cont existente]
        C --> C3[3. Număr persoane în întreținere copii/copil]
        C --> C4[4. Avans disponibil EUR/RON & Sursă fonduri]
        C --> C5[5. Vârstă & Tip contract muncă determinat/nedeterminat]
    end

    %% SUBGRAPH 2: MOTORUL DE CALCUL AI (ENGINE)
    subgraph PHASE2 [2. System Logic & Stress Test BNR]
        C1 & C2 & C3 & C4 & C5 --> D[AI Engine: Calcul Venit Disponibil]
        D --> D1[Calcul Venit Net Ajustat = Venit Net - Minim Subzistență Familie]
        D1 --> D2["Calcul Capacitate Maximă Lunară de Plată = Venit Net * 40%"]
        D2 --> D3[Calcul Rată Liberă Maximă = Capacitate Maximă - Rate Existente]
        
        D3 --> E{Rată Liberă > 0?}
        
        E -- Nu --> F[Status: Ineligibil Financiar în Prezent]
        
        E -- Da --> G[Simulare valoarea maximă a creditului pe 30 ani]
        G --> H["Simulare Stress-Test: IRCC + 2%"]
        H --> I[Adăugare Avans Client = Buget Total Maxim Achiziție]
    end

    %% SUBGRAPH 3: EVALUARE POTRIVIRE PROPRIETATE
    subgraph PHASE3 [3. Matchmaking cu Oferta]
        I --> J{Buget Total Maxim >= Preț Proprietate Dorită?}
        
        %% SCENARIU A: ELIGIBIL COMPLET
        J -- "Da (Aprobat)" --> K[GENERARE RAPORT ELIGIBILITATE PDF]
        K --> K1[1. Afișează: Rata estimată, Suma maximă credit, Avans necesar]
        K1 --> K2[2. Trimite Raport pe WhatsApp + Email]
        K2 --> L[AI Programează Vizionare Proprietate + Sincronizare Calendar Agent]
        L --> M[System Action: Salvează Lead 'Calificat Financiar' în CRM]
        M --> N[Handover: Notifică Brokerul Intern de Credite + Trimite Fișa de Calcul]

        %% SCENARIU B: BUGET INSUFICIENT PENTRU PROPRIETATEA ALEASĂ
        J -- "Nu (Gap de Finanțare)" --> O[AI calculează diferența de avans sau venit necesar]
        O --> P{Clientul dorește alternative?}
        
        P -- "Da (Caută în buget)" --> Q[AI interoghează CRM: Caută apartamente similare în limita Bugetului Max Calculat]
        Q --> Q1[Trimite Top 3 Proprietăți Potrivite pe WhatsApp]
        Q1 --> L

        P -- "Nu (Vrea doar acea proprietate)" --> R[AI propune Soluții de Structurare:]
        R --> R1[- Adăugare co-plătitor părinți/soț]
        R --> R2[- Refinanțare/Închidere carduri de credit existente pentru creștere plafon]
        R1 & R2 --> N
    end

    %% SCENARIU C: NEELIGIBIL
    F --> S[AI oferă Plan de Screening Financiar:]
    S --> S1[1. Sfaturi închidere carduri de cumpărături / overdraft]
    S --> S2[2. Setează Follow-up automat peste 6 luni în CRM]

    %% END POINTS
    N --> END1([Fin Flow: Lead Pre-Aprobat Preluat de Broker Uman])
    S2 --> END2([Fin Flow: Lead Pus în Nurturing])
```


Acest flow poate fi integrat si in [User flow prezentare oferte in functie de criterii client](#user-flow-prezentare-oferte-in-functie-de-criterii-client)


Astfel de flowuri se pot integra si automatiza in n8n, platforma care e open source si se poate face hosting in cloud cu costuri scazute. 

Alte zone cu un potencial mare de automatizare, este zona de post-tranzactionala, unde se pot automatiza task-uri repetitive cum ar fi: 
1. Trimiterea de mesaje catre client/agenti in diferite etape ale procesului de achizitie
2. Generarea de rapoarte si documente necesare in procesul de a chizitie
3. Preluare de date pentru aplicarea unui credit ipotecar
4. Follow-up cu clientii post achizitie

