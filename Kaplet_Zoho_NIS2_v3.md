# ·KAPLET – NIS 2 Assessment System v3
## Guida Zoho completa — allineata al tool v3
### Networking · Videosorveglianza & VMS · Controllo Accessi · Audio Video

---

## ARCHITETTURA DEL SISTEMA

```
TABLET / LAPTOP COMMERCIALE (in visita cliente)
          │
          ▼
  ┌──────────────────────┐
  │     ZOHO FORMS       │  ← NIS 2 Assessment (5 sezioni, ~40 domande)
  │  NIS 2 – Kaplet v3   │     Logica condizionale per i 3 verticali
  └──────────┬───────────┘
             │ Submit → trigger automatico
             ▼
  ┌────────────────────────────────────────────────────────────┐
  │                      ZOHO FLOW                             │
  │                                                            │
  │  1.  Cerca / crea Account nel CRM                          │
  │  2.  Calcola classificazione NIS 2 (Deluge)                │
  │  3.  Calcola score gap per verticale (Deluge)              │
  │  4.  Determina pacchetto consigliato                       │
  │  5.  Crea Deal nella pipeline NIS 2                        │
  │  6.  Genera PDF report (Zoho Writer template)              │
  │  7.  Allega PDF ad Account e Deal                          │
  │  8.  Invia email al cliente con report                     │
  │  9.  Notifica team su Zoho Cliq                            │
  │  10. Crea attività follow-up nel CRM                       │
  └────────────────────────────────────────────────────────────┘
             │
             ▼
  ┌──────────┐  ┌───────────────┐  ┌──────────────────┐
  │ ZOHO CRM │  │ ZOHO WRITER   │  │ ZOHO ANALYTICS   │
  │ Account  │  │ PDF Report    │  │ Dashboard mgmt   │
  │ Deal NIS2│  │ branded Kaplet│  │ Pipeline 3 vert. │
  └──────────┘  └───────────────┘  └──────────────────┘
```

---

## PARTE 1 — ZOHO CRM: CAMPI PERSONALIZZATI

### 1.1 Modulo Account — campi da aggiungere

**Percorso:** CRM → Impostazioni → Personalizzazione → Moduli e campi → Account

| Nome campo API | Label visualizzata | Tipo | Note |
|---|---|---|---|
| NIS2_Classificazione | Classificazione NIS 2 | Picklist | Soggetto Essenziale · Soggetto Importante · Da verificare · Non soggetto a NIS 2 |
| NIS2_Data_Assessment | Data assessment NIS 2 | Data | |
| NIS2_Commerciale | Commerciale assessment | Testo | |
| NIS2_Settore | Settore di attività | Testo | |
| NIS2_Dipendenti | N° dipendenti | Picklist | < 10 · 10-49 · 50-249 · 250+ |
| NIS2_Score_Crit | Gap critici totali | Numero intero | Calcolato da Flow |
| NIS2_Score_Warn | Gap attenzione totali | Numero intero | Calcolato da Flow |
| NIS2_Score_Net | Score networking | Numero intero | |
| NIS2_Score_VMS | Score VMS / security | Numero intero | |
| NIS2_Score_ACS | Score controllo accessi | Numero intero | |
| NIS2_Score_AV | Score audio video | Numero intero | |
| NIS2_Score_Gov | Score governance | Numero intero | Include formazione, crittografia, supply chain, CDA |
| NIS2_Gap_Riepilogo | Gap principali | Testo area | Top 5 gap critici — testo automatico |
| NIS2_Pacchetto | Pacchetto consigliato | Picklist | NIS 2 Starter · NIS 2 Essential · NIS 2 Complete · NIS 2 Managed |
| NIS2_Has_VMS | Sistema VMS presente | Checkbox | |
| NIS2_Has_ACS | Sistema ACS presente | Checkbox | |
| NIS2_PDF_Report | Report PDF NIS 2 | Allegato | |

### 1.2 Modulo Deal — campi da aggiungere

| Nome campo API | Label | Tipo |
|---|---|---|
| NIS2_Pacchetto | Pacchetto consigliato | Picklist: Starter · Essential · Complete · Managed |
| NIS2_Gap_Critici | Gap critici rilevati | Numero intero |
| NIS2_Classificazione | Classificazione NIS 2 | Picklist (stessa di Account) |
| NIS2_Verticali | Verticali con gap | Testo |

### 1.3 Pipeline Deal NIS 2

**Percorso:** CRM → Impostazioni → Pipeline → Nuova pipeline
**Nome:** `NIS 2 – Kaplet`

| Fase | Probabilità |
|---|---|
| Assessment completato | 10% |
| Report presentato al cliente | 25% |
| Proposta tecnica inviata | 40% |
| Trattativa in corso | 65% |
| Contratto firmato | 90% |
| Chiuso — vinto ✓ | 100% |
| Chiuso — perso ✗ | 0% |

---

## PARTE 2 — ZOHO FORMS: STRUTTURA COMPLETA v3

### 2.1 Crea il form

**Zoho Forms → Nuovo → Form vuoto**
Nome: `NIS 2 Assessment – Kaplet`
Tema: sfondo `#000000`, colore primario `#3DED7A`

---

### 2.2 Sezioni e campi — allineati al tool v3

