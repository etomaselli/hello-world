## Tutorial Dremio - Creazione di un dataset virtuale a partire da sorgenti eterogenee

Questo tutorial illustra come creare con Dremio un dataset virtuale a partire da sorgenti eterogenee, includendo e rielaborando solo i dati che interessano.

Le fonti dati utilizzate sono:

- dati sulla diffusione del virus Covid-19 in Italia resi disponibili dalla Protezione Civile sulla repository https://github.com/pcm-dpc/COVID-19, in particolare andamento nazionale (dpc-covid19-ita-andamento-nazionale.json), regionale (dpc-covid19-ita-regioni.json) e provinciale (dpc-covid19-ita-province.json)
- popolazione residente per regione, rilevata dal censimento ISTAT 2011 ed esposta sul portale DatiOpen.it, http://www.datiopen.it/it/opendata/Censimento_2011_Popolazione_per_regione_e_sesso

Dremio consente di creare un nuovo dataset basato su entrambe le fonti e di arricchire i dati sulla diffusione del virus combinandoli con quelli relativi alla popolazione.
