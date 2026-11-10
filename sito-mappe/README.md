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
- **Titolo cliccabile**: «Rete bus UdR 10» riporta alla mappa iniziale con tutte le linee (resta il giorno scelto).
- **Tempo reale** (solo per il giorno di oggi, sul sito online): ogni 30 secondi, e solo con la pagina in primo piano,
  il sito legge dal proxy (`REALTIME` in testa a `build_data.py`; codice del proxy nel repository UDR11, cartella
  `proxy-realtime/`) stato, ritardo e prossima fermata delle corse, abbinate con il `trip_id` del GTFS. Nel quadro
  orario sotto il numero delle corse in viaggio compare il ritardo (+) o l'anticipo (−) in minuti e la cella della
  prossima fermata è evidenziata in verde; nel riquadro di una fermata, in cima, i bus «In arrivo» (compresi gli
  arrivi al capolinea) con l'orario previsto. **Bus in viaggio** si accende da solo all'arrivo dei dati (se non è
  stato spento): le corse con dati reali hanno il bordo verde e stanno fra la fermata precedente e la prossima
  indicata dalle API. Se il proxy non risponde restano gli orari programmati. Nel proxy (Cloudflare) la variabile
  `AGENCY_ID` deve comprendere `UdR10AutolineeTroiani`.
- **Vicino a me** e **Bus in viaggio** (posizioni stimate dagli orari programmati, non in tempo reale).
- Orari sempre nel formato **hh:mm**: gli orari del GTFS con i secondi (es. 06:54:30) sono troncati al minuto.

Il GTFS non indica le fermate principali (`timepoint` è sempre 1): sono considerate principali i capolinea,
la prima fermata in ogni comune, i nodi serviti da almeno 4 altre linee e una fermata almeno ogni 5 minuti
di viaggio (parametri in testa a `build_data.py`).

## File

- `template.html`: grafica e funzioni del sito, con i segnaposto per i dati.
- `logo.png`: logo Astral Infomobilità al centro della testata (incluso nella pagina; per cambiarlo basta
  sostituire il file).
- `icona.png`: icona del sito (logo Unità di Rete, 180×180) per la scheda del browser e la schermata Home del
  telefono, inclusa nella pagina; per cambiarla basta sostituire il file.
- `build_data.py`: legge il GTFS e scrive il sito completo in `sito-mappe/index.html` (pubblicato online) e
  `index.html` nella radice del repo.

Online (GitHub Pages) viene pubblicato **solo** `index.html`: script, modello, README e file GTFS restano nel
repository e non sono raggiungibili dall'indirizzo del sito. La pagina non mostra il nome del file GTFS.

## Aggiornare con un nuovo GTFS

**Da GitHub**: *Add file → Upload files*, trascina il nuovo zip GTFS e fai *Commit changes* su `main`.
Il workflow *Pubblica sito mappe* rigenera e ripubblica il sito in un paio di minuti (scheda *Actions*).

**Un nuovo GTFS si può caricare in anticipo.** Il sito usa tutti gli zip presenti nella radice del repo: ogni GTFS
sostituisce il precedente a partire dalla data di avvio del suo calendario (la `start_date` più bassa di
`calendar.txt`). Per esempio, con il GTFS attuale e uno nuovo con calendari dal 02/11/2026, scegliendo un giorno fino
all'01/11 si vede l'orario attuale, dal 02/11 in poi quello nuovo; la pagina passa da sola all'orario giusto quando si
cambia giorno. Se due zip hanno la stessa data di avvio vale quello caricato per ultimo. I vecchi zip si possono
cancellare quando il loro periodo è passato; uno zip con una data di avvio successiva a quella attuale va tolto se
non deve più entrare in vigore.

Senza argomento lo script usa tutti gli zip della radice; con un percorso usa solo quel GTFS.

**Dal computer** (serve solo Python 3):

```bash
python3 sito-mappe/build_data.py [percorso/GTFS.zip]
```

## Contatore visite

Il sito conta le visite con [GoatCounter](https://www.goatcounter.com/) sull'account `andreaantonini`
(pannello: <https://andreaantonini.goatcounter.com>). Tutte le visite finiscono su un unico percorso, `/UDR10`,
senza distinguere la linea aperta (`#linea-…`) o il giorno scelto (`?giorno=…`). Le aperture del file in locale
(doppio clic) non vengono contate. Codice dell'account e percorso sono in testa a `build_data.py`
(`GOATCOUNTER`, `GOATCOUNTER_PERCORSO`); con `GOATCOUNTER = ""` il contatore è spento.