#### SEZIONE 1 – Anagrafica cliente

| Campo | Tipo | Obbligatorio |
|---|---|---|
| Nome azienda | Testo | ✓ |
| Email referente cliente | Email | ✓ (per invio PDF automatico) |
| Nome referente cliente | Testo | |
| Commerciale Kaplet | Dropdown (lista nomi) | ✓ |
| Settore di attività | Dropdown | ✓ — vedi lista completa sotto |
| Numero dipendenti | Radio | ✓ |
| Fatturato annuo | Radio | |
| Note commerciale | Testo area | |

**Opzioni "Numero dipendenti":**
`Meno di 10 — Microimpresa` · `10 – 49 — Piccola impresa` · `50 – 249 — Media impresa` · `250 o più — Grande impresa`

**Opzioni "Fatturato annuo":**
`Meno di 10 milioni €` · `10 – 50 milioni €` · `Oltre 50 milioni €` · `Non disponibile`

**Lista settori dropdown completa:**
```
── ALLEGATO I – Soggetti Essenziali ──
Energia (elettricità, gas, idrogeno, petrolio, teleriscaldamento)
Trasporti (aereo, ferroviario, marittimo, stradale)
Banche e infrastrutture mercati finanziari
Sanità (ospedali, laboratori, farmaceutica, dispositivi medici)
Acqua potabile
Acque reflue
Infrastrutture digitali (datacenter, cloud, CDN, IXP, DNS, TLD)
Gestione servizi ICT B2B (MSP / MSSP)
Spazio
Pubblica Amministrazione Centrale
── ALLEGATO II – Soggetti Importanti ──
Servizi postali e di corriere
Gestione rifiuti
Produzione chimica
Produzione e distribuzione alimentare
Fabbricazione dispositivi medici e diagnostici
Fabbricazione computer, elettronica, ottica
Fabbricazione macchinari elettrici
Fabbricazione autoveicoli e mezzi di trasporto
Fornitori servizi digitali (marketplace, motori ricerca, social)
Ricerca e università
Pubblica Amministrazione Locale
── ALTRI SETTORI ──
Retail / Commercio
Immobiliare / Strutture ricettive / Hospitality
Costruzioni / Impiantistica
Altro settore
```

---

#### SEZIONE 2 – Networking

| Campo | Opzioni |
|---|---|
| Firewall perimetrale | Nessuno / Firewall base senza IPS / NGFW con IPS-IDS / Managed firewall |
| Segmentazione VLAN | Rete piatta / VLAN parziali / Segmentata completa / Micro-segmentazione |
| Accesso remoto | RDP-VNC esposto / VPN senza MFA / VPN con MFA / ZTNA Zero Trust |
| Inventario asset di rete | Nessuno / Manuale non aggiornato / Documentato aggiornato / Discovery automatico |
| Logging e monitoraggio rete | Nessun log / Log locali < 12 mesi / Log centralizzati 12+ mesi / SIEM con alerting |
| Patch management infrastruttura | Nessun processo / Manuali sporadici / Patch cycle pianificato / Vuln scan + auto |

---

#### SEZIONE 3 – Videosorveglianza & VMS

| Campo | Opzioni |
|---|---|
| Sistema VMS presente? | Sì / No |
| [SE SÌ] Vendor VMS | Checkbox: Axis · Avigilon · Bosch · Pelco · Milestone · Genetec · Hanwha · Hikvision · Dahua · Altro |
| [SE SÌ] Tipologia dispositivi | Checkbox: Telecamere IP · PTZ · NVR/DVR · Encoder · Decoder/videowall · AI analytics · Termiche/ANPR |
| [SE SÌ] N° telecamere | < 20 / 20-100 / Oltre 100 |
| [SE SÌ] Firmware dispositivi | Non monitorato / Non aggiornato > 12 mesi / Periodico / Automatico |
| [SE SÌ] Credenziali VMS e dispositivi | Default admin-admin / Password condivise / Account nominali senza MFA / Account nominali + MFA |
| [SE SÌ] Accesso remoto al VMS | Port forwarding diretto / Solo VPN / Cloud vendor / Non accessibile |
| [SE SÌ] Accesso tecnici manutentori | Non controllato / Credenziali condivise / Tracciato nominale / Just-in-time |
| [SE SÌ] Log accessi VMS | Nessuno / < 90 giorni / 12+ mesi / Centralizzati |
| [SE SÌ] Posizione server VMS/NVR | Area non protetta / Armadio chiuso / Rack room CED / Cloud hosted |

⚙️ **Logica condizionale:** i campi [SE SÌ] compaiono solo se "Sistema VMS presente?" = "Sì"

---

#### SEZIONE 4 – Controllo Accessi (ACS)

