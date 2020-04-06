DATI OPEN:
- WorldBank:
  - https://data.worldbank.org/
  - https://databank.worldbank.org/home
  - catalogo: https://datacatalog.worldbank.org/
- OMS (API OData): https://www.who.int/data/gho/info/gho-odata-api
- USA:
	- https://www.data.gov/
- UE:
	- Portale Open Data UE: https://data.europa.eu/euodp/it/home
	- https://www.europeandataportal.eu/en
- ITALIA:
	- Dati Aperti della PA (anche formato OData): https://www.dati.gov.it/
	- Dati geo della PA: https://geodati.gov.it/geoportale/
	- datahub.io: https://datahub.io/
	- DatiOpen.it (poco aggiornato): http://www.datiopen.it/
	- Open Data Hub Italia: https://www.sciamlab.com/opendatahub/dataset

!! fonti OData: OMS, DatiOpen.it, dati locali (regionali/comunali)

- Le datasources di tipo JSON possono prendere dati sia da un servizio web (API) sia da un file (es. Github raw json files)

**************************************************

Questo tutorial illustra come creare con Cyclotron una dashboard che includerà le seguenti features:
- fonti dati JSON
- fonti dati OData ?
- elaborazione di fonti dati diverse tramite JavaScript
- dati geospaziali e geoJSON su mappa interattiva
- criptazione di dati sensibili nella configurazione.

I dati utilizzati si riferiscono alla diffusione del virus Covid-19 in Italia e sono resi disponibili dalla Protezione Civile sulla seguente repository: https://github.com/pcm-dpc/COVID-19. Le altre fonti dati utilizzate sono:

- confini amministrativi delle regioni italiane, forniti da https://gist.github.com/datajournalism-it

### 1. Creazione di una Nuova Dashboard

Dall'interfaccia di Cyclotron, crea una nuova dashboard denominata `analisi-covid19-italia`. Nella sezione *Details* dell'editor si possono configurare le proprietà generali della dashboard, come il tema grafico o la barra laterale. Poiché ogni dashboard viene salvata come documento JSON, cliccando sul pulsante *Edit JSON* si può visualizzare e modificare direttamente tale documento.

Questo è un esempio della configurazione iniziale della dashboard:

```
{
    "description": "Analisi della diffusione del virus Covid-19 in Italia",
    "name": "analisi-covid19-italia",
    "pages": [],
    "showDashboardControls": false,
    "sidebar": {
        "showDashboardSidebar": false
    },
    "theme": "light"
}
```

La dashboard di questo tutorial sarà composta da due pagine: la prima mostrerà la mappa dell'Italia e alcuni dati giornalieri a livello nazionale; selezionando una regione dalla mappa si potrà accedere alla seconda pagina per visualizzare i dati giornalieri a livello regionale. In entrambe le pagine, uno slider temporale permetterà di selezionare il giorno da analizzare.

### 2. Intestazione

Nella sezione *Pages* dell'editor, crea due pagine tramite il pulsante *Add Page* e apri la prima. Le proprietà principali delle pagine sono il numero di righe e il numero di colonne in cui verranno suddivise per formare una griglia in cui posizionare i widget.

Alla prima pagina assegna le seguenti proprietà:

- Name: `analisi-nazionale`
- Grid Columns: `4`
- Grid Rows: `5`

Per aggiungere alla dashboard un'intestazione e i pulsanti di navigazione tra le pagine, crea un nuovo widget tramite il pulsante *Add Widget*, assegnagli il tipo `Header` e le seguenti proprietà:

- Title: `Diffusione del Virus Covid-19 in Italia`
- Show Title: `true`
- Show Parameters: `false`
- Grid Rows: `1`
- Grid Columns: `4`

Per aggiungere i pulsanti di navigazione occorre creare un apposito script HTML nel riquadro della proprietà *HTML Content*; lo script verrà compilato e aggiunto sotto il titolo del widget. Dato che l'installazione di Cyclotron include Bootstrap 3, si può usare per dare uno stile agli elementi HTML senza bisogno di importarlo.

