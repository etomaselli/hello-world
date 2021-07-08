## Tutorial Dremio - Creazione di un Dataset Virtuale a Partire da Sorgenti Eterogenee

Questo tutorial illustra come creare con Dremio dei dataset virtuali a partire da sorgenti eterogenee, includendo e rielaborando solo i dati che interessano.

Le fonti dati utilizzate sono:

- i dati sulla diffusione del virus Covid-19 in Italia resi disponibili dalla Protezione Civile sulla repository https://github.com/pcm-dpc/COVID-19, in particolare andamento nazionale (dpc-covid19-ita-andamento-nazionale.json), regionale (dpc-covid19-ita-regioni.json) e provinciale (dpc-covid19-ita-province.json)
- la popolazione residente per regione, rilevata dal censimento ISTAT 2011 ed esposta sul portale DatiOpen.it, http://www.datiopen.it/it/opendata/Censimento_2011_Popolazione_per_regione_e_sesso

Dremio consente di creare nuovi dataset basati su entrambe le fonti, di rielaborare i dati sulla diffusione del virus e di arricchirli combinandoli con quelli relativi alla popolazione.

A questo tutorial seguirà una seconda parte che tratterà di come visualizzare tramite altri strumenti i dataset creati su Dremio.

### 1. Download dei Dati

Scarica la repository [COVID-19](https://github.com/pcm-dpc/COVID-19) come file .zip ed estraila. La cartella "dati-json" contiene tre file con i dati in formato JSON sul numero di contagi, ricoveri, decessi, tamponi registrati giorno per giorno da febbraio 2020:

- `dpc-covid19-ita-andamento-nazionale.json`: dati a livello nazionale (ogni oggetto rappresenta un giorno)
- `dpc-covid19-ita-regioni.json`: dati a livello regionale (ogni oggetto rappresenta un giorno e una regione)
- `dpc-covid19-ita-province.json`: dati a livello provinciale (ogni oggetto rappresenta un giorno e una provincia)

Gli stessi dati si trovano anche in formato CSV nelle cartelle "dati-andamento-nazionale", "dati-regioni" e "dati-province". Dremio supporta entrambi i formati di file. Per praticità, nel tutorial utilizzerai quelli in formato JSON.

Dalla pagina di DatiOpen relativa al [censimento 2011](http://www.datiopen.it/it/opendata/Censimento_2011_Popolazione_per_regione_e_sesso), vai al tab *Scarica* ed esporta il dataset in formato JSON. Ogni oggetto nel file corrisponde ad una regione e riporta la popolazione maschile. femminile e totale.

### 2. Caricamento dei Dati su Dremio

Dalla tua home su Dremio, clicca sul pulsante *Upload file*, naviga fino alla cartella "dati-json" della repository e seleziona il file `dpc-covid19-ita-andamento-nazionale.json` (oppure trascinalo nel riquadro di caricamento). Clicca su *Next* e Dremio ti mostrerà un preview dei dati in formato tabellare. Clicca su *Save* e carica anche i file `dpc-covid19-ita-regioni.json` e `dpc-covid19-ita-province.json`. Infine carica il file `Censimento-2011---Popolazione-per-regione-e-sesso.json`, rinominandolo per comodità in "censimento-2011".


3. query per esplorare i dati e combinarli
4. creazione di un nuovo dataset