| Campo | Opzioni |
|---|---|
| Sistema ACS presente? | Sì / No — solo chiavi meccaniche |
| [SE SÌ] Tecnologia ACS | Checkbox: Badge RFID · PIN tastierino · Biometria · Badge mobile · Tornelli · Videocitofonia IP · Elettroserrature |
| [SE SÌ] Gestione badge / offboarding | Nessuna policy / Revoca manuale irregolare / Procedura definita / Automatizzato con HR-AD |
| [SE SÌ] Credenziali software ACS | Default / Account nominali senza MFA / Account nominali + MFA / RBAC + MFA + log |
| [SE SÌ] Accesso remoto ACS | Esposto su internet / Solo VPN / Cloud vendor / Non accessibile |
| [SE SÌ] Firmware centraline e lettori | Non monitorato / Non aggiornato / Periodico / Gestito da fornitore con SLA |
| [SE SÌ] Integrazione ACS ↔ VMS | Nessuna / Parziale / Completa con correlazione / Con AI analytics / PSIM |
| [SE SÌ] Log eventi ACS | Nessuno / Breve retention / 12+ mesi / Centralizzati nel SIEM |

⚙️ **Logica condizionale:** i campi [SE SÌ] compaiono solo se "Sistema ACS presente?" = "Sì"

---

#### SEZIONE 5 – Audio Video, UC e Governance

**5A — Audio Video & Unified Communication**

| Campo | Opzioni |
|---|---|
| Sistemi AV presenti | Checkbox: AV over IP · Digital Signage · Videoconferenza (Cisco, Poly, Neat…) · Controllo sala (Crestron, AMX…) · KVM remoto · Videowall · Teams/Zoom Rooms · Nessuno |
| Credenziali device AV / VC | Default mai cambiate / Condivise / Nominali senza MFA / Nominali + MFA gestiti |
| Firmware dispositivi AV / VC | Non monitorato / Non aggiornato / Periodico / Patch cycle documentato |
| Rete dispositivi AV | Stessa LAN PC / Parzialmente separata / VLAN AV dedicata / VLAN + ACL |
| Accesso remoto manutenzione AV | Aperto / VPN aziendale / Tool gestito con log / Solo on-site |
| Cifratura comunicazioni VC | Nessuna / Presente non verificata / End-to-end verificata / Non applicabile |

**5B — Governance e Processi** *(obbligatori per tutti i verticali)*

| Campo | Articolo NIS 2 | Opzioni |
|---|---|---|
| Backup e disaster recovery | Art. 24 lett. c | Nessuno / Solo locale mai testato / Offsite-cloud test sporadici / DR plan testato |
| Gestione incidenti e CSIRT | Art. 25-26 | Nessuna procedura / Informale / IRP documentato / IRP + referente CSIRT designato |
| **Formazione e awareness** | **Art. 24 lett. g** | Nessuna / Occasionale / Piano annuale strutturato / Continua con phishing test |
| **Crittografia dati** | **Art. 24 lett. h** | Nessuna / Solo parziale / Transito cifrato, riposo no / Completa — transito e riposo |
| **Valutazione fornitori ICT** | **Art. 24 lett. d** | Nessuna verifica / Solo clausole contrattuali / Questionario sicurezza / Assessment periodico |
| **Responsabilità CDA** | **Art. 23** | CDA non informato / Informato senza piano / Piano approvato formalmente / Supervisione attiva |
| Note aggiuntive | | Testo libero |

> I 4 campi in grassetto sono quelli aggiunti nella v3 — coprono gli obblighi NIS 2 che mancavano nella versione precedente.

---

### 2.3 Impostazioni form

```
Impostazioni → Generale:
  Messaggio post-invio:
  "Assessment completato. Il report NIS 2 viene generato
  automaticamente e inviato al cliente entro 5 minuti."

Impostazioni → Notifiche:
  Notifica email al commerciale: SÌ — conferma di invio

Impostazioni → Integrazioni:
  Zoho Flow: ATTIVA (webhook automatico al submit)
  Zoho CRM: NON configurare qui — tutto gestito da Flow
```

---

## PARTE 3 — ZOHO FLOW: FLUSSO COMPLETO CON CODICE DELUGE v3

**Trigger:** Zoho Forms → Nuova risposta → `NIS 2 Assessment – Kaplet`

---

### STEP 1 — Cerca o crea Account nel CRM

```
Azione: Zoho CRM → Cerca record → Modulo: Account
Condizione: "Account Name" contiene [Forms.nome_azienda]

SE trovato → usa l'ID esistente
SE non trovato → Zoho CRM → Crea Account con:
  Account Name      = [Forms.nome_azienda]
  NIS2_Settore      = [Forms.settore]
  NIS2_Dipendenti   = [Forms.numero_dipendenti]
  NIS2_Commerciale  = [Forms.commerciale_kaplet]
  NIS2_Data_Assessment = today()
  NIS2_Has_VMS      = ([Forms.vms_presente] == "Sì")
  NIS2_Has_ACS      = ([Forms.acs_presente] == "Sì")
```

---

### STEP 2 — Funzione Deluge: Classificazione NIS 2