```
<div class="button-group" align="center">
    <button class="btn btn-default" id="naz">Analisi Nazionale</button>
    <button class="btn btn-default" id="reg">Analisi Regionale</button>
</div>
<script>
    // La funzione goToPage(pageNum) permette di navigare alla pagina specificata
    $('#naz').on('click', function () {
        Cyclotron.goToPage(1);
    });
    $('#reg').on('click', function () {
        Cyclotron.goToPage(2);
    });
</script>
```

Dopo aver salvato e cliccato sul pulsante *Preview*, potrai visualizzare la dashboard con il widget appena creato.

### 3. Slider Temporale

Crea nella stessa pagina un altro widget, assegnagli il tipo `Slider` e configuralo con le seguenti proprietà, per specificare l'intervallo di tempo e la scala di valori da visualizzare accanto allo slider:

- Minimum date-time: `2020-02-24`
- Maximum date-time: `#{dataDiOggi}`
- Date-time Format: `YYYY-MM-DD`
- Step: `1`
- Pips.Mode: `count`
- Pips.Values: `5`
- Pips.Stepped: `true`
- Grid Rows: `4`
- Grid Columns: `1`
- Orientation: `vertical`
- Direction: `rtl` (right-to-left o da sotto a sopra)
- Tooltips: `true`
- Initial Handle Position: `#{dataDiOggi}`

La stringa `#{dataDiOggi}` è un placeholder e indica che il valore da assegnare, in questo caso, alle proprietà *Maximum date-time* e *Initial Handle Position* corrisponde a quello del parametro **dataDiOggi**, che creerai tra poco. Cerca la proprietà *Subscription To Parameters*, clicca sul pulsante *Add Subscription to Parameters* e inserisci il nome del parametro, `dataDiOggi`. Quando il widget verrà caricato e ad ogni eventuale aggiornamento del parametro, il placeholder verrà sostituito con il valore attuale di **dataDiOggi**.

Nella sezione *Parameters* dell'editor della dashboard, clicca il pulsante *Add Parameter* e assegna al nuovo parametro le seguenti proprietà:

- Name: `dataDiOggi`
- Default Value: `${moment().format('YYYY-MM-DD')}`
- Show in URL: `false`

Il valore di default sarà la data di oggi, nello stesso formato utilizzato dallo slider.

Crea un altro parametro con le seguenti proprietà:

- Name: `dataSelezionata`
- Default Value: `${moment().format('YYYY-MM-DD')}`
- Show in URL: `false`

Il parametro **dataSelezionata** conterrà la data selezionata tramite lo slider e verrà utilizzato dagli altri componenti della dashboard per filtrare i dati. Inizialmente avrà come valore la data di oggi, ma si aggiornerà ogni volta che verrà selezionata una nuova data tramite lo slider.

Per configurare la gestione della selezione di una data, torna sulla sezione dell'editor dedicata allo slider, cerca la proprietà *Specific Events*, clicca sul pulsante *Add param-event* e popola i seguenti campi:

- Parameter Name: `dataSelezionata`
- Event: `dateTimeChange`

In questo modo, ad ogni evento "cambio data" il parametro cambierà valore. Puoi cliccare su *Preview* per visualizzare lo stato della dashboard.

Adesso il documento JSON completo del widget slider dovrebbe essere questo:

```
{
    "direction": "rtl",
    "gridHeight": 4,
    "gridWidth": 1,
    "handlePosition": "#{dataDiOggi}",
    "maxValue": "#{dataDiOggi}",
    "minValue": "2020-02-24",
    "momentFormat": "YYYY-MM-DD",
    "orientation": "vertical",
    "parameterSubscription": ["dataDiOggi"],
    "pips": {
        "mode": "count",
        "stepped": true,
        "values": "5"
    },
    "player": {},
    "specificEvents": [{
        "event": "dateTimeChange",
        "paramName": "dataSelezionata"
    }],
    "step": 1,
    "tooltips": true,
    "widget": "slider"
}
```

