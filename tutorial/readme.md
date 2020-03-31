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
- 

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

### 2. Intestazione e Slider Temporale

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

Per aggiungere i pulsanti di navigazione occorre creare un apposito script HTML nel riquadro della proprietà *HTML Content*; lo script verrà compilato e aggiunto sotto il titolo del widget. Dato che l'installazione di Cyclotron include Bootstrap 3, si può usare per dare uno stile agli elementi HTML senza bisogno di importarlo nell'apposita sezione *Scripts*.

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