```deluge
// ── classifica_nis2 ────────────────────────────────────────────────────
// Input:  settore (string), dipendenti (string), fatturato (string)
// Output: mappa {classificazione, descrizione, sanzione}

void classifica_nis2(string settore, string dipendenti, string fatturato)
{
  list essenziali = list();
  essenziali.add("Energia (elettricità, gas, idrogeno, petrolio, teleriscaldamento)");
  essenziali.add("Trasporti (aereo, ferroviario, marittimo, stradale)");
  essenziali.add("Banche e infrastrutture mercati finanziari");
  essenziali.add("Sanità (ospedali, laboratori, farmaceutica, dispositivi medici)");
  essenziali.add("Acqua potabile");
  essenziali.add("Acque reflue");
  essenziali.add("Infrastrutture digitali (datacenter, cloud, CDN, IXP, DNS, TLD)");
  essenziali.add("Gestione servizi ICT B2B (MSP / MSSP)");
  essenziali.add("Spazio");
  essenziali.add("Pubblica Amministrazione Centrale");

  list importanti = list();
  importanti.add("Servizi postali e di corriere");
  importanti.add("Gestione rifiuti");
  importanti.add("Produzione chimica");
  importanti.add("Produzione e distribuzione alimentare");
  importanti.add("Fabbricazione dispositivi medici e diagnostici");
  importanti.add("Fabbricazione computer, elettronica, ottica");
  importanti.add("Fabbricazione macchinari elettrici");
  importanti.add("Fabbricazione autoveicoli e mezzi di trasporto");
  importanti.add("Fornitori servizi digitali (marketplace, motori ricerca, social)");
  importanti.add("Ricerca e università");
  importanti.add("Pubblica Amministrazione Locale");

  bool media_grande = (dipendenti == "50 – 249 — Media impresa" ||
                       dipendenti == "250 o più — Grande impresa");
  bool alto_fatturato = (fatturato == "Oltre 50 milioni €");

  string classificazione = "";
  string descrizione = "";
  string sanzione = "";

  if (essenziali.contains(settore) && (media_grande || alto_fatturato))
  {
    classificazione = "Soggetto Essenziale";
    descrizione = "Allegato I D.Lgs. 138/2024. Obblighi massimi: 43 misure ACN " +
                  "(det. 164179 del 14/04/2025), notifica CSIRT entro 24h dal " +
                  "1° gennaio 2026, responsabilità diretta organi direttivi (Art. 23).";
    sanzione = "Fino a 10.000.000 € o 2% del fatturato globale annuo";
  }
  else if (importanti.contains(settore) && (media_grande || alto_fatturato))
  {
    classificazione = "Soggetto Importante";
    descrizione = "Allegato II D.Lgs. 138/2024. Obblighi: 37 misure ACN, notifica " +
                  "CSIRT Italia, vigilanza semplificata. Registrazione ACN obbligatoria.";
    sanzione = "Fino a 7.000.000 € o 1,4% del fatturato globale annuo";
  }
  else if (essenziali.contains(settore) || importanti.contains(settore))
  {
    classificazione = "Da verificare";
    descrizione = "Il settore rientra negli allegati NIS 2 ma la dimensione " +
                  "potrebbe escludere l'azienda. ACN può comunque qualificarla. " +
                  "Registrazione sulla piattaforma ACN consigliata.";
    sanzione = "Da determinare dopo categorizzazione ACN (aprile 2026)";
  }
  else
  {
    classificazione = "Non soggetto a NIS 2";
    descrizione = "Il settore non rientra negli allegati NIS 2. Non ci sono obblighi " +
                  "diretti. Se l'azienda è fornitrice di soggetti NIS 2, potrebbe " +
                  "ricevere richieste di compliance come parte della loro supply chain.";
    sanzione = "Non applicabile";
  }

  map risultato = map();
  risultato.put("classificazione", classificazione);
  risultato.put("descrizione", descrizione);
  risultato.put("sanzione", sanzione);
  return risultato;
}
```

---

### STEP 3 — Funzione Deluge: Score Gap v3 (con i 4 nuovi ambiti)