### 4. Contatori

Il file JSON https://github.com/pcm-dpc/COVID-19/blob/master/dati-json/dpc-covid19-ita-andamento-nazionale.json contiene i dati sul numero di contagi, ricoveri, decessi, tamponi registrati giorno per giorno a livello nazionale. Questi dati possono essere analizzati sulla dashboard tramite la configurazione di una datasource, che li recupererà e rielaborerà per la visualizzazione in un widget.

Nella sezione *Data Sources* dell'editor, clicca sul pulsante *Add Data source* per creare una nuova datasource di tipo `JSON` e assegnale le seguenti proprietà:

- Name: `contatori-nazionali`
- URL: `https://raw.githubusercontent.com/pcm-dpc/COVID-19/master/dati-json/dpc-covid19-ita-andamento-nazionale.json`
- Subscription To Parameters: `dataSelezionata`

La datasource si aggiornerà ogni volta che il parametro **dataSelezionata** avrà un nuovo valore, il quale verrà usato per filtrare tra i dati solo quelli relativi alla data selezionata. La proprietà *Post-Processor*, quando specificata, è una funzione JavaScript che riceve in input il dataset ottenuto dalla chiamata all'URL (che in questo esempio è un'array di giorni con i relativi dati) e può modificarlo prima che venga restituito come risultato della datasource. In questo caso, la funzione dovrà trovare nell'array di giorni quello corrispondente alla data selezionata e preparare i dati per il widget che li riceverà.

Configura la proprietà *Post-Processor* come segue:

```
e = function(dataset){
    var result = [];
    var day = Cyclotron.parameters.dataSelezionata; //accesso al parametro
    
    var dayData = _.find(dataset, function(d){
        return moment(d.data, 'YYYY-MM-DDTHH:mm:ss').isSame(moment(day, 'YYYY-MM-DD'), 'day');
    });
    
    if(dayData){
        result.push(dayData);
    }
    
    return result;
}
```

