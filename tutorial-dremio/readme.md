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
- `dpc-covid19-ita-province.json`: casi totali a livello provinciale (ogni oggetto rappresenta un giorno e una provincia)

Gli stessi dati si trovano anche in formato CSV nelle cartelle "dati-andamento-nazionale", "dati-regioni" e "dati-province". Dremio supporta entrambi i formati di file. Per praticità, nel tutorial utilizzerai quelli in formato JSON.

Il formato dei dataset e la descrizione dei campi si trovano nel file https://github.com/pcm-dpc/COVID-19/blob/master/dati-andamento-covid19-italia.md.

Dalla pagina di DatiOpen relativa al [censimento 2011](http://www.datiopen.it/it/opendata/Censimento_2011_Popolazione_per_regione_e_sesso), vai al tab *Scarica* ed esporta il dataset in formato CSV. Ogni riga nel file corrisponde ad una regione e riporta la popolazione maschile, femminile e totale.

### 2. Caricamento dei Dati su Dremio

Dalla tua home su Dremio, clicca sul pulsante *Upload file*, naviga fino alla cartella "dati-json" della repository e seleziona il file `dpc-covid19-ita-andamento-nazionale.json` (oppure trascinalo nel riquadro di caricamento). Clicca su *Next* e Dremio ti mostrerà un preview dei dati in formato tabellare. Clicca su *Save* e carica anche i file `dpc-covid19-ita-regioni.json` e `dpc-covid19-ita-province.json`. Infine carica il file `Censimento-2011---Popolazione-per-regione-e-sesso.csv`, rinominandolo per comodità in "censimento-2011". Dovrai cambiare *Field Delimiter* in `Custom...` inserendo `;` come valore e spuntare la casella *Extract Field Names* per leggere i nomi delle colonne dalla prima riga.

### 3. Adattamento del Dataset sul Censimento

Nel dataset *censimento-2011* la denominazione di alcune regioni è diversa da quella usata nei dataset della Protezione Civile. Prima di poterli combinare, è necessario uniformarli. Dalla home di Dremio, clicca sul dataset *censimento-2011* per visualizzarlo, seleziona la prima stringa "Friuli-Venezia Giulia" che trovi nella colonna *Regione* e seleziona l'opzione *Replace...* dal menu a tendina che si aprirà. Nella schermata successiva potrai sostituire la stringa selezionata con un nuovo valore in tutta la colonna *Regione* (di fatto Dremio creerà una nuova colonna denominata *Regione* ed eliminerà quella vecchia). Inserisci nel campo *Replacement value* la stringa `Friuli Venezia Giulia` e clicca su *Apply*. Con lo stesso procedimento, sostituisci il valore "Bolzano/Bozen" con "P.A. Bolzano" e "Trento" con "P.A. Trento".

Se a sinistra del nome delle colonne *Femmine*, *Maschi* e *Totale* c'è l'icona "Abc", significa che Dremio ha interpretato i valori riportati come testo. Per convertirli in numeri, clicca sull'icona, seleziona *Integer...* e clicca su *Apply...*.

Infine clicca su *Save as...* in alto a destra e salva il nuovo dataset virtuale nella tua home denominandolo `censimento-2011-conformed`.

### 4. Dataset Regionale con Popolazione

Per arricchire il dataset regionale con il numero di abitanti di ciascuna regione, è necessario combinarlo con i dati sul censimento.

In Dremio ci sono due modi per unire i dati di più dataset: con un editor grafico oppure con una query SQL:

**Join nell'Editor**

Dalla home di Dremio, clicca sul dataset *dpc-covid19-ita-regioni* e poi su *Join* in alto a sinistra. Nella nuova schermata, clicca sul dataset *censimento-2011-conformed* che hai creato prima e poi clicca su *Next*. Nella schermata successiva dovrai inserire una condizione per il join. Le colonne da mettere in relazione sono *denominazione_regione* per il dataset *dpc-covid19-ita-regioni* e *Regione* per il dataset *censimento-2011-conformed*; trascinale dai menu a tendina ai rispettivi riquadri in modo da inserire la condizione `denominazione_regione = Regione`. Se clicchi su *Preview*, nella tabella in basso vedrai le colonne del secondo dataset aggiunte a destra di quelle del primo. Clicca su *Apply* per confermare il join.

**Join in una Query**

Dalla home di Dremio, clicca su *New Query* e inserisci la query seguente:

```
SELECT * FROM "@dremio"."dpc-covid19-ita-regioni" regioni
JOIN "@dremio"."censimento-2011-conformed" censimento
ON regioni.denominazione_regione = censimento.Regione
```

Clicca su *Preview* e nella tabella in basso vedrai le colonne del primo dataset affiancate a quelle del secondo.

In entrambi i modi, adesso il nuovo dataset contiene tutte le colonne di entrambi i dataset di partenza. Rinomina la colonna *Totale* in *popolazione* per renderlo più comprensibile. A questo punto puoi scegliere quali colonne ti interessa includere nel dataset e quali vuoi escludere, per esempio perché contengono informazioni extra o non rilevanti. Una colonna può essere rimossa cliccando sulla freccia a destra del suo nome e selezionando *Drop*.
Conserva solo le colonne *data*, *lat*, *long*, *denominazione_regione*, *ricoverati_con_sintomi*, *terapia_intensiva*, *totale_ospedalizzati*, *isolamento_domiciliare*, *totale_positivi*, *dimessi_guariti*, *deceduti*, *totale_casi* e *popolazione* e rimuovi le altre.

Clicca su *Save As...* in alto a destra e salva il nuovo dataset virtuale con il nome `covid19-ita-regioni-with-population`.

### 5. Dataset Regionale con i Valori ogni 100000 Abitanti

Se due regioni oggi hanno 1000 casi positivi ciascuna ma la prima è dieci volte più popolosa, la situazione epidemiologica nella seconda regione è più critica di quanto appaia se si confronta solo il numero assoluto di casi positivi per regione. Dunque per confrontare l'andamento dei contagi tra le diverse regioni può essere utile rapportare i valori giornalieri per regione con una cifra di riferimento, per esempio 100000 abitanti.

Dalla home di Dremio, clicca sul dataset *covid19-ita-regioni-with-population* che hai appena creato per usarlo come punto di partenza. Rimuovi le colonne *dimessi_guariti*, *deceduti* e *totale_casi*, dato che sono contatori e riportano dati assoluti. Le colonne rimaste vanno modificate in modo che i valori riportati siano riferiti a 100000 abitanti.

Clicca sulla freccia a destra del nome di *ricoverati_con_sintomi*, seleziona *Calculated field...* e inserisci nel riquadro di sinistra che apparirà l'espressione

```
"ricoverati_con_sintomi"/("popolazione"/100000)
```

Se clicchi su *Preview* vedrai la colonna con i valori originali e quella con i valori calcolati, che andrà a sostituire la prima. Clicca su *Apply* per confermare l'operazione.

Con lo stesso procedimento, applica la trasformazione anche alle colonne *terapia_intensiva* (`"terapia_intensiva"/("popolazione"/100000)`), *totale_ospedalizzati* (`"totale_ospedalizzati"/("popolazione"/100000)`), *isolamento_domiciliare* (`"isolamento_domiciliare"/("popolazione"/100000)`) e *totale_positivi* (`"totale_positivi"/("popolazione"/100000)`).

Salva il nuovo dataset con il nome `covid19-ita-regioni-per-100k`.

### 6. Dataset Provinciale Ridotto

Il dataset *dpc-covid19-ita-province* ha alcuni campi contenenti codici territoriali e informazioni extra che possono essere rimossi nel caso non vengano utilizzati per ulteriori analisi. Clicca sul dataset e rimuovi le colonne *stato*, *codice_regione*, *codice_provincia*, *note*, *codice_nuts_1*, *codice_nuts_2* e *codice_nuts_3*.

Ogni regione nel dataset ha anche una provincia in più denominata "In fase di definizione/aggiornamento", usata per indicare i dati non ancora assegnati ad una specifica provincia. Per escludere questa provincia fittizia dal risultato, seleziona la prima stringa "In fase di definizione/aggiornamento" che trovi nella colonna *denominazione_provincia* e clicca sull'opzione *Exclude...* nel menu a tendina. Nella nuova schermata vedrai un preview del dataset senza le righe in cui `denominazione_provincia='In fase di definizione/aggiornamento'`. Clicca su *Apply* per confermare la trasformazione.