```deluge
// ── calcola_score_gap_v3 ────────────────────────────────────────────────
// Calcola gap per tutti i verticali inclusi i 4 nuovi ambiti NIS 2
// Input:  mappa con tutti i valori del form
// Output: mappa con score per verticale, gap critici e pacchetto consigliato

void calcola_score_gap_v3(map f)
{
  int c_net = 0; int w_net = 0;
  int c_vms = 0; int w_vms = 0;
  int c_acs = 0; int w_acs = 0;
  int c_av  = 0; int w_av  = 0;
  int c_gov = 0; int w_gov = 0;

  list gap_list = list();

  // ── NETWORKING ────────────────────────────────────────────────────────
  string fw = f.get("firewall");
  if (fw == "Nessuno")
    { c_net = c_net+1; gap_list.add("RETE: Nessun firewall perimetrale — Art. 24"); }
  else if (fw == "Firewall base senza IPS")
    { w_net = w_net+1; }

  string vlan = f.get("vlan");
  if (vlan == "Rete piatta")
    { c_net = c_net+1; gap_list.add("RETE: Rete piatta — nessuna segmentazione VLAN"); }
  else if (vlan == "VLAN parziali")
    { w_net = w_net+1; }

  string remote = f.get("accesso_remoto");
  if (remote == "RDP-VNC esposto")
    { c_net = c_net+1; gap_list.add("RETE: RDP/VNC esposto su internet — URGENTE"); }
  else if (remote == "VPN senza MFA")
    { c_net = c_net+1; gap_list.add("RETE: VPN senza MFA — Art. 24 lett. i"); }

  string logging = f.get("logging");
  if (logging == "Nessun log")
    { c_net = c_net+1; gap_list.add("RETE: Nessun logging — notifica CSIRT impossibile"); }
  else if (logging == "Log locali < 12 mesi")
    { w_net = w_net+1; }

  string inv = f.get("inventario");
  if (inv == "Nessuno")
    { c_net = c_net+1; gap_list.add("RETE: Nessun inventario asset"); }
  else if (inv == "Manuale non aggiornato")
    { w_net = w_net+1; }

  string patch_n = f.get("patch_rete");
  if (patch_n == "Nessun processo")
    { c_net = c_net+1; gap_list.add("RETE: Nessun patch management infrastruttura"); }
  else if (patch_n == "Manuali sporadici")
    { w_net = w_net+1; }

  // ── VMS / VIDEOSORVEGLIANZA ───────────────────────────────────────────
  if (f.get("vms_presente") == "Sì")
  {
    string firm = f.get("firmware_vms");
    if (firm == "Non monitorato" || firm == "Non aggiornato > 12 mesi")
      { c_vms = c_vms+1; gap_list.add("VMS: Firmware non aggiornato — CVE pubbliche"); }

    string vcreds = f.get("credenziali_vms");
    if (vcreds == "Default admin-admin")
      { c_vms = c_vms+1; gap_list.add("VMS: Credenziali default — GAP #1 da correggere"); }
    else if (vcreds == "Password condivise")
      { w_vms = w_vms+1; }

    string vexp = f.get("accesso_remoto_vms");
    if (vexp == "Port forwarding diretto")
      { c_vms = c_vms+1; gap_list.add("VMS: Esposto su internet con port forwarding"); }

    string vtech = f.get("accesso_tecnici_vms");
    if (vtech == "Non controllato")
      { c_vms = c_vms+1; gap_list.add("VMS: Accesso tecnici non controllato — supply chain"); }
    else if (vtech == "Credenziali condivise")
      { w_vms = w_vms+1; }

    string vlog = f.get("log_vms");
    if (vlog == "Nessuno" || vlog == "< 90 giorni")
      { w_vms = w_vms+1; }

    string vloc = f.get("posizione_server_vms");
    if (vloc == "Area non protetta")
      { w_vms = w_vms+1; }
  }

  // ── CONTROLLO ACCESSI ─────────────────────────────────────────────────
  if (f.get("acs_presente") == "Sì")
  {
    string acreds = f.get("gestione_badge");
    if (acreds == "Nessuna policy")
      { c_acs = c_acs+1; gap_list.add("ACS: Badge mai revocati — ex dipendenti con accesso fisico"); }
    else if (acreds == "Revoca manuale irregolare")
      { w_acs = w_acs+1; }

    string asoft = f.get("credenziali_acs");
    if (asoft == "Default")
      { c_acs = c_acs+1; gap_list.add("ACS: Credenziali default sul software ACS"); }
    else if (asoft == "Account nominali senza MFA")
      { w_acs = w_acs+1; }

    string aexp = f.get("accesso_remoto_acs");
    if (aexp == "Esposto su internet")
      { c_acs = c_acs+1; gap_list.add("ACS: Software ACS esposto — apertura varchi da remoto"); }

    string afirm = f.get("firmware_acs");
    if (afirm == "Non monitorato" || afirm == "Non aggiornato")
      { w_acs = w_acs+1; }

    string alog = f.get("log_acs");
    if (alog == "Nessuno" || alog == "Breve retention")
      { w_acs = w_acs+1; }
  }

  // ── AUDIO VIDEO ───────────────────────────────────────────────────────
  string avcreds = f.get("credenziali_av");
  if (avcreds == "Default mai cambiate")
    { c_av = c_av+1; gap_list.add("AV: Credenziali default su device AV/VC"); }
  else if (avcreds == "Condivise")
    { w_av = w_av+1; }

  string avfirm = f.get("firmware_av");
  if (avfirm == "Non monitorato" || avfirm == "Non aggiornato")
    { w_av = w_av+1; }

  string avnet = f.get("rete_av");
  if (avnet == "Stessa LAN PC")
    { w_av = w_av+1; }

  string avremote = f.get("accesso_remoto_av");
  if (avremote == "Aperto")
    { c_av = c_av+1; gap_list.add("AV: Accesso remoto manutenzione AV non controllato"); }

  // ── GOVERNANCE E PROCESSI (inclusi 4 nuovi ambiti NIS 2) ──────────────

  // Backup — Art. 24 lett. c
  string backup = f.get("backup_dr");
  if (backup == "Nessuno")
    { c_gov = c_gov+1; gap_list.add("GOV: Nessun backup — Art. 24 lett. c"); }
  else if (backup == "Solo locale mai testato")
    { w_gov = w_gov+1; }

  // Gestione incidenti — Art. 25-26
  string incident = f.get("gestione_incidenti");
  if (incident == "Nessuna procedura")
    { c_gov = c_gov+1; gap_list.add("GOV: Nessun IRP — notifica CSIRT entro 24h impossibile"); }
  else if (incident == "Informale")
    { w_gov = w_gov+1; }

  // ── NUOVO: Formazione — Art. 24 lett. g ──────────────────────────────
  string training = f.get("formazione");
  if (training == "Nessuna")
    { w_gov = w_gov+1; gap_list.add("GOV: Nessuna formazione cybersecurity — Art. 24 lett. g"); }
  else if (training == "Occasionale")
    { w_gov = w_gov+1; }

  // ── NUOVO: Crittografia — Art. 24 lett. h ────────────────────────────
  string crypto = f.get("crittografia");
  if (crypto == "Nessuna")
    { c_gov = c_gov+1; gap_list.add("GOV: Nessuna cifratura — Art. 24 lett. h"); }
  else if (crypto == "Solo parziale" || crypto == "Transito cifrato, riposo no")
    { w_gov = w_gov+1; }

  // ── NUOVO: Supply chain fornitori — Art. 24 lett. d ──────────────────
  string supply = f.get("supply_chain");
  if (supply == "Nessuna verifica")
    { w_gov = w_gov+1; gap_list.add("GOV: Fornitori ICT non valutati — Art. 24 lett. d"); }
  else if (supply == "Solo clausole contrattuali")
    { w_gov = w_gov+1; }

  // ── NUOVO: Responsabilità CDA — Art. 23 ──────────────────────────────
  string cda = f.get("responsabilita_cda");
  if (cda == "CDA non informato")
    { c_gov = c_gov+1; gap_list.add("GOV: CDA non informato — responsabilità personale Art. 23"); }
  else if (cda == "Informato senza piano")
    { w_gov = w_gov+1; }

  // ── TOTALI E PACCHETTO ────────────────────────────────────────────────
  int tot_crit = c_net + c_vms + c_acs + c_av + c_gov;
  int tot_warn = w_net + w_vms + w_acs + w_av + w_gov;

  string pacchetto = "";
  if (tot_crit >= 6)
    { pacchetto = "NIS 2 Complete"; }
  else if (tot_crit >= 3)
    { pacchetto = "NIS 2 Essential"; }
  else if (tot_crit >= 1)
    { pacchetto = "NIS 2 Starter"; }
  else
    { pacchetto = "NIS 2 Managed"; }

  // Riepilogo top 5 gap
  string gap_riepilogo = "";
  int i = 0;
  for each g in gap_list
  {
    if (i < 5)
    { gap_riepilogo = gap_riepilogo + "• " + g + "\n"; i = i+1; }
  }

  // ── OUTPUT ────────────────────────────────────────────────────────────
  map out = map();
  out.put("tot_crit",     tot_crit.toString());
  out.put("tot_warn",     tot_warn.toString());
  out.put("c_net",        c_net.toString());
  out.put("c_vms",        c_vms.toString());
  out.put("c_acs",        c_acs.toString());
  out.put("c_av",         c_av.toString());
  out.put("c_gov",        c_gov.toString());
  out.put("w_net",        w_net.toString());
  out.put("w_vms",        w_vms.toString());
  out.put("w_acs",        w_acs.toString());
  out.put("w_av",         w_av.toString());
  out.put("w_gov",        w_gov.toString());
  out.put("pacchetto",    pacchetto);
  out.put("gap_riepilogo", gap_riepilogo);
  return out;
}
```

