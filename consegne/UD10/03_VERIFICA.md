# UD10 — Verifica

## Parte A

1. B. un artefatto usato per creare container
2. B. il build context
3. B. documenta la porta prevista nell'immagine ma non sostituisce `-p`
4. B. host 127.0.0.1:8080 → container 80
5. B. nome del servizio
6. B. il container frontend stesso
7. B. rimuove anche i volumi del progetto
8. B. può essere running ma unhealthy

## Parte B

9. Distingui image, container e registry.
L'image è un artefatto statico, immutabile e a layer che contiene runtime, codice e configurazione base. Il container è un'istanza runtime creata a partire da un'immagine. Il registry è l'archivio remoto, per esempio Azure Container Registry, usato per archiviare, versionare e distribuire le immagini tramite push e pull.

10. Spiega perché il build context dovrebbe essere limitato.
Fare uso di .dockerignore e limitare il build context permette di utilizzare meno spazio ed evitare di inserire dati sensibili all'interno dell'immagine.

11. Distingui named volume e bind mount.
Un named volume è un volume gestito interamente da Docker, isolato dal filesystem host e utile per la persistenza dei dati. Un bind mount mappa direttamente una cartella o file specifico dell'host dentro il container, utile in fase di sviluppo locale (anche se non ne abbiamo parlato a lezione oggi).

12. Perché le variabili d'ambiente permettono di riusare la stessa immagine?
Consentono di separare la configurazione dal codice. Parametri necessari per il runtime possono essere definiti a runtime, tramite docker run --env o compose.yaml.

13. Perché il backend Compose non deve necessariamente pubblicare una porta sull'host?
Il backend comunica all'interno della rete Docker privata creata da Compose. Il frontend è l'unico componente esposto all'host su porta 8080. Esso instrada internamente il traffico verso backend:8000. Esporre il backend sull'host non è necessario e riduce la superficie di attacco.

14. Quali comandi useresti per iniziare il troubleshooting di uno stack Compose che non risponde?
- `docker compose ps`: Per verificare lo stato.
- `docker compose logs`: Per esaminare i log/errori.
- `docker compose config`: Per accertarsi che il parsing del file yaml e le variabili d'ambiente siano corretti.

## Parte C

15. Qual è la causa più probabile e qual è la modifica minima?
Causa probabile: Errore di configurazione a runtime. La variabile LOW_STOCK_THRESHOLD passata al backend contiene la stringa 'abc', non un intero.
Modifica minima: Modificare il valore di LOW_STOCK_THRESHOLD in compose.yaml impostando un numero valido.

16. È necessario ricostruire l'immagine? Quali verifiche eseguiresti dopo la correzione?
No, l'immagine Docker è valida. Il problema sta esclusivamente nella configurazione runtime. È sufficiente ricreare il container.
Innanzitutto bisogna verificare che il container backend sia in stato Up e passi a healthy con `docker compose ps`. Poi verifichiamo che facendo una richiesta a `/health` la risposta sia 200 OK.

## 17. Una futura pipeline gira su `pool-ud09-wsl` e lo step `docker build` fallisce con errore di connessione al Docker daemon. Quale componente dell'ambiente controlleresti per primo e perché?
L'Agent deve poter raggiungere realmente Docker dal proprio ambiente. È molto probabile che il Docker daemon non sia avviato o non accessibile all'user dell'agent. `sudo systemctl status docker` ci permette di capire se il daemon è attivo o no.

## 18. Distingui, rispetto alla disponibilità dei tool, un self-hosted Agent da un Microsoft-hosted Agent.
Con un self-hosted agent l'ambiente e la configurazione sono interamente sotto il nostro controllo. I tool devono essere mantenuti manualmente sulla macchina host. Il vantaggio è la persistenza, cioé i tool, le cache e le immagini Docker scaricate o costruite restano sul filesystem tra un job e l'altro. Di contro, con un Microsoft-hosted agent l'ambiente è interamente gestito da Microsoft e i tool sono disponibili in base all'immagine hosted. Si tratta di un ambiente effimero, cioé ad ogni avvio di un job avremo un ambiente completamente nuovo e pulito, al costo di non avere memoria delle esecuzioni precedenti.

## Cleanup finale

- `docker compose down`:
```
Container   catalogo-prodotti-frontend-1    Removed
Container   catalogo-prodotti-backend-1     Removed
Network     catalogo-prodotti_catalog-net   Removed
```
- volume presente dopo `down`: sì, catalogo-prodotti_catalog-runtime
- `docker compose down -v`:
```
Volume catalogo-prodotti_catalog-runtime Removed
```
- volume rimosso: sì
- immagini UD10 rimosse: sì
- prune globale usato: NO

