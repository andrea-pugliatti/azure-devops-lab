# UD06 — Consegna laboratorio autonomo

## 1. Baseline

- VM: VM running
- Nginx: installato, nginx/1.24.0 (Ubuntu)
- HTTP:
```sh
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Mon, 14 Sep 2026 13:47:01 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Mon, 14 Sep 2026 12:19:17 GMT
Connection: keep-alive
ETag: "6aa7e645-267"
Accept-Ranges: bytes
```
- regola NSG: Allow-HTTP-MyIP

## 2–4. Guasto, diagnosi, ripristino

- regola introdotta: Deny-HTTP-Auto
- sintomo: 
Test HTTP ritorna `curl: (28) Failed to connect to 20.165.198.7 port 80 after 75003 ms: Couldn't connect to server`
Connettendomi tramite ssh ed effettuo test nginx `systemctl status` e il test HTTP locale `curl -I http://localhost` ritorna:
```sh
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Mon, 14 Sep 2026 13:59:07 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Mon, 14 Sep 2026 12:19:17 GMT
Connection: keep-alive
ETag: "6aa7e645-267"
Accept-Ranges: bytes
```
IP Flow Verify ritorna `Access denied` Rule: `Deny-HTTP-Auto` NSG: `vm-ud06-linux-nsg`
- ipotesi: Rule `Deny-HTTP-Auto` blocca il flusso
- controllo: Rieseguo test IP Flow Verify
- causa: Rule `Deny-HTTP-Auto` blocca il flusso
- correzione minima: Elimino la regola
- verifica: Rieseguo IP Flow Verify `Access allowed` Rule `Allow-HTTP-MyIP`

## 5. Monitoring

- metrica: Percentage CPU
- intervallo: 24 ore
- aggregazione: Average
- deduzione: Posso capire quanto è utilizzata la CPU in un arco temporale e identificare intervalli di picco ricorrenti
- cosa non posso dedurre: Non posso dedurre delle anomalie/errori.

## 6. VMSS Autoscale

- min: 1
- default:
- max: 4
- metrica: Percentage CPU Avg
- condizione: Maggiore del 70% per 5m
- azione: scale out by 1
- perché max=4: Applicare un limite massimo ci permette di tenere sotto controllo i costi.

## 7. App Service scaling

- A: Scale up
- B: Azure Monitor Autoscale
- C: Automatic Scaling
- D: Scale out manuale

## 8. Backup policy

- frequenza: Giornaliera
- orario: 2:00 AM
- retention: 30 giorni
- motivazione: La frequenza giornaliera garantisce una perdita massima di dati pari a 24 ore (RPO). L'orario notturno evita rallentamenti durante le ore lavorative. I 30 recovery point giornalieri permettono il rollback rapido da errori umani, aggiornamenti software falliti o ransomware rilevati tempestivamente.

## 9. HA / Backup / DR

- A: High Availability, quì l'obiettivo è garantire la tolleranza ai guasti e l'assenza di interruzioni di servizio.
- B: Backup, serve una copia storica registrata in un momento precedente da cui ripristinare i dati persi.
- C: Disaster Recovery, si affronta la perdita totale di un'infrastruttura geografica o di un intero data center.

## 10. RPO / RTO

- RPO: 15 minuti
- RTO: 60 minuti