---

### STEP 4 — Aggiorna Account CRM

```
Azione: Zoho CRM → Aggiorna record → Account [ID dal STEP 1]

NIS2_Classificazione  = [STEP2.classificazione]
NIS2_Data_Assessment  = today()
NIS2_Score_Crit       = [STEP3.tot_crit]
NIS2_Score_Warn       = [STEP3.tot_warn]
NIS2_Score_Net        = [STEP3.c_net]
NIS2_Score_VMS        = [STEP3.c_vms]
NIS2_Score_ACS        = [STEP3.c_acs]
NIS2_Score_AV         = [STEP3.c_av]
NIS2_Score_Gov        = [STEP3.c_gov]
NIS2_Gap_Riepilogo    = [STEP3.gap_riepilogo]
NIS2_Pacchetto        = [STEP3.pacchetto]
NIS2_Has_VMS          = ([Forms.vms_presente] == "Sì")
NIS2_Has_ACS          = ([Forms.acs_presente] == "Sì")
```

---

### STEP 5 — Crea Deal nella pipeline NIS 2

```
Azione: Zoho CRM → Crea record → Deal

Deal Name             = "NIS 2 – " + [Forms.nome_azienda] + " – " + today()
Account Name          = [Account ID dal STEP 1]
Pipeline              = "NIS 2 – Kaplet"
Stage                 = "Assessment completato"
Owner                 = [Forms.commerciale_kaplet] → lookup utente CRM
Closing Date          = today() + 90 giorni
NIS2_Classificazione  = [STEP2.classificazione]
NIS2_Gap_Critici      = [STEP3.tot_crit]
NIS2_Pacchetto        = [STEP3.pacchetto]
NIS2_Verticali        = verticali con gap critici > 0
```

---

### STEP 6 — Genera PDF con Zoho Writer

