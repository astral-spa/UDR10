# Sito della Rete UdR 10

Sito con mappa interattiva e quadri orari della rete, generato dal GTFS. È un unico file HTML autonomo
(la libreria delle mappe Leaflet è inclusa): si apre con un doppio clic o si pubblica così com'è.

## Funzioni

- **Elenco linee per comune** con ricerca per linea o fermata e numero di corse nel giorno scelto.
- **Giorno**: si sceglie una data; linee, corse e partenze seguono i calendari del GTFS.
- **Mappa** (sfondi Esri: stradale, topografica, grigio chiaro, satellite): percorsi della linea con frecce del
  senso di marcia, capolinea P/A, varianti di percorso evidenziabili, filtro per zone, schermo intero, stampa.
- **Quadro orario** per calendario e direzione, con fermate principali o tutte; clic su una corsa per vederla
  sulla mappa, clic su una fermata per le sue partenze del giorno.
- **Vicino a me** e **Bus in viaggio** (posizioni stimate dagli orari programmati, non in tempo reale).
- Orari sempre nel formato **hh:mm**: gli orari del GTFS con i secondi (es. 06:54:30) sono troncati al minuto.

Il GTFS non indica le fermate principali (`timepoint` è sempre 1): sono considerate principali i capolinea,
la prima fermata in ogni comune, i nodi serviti da almeno 4 altre linee e una fermata almeno ogni 5 minuti
di viaggio (parametri in testa a `build_data.py`).

## File

- `template.html`: grafica e funzioni del sito, con i segnaposto per i dati.
- `build_data.py`: legge il GTFS e scrive il sito completo in `sito-mappe/index.html` (pubblicato online) e
  `index.html` nella radice del repo.

Online (GitHub Pages) viene pubblicato **solo** `index.html`: script, modello, README e file GTFS restano nel
repository e non sono raggiungibili dall'indirizzo del sito. La pagina non mostra il nome del file GTFS.

## Aggiornare con un nuovo GTFS

**Da GitHub**: *Add file → Upload files*, trascina il nuovo zip GTFS e fai *Commit changes* su `main`.
Il workflow *Pubblica sito mappe* rigenera e ripubblica il sito in un paio di minuti (scheda *Actions*).

**Un nuovo GTFS si può caricare in anticipo.** Il sito usa tutti gli zip presenti nella radice del repo: ogni GTFS
sostituisce il precedente a partire dalla data di avvio del suo calendario (la `start_date` più bassa di
`calendar.txt`). Scegliendo un giorno precedente si vede l'orario in vigore prima, da quella data in poi quello nuovo;
la pagina passa da sola all'orario giusto quando si cambia giorno. Se due zip hanno la stessa data di avvio vale quello
caricato per ultimo. I vecchi zip si possono cancellare quando il loro periodo è passato; uno zip lasciato nella radice
con una data di avvio più recente sostituisce l'orario da quella data in poi.

Senza argomento lo script usa tutti gli zip della radice; con un percorso usa solo quel GTFS.

**Dal computer** (serve solo Python 3):

```bash
python3 sito-mappe/build_data.py [percorso/GTFS.zip]
```
