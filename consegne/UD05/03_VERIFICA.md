# Consegna UD05 — Verifica

## Parte A — Scelta singola

Per le domande 1–8 riporta risposta e motivazione.

1. B. `/24`, /24 mette a disposizione 256 indirizzi, mentre /26 ne mette a disposizione 64
2. B. sovrapposizione degli indirizzi, VNet con indirizzi sovrapposti non possono essere collegate
3. B. 200, il valore più basso di priorità viene valutato prima
4. A. subnet e NIC
5. A. consentito da entrambi
6. A. tradurre nomi in indirizzi
7. B. IP Flow Verify
8. B. non garantisce raggiungibilità

## Parte B — Risposte brevi

9. Prevedere un margine di crescita evita l'esaurimento in produzione degli IP disponibili e scongiura la necessità di dover ricreare o modificare subnet in seguito.
10. Il DNS ha il solo compito di tradurre i nomi host in indirizzi IP. Per routing si intende l'instradamento dei pacchetti, cioé viene definito il percorso che i pacchetti devono seguire. Una route associa un prefisso di destinazione a un next hop. L'NSG, invece, funge da filtro di sicurezza (firewall). Contiene delle regole che determinano se autorizzare (Allow) o bloccare (Deny) il traffico.
11. Un NSG tiene traccia dello stato delle connessioni. Se un flusso è consentito in una direzione, il traffico di risposta appartenente a quello stesso flusso viene automaticamente autorizzato e non richiede una ulteriore regola nella direzione opposta. Ciò, tuttavia, non autorizza nuove connessioni indipendenti.
12. Applicare la policy a livello di subnet garantisce che tutti i servizi e le NIC collegate ereditino le stesse regole di sicurezza. Ciò riduce la frammentazione delle policy e semplifica l'analisi dei problemi.
13. Le verifiche possibili sono consultazione delle route effettive e delle regole di sicurezza effettive. Quello che manca è il test del flusso (IP Flow Verify) poiché manca la VM.

## Parte C — Caso situazionale

14. Il nome delle regole NSG non ha nessun effetto sulla valutazione. È unicamente importante valore della priorità. Avendo priorità 150, la regola Deny-Web viene esaminata prima della regola AAA-Allow-Web con priorità 400.
15. La correzione minima consiste nel modificare la priorità della regola Allow-Web assegnandole un valore inferiore a 150, mantenendo inalterati gli altri parametri. Oppure, al contrario, assegnare un valore maggiore a 400 alla priorità della regola Deny-Web.
16. Seguendo la catena di troubleshooting (DNS -> route -> NSG -> endpoint/porta -> servizio):
- Verifico che non ci sia una NSG a livello di NIC
- Controllo le route effettive
- Verifico che il DNS stia risolvendo il nome correttamente
- Controllo il servizio su VM
