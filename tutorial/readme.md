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

I dati utilizzati si riferiscono alla diffusione del virus Covid-19 in Italia e sono resi disponibili dalla Protezione Civile sulla seguente repository: https://github.com/pcm-dpc/COVID-19.