```
Template: "NIS 2 Report – Kaplet v3"

Variabili merge fields:
  {{azienda}}              = [Forms.nome_azienda]
  {{settore}}              = [Forms.settore]
  {{data}}                 = today() — formato DD/MM/YYYY
  {{commerciale}}          = [Forms.commerciale_kaplet]
  {{classificazione}}      = [STEP2.classificazione]
  {{desc_classif}}         = [STEP2.descrizione]
  {{sanzione}}             = [STEP2.sanzione]
  {{tot_crit}}             = [STEP3.tot_crit]
  {{tot_warn}}             = [STEP3.tot_warn]
  {{gap_networking}}       = [STEP3.c_net] criticità · [STEP3.w_net] attenzione
  {{gap_vms}}              = [STEP3.c_vms] criticità · [STEP3.w_vms] attenzione
  {{gap_acs}}              = [STEP3.c_acs] criticità · [STEP3.w_acs] attenzione
  {{gap_av}}               = [STEP3.c_av] criticità · [STEP3.w_av] attenzione
  {{gap_gov}}              = [STEP3.c_gov] criticità · [STEP3.w_gov] attenzione
  {{gap_riepilogo}}        = [STEP3.gap_riepilogo]
  {{pacchetto}}            = [STEP3.pacchetto]

Nome file: "Kaplet_NIS2_" + [Forms.nome_azienda] + "_" + today() + ".pdf"
Formato output: PDF
```

---

### STEP 7 — Allega PDF e invia email al cliente

```
Azione 1: Zoho CRM → Allega PDF all'Account
Azione 2: Zoho CRM → Allega PDF al Deal

Azione 3: Zoho Mail → Invia email

  A:        [Forms.email_referente_cliente]
  CC:       [email commerciale Kaplet]
  Oggetto:  "·KAPLET – Report NIS 2: " + [Forms.nome_azienda]
  Corpo:
    Gentile [Forms.nome_referente],

    in allegato il report NIS 2 elaborato durante il nostro incontro.

    Classificazione NIS 2: [STEP2.classificazione]
    Gap critici rilevati:  [STEP3.tot_crit]
    Pacchetto consigliato: [STEP3.pacchetto]

    Il team Kaplet è disponibile per presentare i risultati
    nel dettaglio e definire il percorso di adeguamento.

    [Firma commerciale]
    ·KAPLET – kaplet.it

  Allegato: [PDF STEP 6]
```

---

### STEP 8 — Notifica team su Zoho Cliq

```
Canale: #nis2-assessment

Messaggio:
  🔴 Nuovo assessment NIS 2 completato

  Cliente:       [Forms.nome_azienda]
  Settore:       [Forms.settore]
  Classifica:    [STEP2.classificazione]
  Pacchetto:     [STEP3.pacchetto]

  Gap critici:   [STEP3.tot_crit] totali
  ↳ Networking:  [STEP3.c_net]
  ↳ VMS:         [STEP3.c_vms]
  ↳ ACS:         [STEP3.c_acs]
  ↳ AV:          [STEP3.c_av]
  ↳ Governance:  [STEP3.c_gov]
    (include formazione, crittografia, supply chain, CDA)

  Commerciale:   [Forms.commerciale_kaplet]
  → Deal creato in CRM · Report PDF inviato al cliente
```

---

### STEP 9 — Crea attività follow-up nel CRM

```
Azione: Zoho CRM → Crea attività

  Tipo:        Meeting / Chiamata
  Oggetto:     "Follow-up NIS 2 – " + [Forms.nome_azienda]
  Collegato a: [Deal ID dal STEP 5]
  Assegnato:   [Forms.commerciale_kaplet]
  Data:        today() + 3 giorni lavorativi
  Ora:         09:00
  Note:        "Report NIS 2 inviato al cliente.
                Classificazione: " + [STEP2.classificazione] + "
                Gap critici: " + [STEP3.tot_crit] + "
                Pacchetto consigliato: " + [STEP3.pacchetto]
```

---

## PARTE 4 — ZOHO WRITER: TEMPLATE PDF REPORT v3

```
INTESTAZIONE (ogni pagina):
  Logo ·KAPLET (sx) | "NIS 2 Assessment Report" (centro) | {{data}} (dx)
  Linea verde #3DED7A

────────────────────────────────────────
PAG. 1 – INTESTAZIONE

  ·KAPLET
  _report nis 2 assessment

  Azienda:          {{azienda}}
  Settore:          {{settore}}
  Data assessment:  {{data}}
  Elaborato da:     {{commerciale}} – ·KAPLET

  ┌─────────────────────────────────────┐
  │ Classificazione: {{classificazione}} │
  └─────────────────────────────────────┘

  {{desc_classif}}
  Sanzione massima: {{sanzione}}

────────────────────────────────────────
PAG. 2 – SINTESI RISULTATI

  _sintesi gap analysis

  Gap critici:    {{tot_crit}}
  Da migliorare:  {{tot_warn}}

  _score per verticale
  Networking:              {{gap_networking}}
  Videosorveglianza & VMS: {{gap_vms}}
  Controllo Accessi:       {{gap_acs}}
  Audio Video & UC:        {{gap_av}}
  Governance & Processi:   {{gap_gov}}
    (formazione, crittografia, supply chain, responsabilità CDA)

────────────────────────────────────────
PAG. 3 – GAP PRINCIPALI

  _gap critici rilevati

  {{gap_riepilogo}}

  I gap critici richiedono intervento prioritario.
  Dal 1° gennaio 2026 la notifica al CSIRT Italia è
  obbligatoria entro 24 ore da ogni incidente significativo.

────────────────────────────────────────
PAG. 4 – RACCOMANDAZIONI KAPLET

  _pacchetto consigliato

  {{pacchetto}}

  Il percorso Kaplet:
  DESIGN  → Audit 3 verticali · Gap analysis · Risk assessment
  BUILD   → NGFW · VLAN · Hardening VMS/ACS/AV · SIEM · MFA
  MANAGE  → DOC monitoring 24/7 · CSIRT · Report direzionale

────────────────────────────────────────
FOOTER:
  ·KAPLET – Cesena / Milano – kaplet.it
  D.Lgs. 138/2024 – Direttiva (UE) 2022/2555
  Det. ACN 164179 del 14/04/2025
  Documento riservato
```

