# UD11 — Consegna laboratorio autonomo

## Baseline

- status: ok
- version: v2
- target port: 8000

## Errore

- nuovo target port: 9999
- sintomo: FQDN irraggiungibile
- HTTP/timeout: `curl: (28) Operation timed out after 20006 milliseconds with 0 bytes received`

## Evidenze

- running status: Running
- revision: v2
- health: Healthy
- log listen port: 8000
- ingress target port: 9999

## Diagnosi

- Sintomo: FQDN irraggiungibile.
- Risultato atteso: Risposta 200 OK chiamando `curl -i --max-time 20 "https://$ACA_FQDN/health"`.
- Evidenza: Facendo la stessa chiamata ottengo un timeout. I log del container confermano che il backend è avviato sulla porta 8000. Lo stato dell'applicazione e della revision risulta attivo e healthy. L'output di `az containerapp ingress show` indica `targetPort: 9999`.
- Ipotesi: L'ingress riceve il traffico esterno, ma tenta di inoltrarlo internamente verso una porta sulla quale nessun servizio sta ascoltando all'interno del container.
- Causa: C'è una discrepanza tra il parametro targetPort dell'ingress di Container Apps (9999) e l'effettiva porta di ascolto dell'applicazione (8000).
- Correzione minima: Modificare la porta di ingress, allineandola sulla porta di ascolto dell'applicazione.

## Verifica

- target port: 8000
- health: 200 OK
- version: v2

## Domande

1. Era necessario creare una nuova image?
No. L'immagine (v2) conteneva già il codice corretto e il web server regolarmente avviato. Il problema era puramente legato alla configurazione in Azure Container Apps.

2. Era necessario fare push di v3?
No. Poiché il container e il codice applicativo non richiedevano alcuna modifica, non vi era motivo di compilare un nuovo artefatto né di pubblicare un nuovo tag v3 sul registry.

3. Il problema era ACR, managed identity o ingress?
Ingress. ACR e Managed Identity avevano già completato i loro compiti. L'autenticazione era riuscita, l'immagine era stata scaricata e il container era in esecuzione (healthy). Il guasto era confinato al routing del traffico di rete tra l'endpoint pubblico e la porta interna del container.

4. Quale evidenza ha identificato la causa?
I log mostravano che il processo backend ascoltava sulla porta 8000 mentre la configurazione dell'ingress mostrava invece una targetPort impostata su 9999.

5. Perché modificare più impostazioni contemporaneamente sarebbe stato un errore metodologico?
Perché impedisce di isolare la vera causa. Cambiare più parametri insieme rischia di introdurre nuovi errori e rende impossibile capire quale intervento abbia effettivamente risolto il problema. Un approccio metodologico corretto richiede di formulare un'ipotesi, applicare una sola modifica alla volta e verificarne il risultato.

## Passaggio alla verifica

- target port ripristinato a 8000: sì
- health v2 nuovamente OK: sì
- Resource Group mantenuto disponibile: sì/no