Ora che la datasource è pronta, torna alla pagina `analisi-nazionale` e crea quattro nuovi widget di tipo `Number`, ognuno con le seguenti proprietà (puoi copiare il documento JSON del primo nell'editor JSON degli altri):

- Data Source: `contatori-nazionali`
- Grid Rows: `1`
- Grid Columns: `1`
- No Data Message: `Dati non disponibili per la data scelta`

Adesso tutti e quattro i widget leggeranno la stessa datasource, ma ognuno esporrà uno dei dati. La proprietà *Numbers* può essere usata per mostrare una serie di valori statici oppure provenienti dalla datasource (tramite la sintassi `#{campo_valore}`). Nel primo widget di tipo `Number`, cliccando sul pulsante *Add number*, crea un numero con le seguenti proprietà:

- Number: `#{totale_casi}`
- Prefix: `Totale Casi`

Fai lo stesso per il secondo widget:

- Number: `#{totale_positivi}`
- Prefix: `Totale Positivi`

Il terzo:

- Number: `#{dimessi_guariti}`
- Prefix: `Dimessi Guariti`

E infine il quarto:

- Number: `#{deceduti}`
- Prefix: `Decessi`

Se clicchi nuovamente su *Preview* e provi a cambiare la data selezionata con lo slider, vedrai che i quattro contatori si aggiorneranno con i dati relativi al giorno scelto o, nel caso questi non fossero disponibili, con il messaggio impostato.

Al momento i quattro widget saranno disposti sulla dashboard da sinistra a destra, nell'ordine in cui sono elencati nella pagina. Nel prossimo passaggio verrà aggiunto l'ultimo widget della pagina e i contatori si incolonneranno per riempire lo spazio sulla griglia.

### 5. Dati Geografici e Mappa Interattiva

La mappa che stai per creare avrà i seguenti elementi:

- layer OSM: mappa geografica di base
- layer vettoriale con i confini regionali: ogni regione sarà rappresentata come una feature GeoJSON che, se selezionata con un click, permetterà di procedere con l'analisi dei dati regionali nella seconda pagina della dashboard
---------------------------------------------------------
- overlays: su ogni regione sarà rappresentato un cerchio di dimensione proporzionata al numero totale di casi registrati sul territorio

Gli overlays saranno elaborati da una datasource e passati al widget OpenLayers.

Il file https://github.com/pcm-dpc/COVID-19/blob/master/dati-json/dpc-covid19-ita-regioni.json contiene i dati sul numero di contagi, ricoveri, decessi, tamponi registrati giorno per giorno per ogni regione, completi delle coordinate geografiche necessarie per geolocalizzarli.

Crea una nuova datasource di tipo JSON con le seguenti proprietà:

- Name: `casi-per-regione`
- URL: `https://github.com/pcm-dpc/COVID-19/raw/master/dati-json/dpc-covid19-ita-regioni.json`
- Subscription to Parameters: `dataSelezionata`
- Post-Processor:

```
!!! TODO !!! ispezione overlays, non spariscono quando la datasource si svuota
```

La funzione in *Post-Processor* seleziona nell'insieme di dati regionali quelli relativi alla data selezionata, dopodiché, per ciascuna regione, calcola la dimensione del cerchio che le verrà assegnato in base al numero totale di casi registrati e determina il posizionamento, l'elemento HTML con cui rappresentare l'overlay e la classe CSS da assegnargli. Quest'ultima dovrà essere definita nell'apposita sezione dell'editor.

Nella sezione *Styles* dell'editor, clicca sul pulsante *Add Style* e assegna alla proprietà *CSS Text* la seguente classe CSS:

```
.regions {
    opacity: .8;
    border-radius: 50%;
    line-height: 50px;
    background-color: red;
}
```

A questo punto, torna alla pagina `analisi-nazionale`, clicca su *Add Widget* e poi trascina il nuovo widget tra quello di tipo `Slider` e il primo di tipo `Number`, in modo che sia al terzo posto nell'elenco dei widget inclusi nella pagina. Assegna al nuovo widget le seguenti proprietà:

- Widget Type: `OpenLayers Map`
- Data Source: `casi-per-regione`
- Center.X: `13`
- Center.Y: `42`
- Zoom: `5`
- Grid Rows: `4`
- Grid Columns: `2`

Alla proprietà *Layers*, cliccando su *Add Layer* aggiungi due layers. Al primo, che sarà un layer di base in colori neutrali, assegna le seguenti proprietà:

- Type: `tile`
- Source.Name: `Stamen`
- Source.Configuration:

```
{
    "layer": "toner-lite"
}
```

Il secondo layer sarà di tipo vettoriale e rappresenterà i confini regionali:

- Type: `vector`
- Source.Name: `Vector`
- Source.Configuration:

```
{
    "format": new ol.format.GeoJSON(),
    "url": "https://gist.githubusercontent.com/datajournalism-it/f1abb68e718b54f6a0fe/raw/23636ff76534439b52b87a67e766b11fa7373aa9/regioni-con-trento-bolzano.geojson"
}
```

Affiché il widget interpreti correttamente i dati provenienti dalla datasource, occorre configurare la proprietà `DataSource Mapping` come segue, specificando quindi quali campi nel risultato prodotto dalla datasource contengano determinate informazioni:

- Identifier Field: `id`
- Overlay List Field: `regions`
- CSS Class Field: `cssClass`
- Overlay Identifier Field: `denominazione_regione`
- Position Field: `coordinates`
- Positioning Field: `positioning`
- Template Field: `htmlContent`

A questo punto, aprendo il preview della dashboard, sarà già visibile la mappa con i confini regionali e i cerchi rossi indicheranno proporzionalmente il numero di contagi rilevati per ciascuna regione in una specifica data. L'ultimo passaggio per concludere la prima pagina è l'implementazione della selezione di una regione dalla mappa.

-----------------------------------------------------------






- cliccabilità features