---

## PARTE 5 — ZOHO ANALYTICS: DASHBOARD NIS 2 v3

**Connetti:** Zoho Analytics → Sorgente dati → Zoho CRM

### Report da creare

| Report | Tipo | Cosa mostra |
|---|---|---|
| KPI Summary | Componenti numerici | N° assessment / Deal attivi / Media gap critici per assessment |
| Classificazione NIS 2 | Torta | Distribuzione: Essenziale / Importante / Da verificare / Non soggetto |
| Pacchetti consigliati | Barre | Distribuzione: Starter / Essential / Complete / Managed |
| Score per verticale | Barre raggruppate | Media gap critici: Net / VMS / ACS / AV / Gov |
| Gap governance v3 | Barre | Dettaglio score governance: backup / CSIRT / formazione / crittografia / supply / CDA |
| Pipeline per commerciale | Barre | N° assessment e deal per commerciale |
| Heatmap settore × verticale | Heatmap | Settore cliente vs verticale — dove sono i gap maggiori |
| Trend assessment | Linea | N° assessment nel tempo — misura adozione del tool |

---

## PARTE 6 — PIANO DI IMPLEMENTAZIONE 4 SETTIMANE

```
SETTIMANA 1 – Fondamenta
  □ Aggiungi tutti i campi custom al CRM Account e Deal (Parte 1)
  □ Crea pipeline "NIS 2 – Kaplet" nel CRM
  □ Crea il canale #nis2-assessment su Zoho Cliq
  □ Costruisci il form su Zoho Forms (Parte 2)
     → Attenzione ai 4 nuovi campi Sezione 5B
  □ Testa il form su tablet con dati fittizi

SETTIMANA 2 – Automazioni base
  □ Crea template PDF su Zoho Writer (Parte 4)
  □ Configura Flow: STEP 1 (Form → CRM Account)
  □ Configura Flow: STEP 5 (CRM → Crea Deal)
  □ Configura Flow: STEP 8 (Notifica Cliq)
  □ Test end-to-end: compila form → verifica Account e Deal creati

SETTIMANA 3 – Deluge + PDF + Email
  □ Implementa funzione Deluge classifica_nis2 (STEP 2)
  □ Implementa funzione Deluge calcola_score_gap_v3 (STEP 3)
  □ Configura aggiornamento Account con score (STEP 4)
  □ Configura generazione PDF (STEP 6)
  □ Configura email automatica al cliente (STEP 7)
  □ Configura attività follow-up (STEP 9)
  □ Test completo con un assessment reale

SETTIMANA 4 – Analytics + Go live
  □ Crea gli 8 report Analytics (Parte 5)
  □ Briefing commerciali (30 min con le slide PowerPoint)
  □ Primo assessment reale con cliente pilota
  □ Raccolta feedback e ottimizzazioni
  □ Go live ufficiale
```

---

## NOTE OPERATIVE PER I COMMERCIALI

```
PRIMA DELLA VISITA
  → Apri Zoho Forms sul tablet
  → Cerca "NIS 2 Assessment – Kaplet"
  → Il form funziona anche offline — sincronizza al rientro

DURANTE LA VISITA (20-25 minuti)
  → Usa le domande come guida di conversazione
  → Le domande tecniche (VMS, ACS, AV) coinvolgi il responsabile IT
  → La domanda sul CDA è la più importante: coinvolgi l'amministratore

DOPO IL SUBMIT (automatico)
  → Cliente riceve PDF via email entro 5 minuti
  → Deal creato automaticamente nel CRM
  → Follow-up pianificato a 3 giorni
  → Notifica al team tecnico su Cliq

FRASI UTILI
  "Stiamo raccogliendo alcune informazioni per capire
   se la sua azienda è soggetta alla NIS 2 e dove
   concentrarsi per prima cosa."

  "Non è un audit: è una fotografia rapida per darle
   un report concreto e un percorso chiaro."

  "Il report le arriva via email mentre siamo qui —
   così lo vediamo insieme e definiamo il passo successivo."

  [Se il CDA è presente]
  "L'Art. 23 del D.Lgs. 138/2024 stabilisce la responsabilità
   personale degli amministratori. È importante che lei sia
   informato e che ci sia un piano formalmente approvato."
```

---

*·KAPLET – NIS 2 Assessment System v3.0*
*Allineato al tool HTML v3 — D.Lgs. 138/2024 – Direttiva (UE) 2022/2555*
*Det. ACN 164179 del 14/04/2025 — 37 misure (Importanti) / 43 misure (Essenziali)*
