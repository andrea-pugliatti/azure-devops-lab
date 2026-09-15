# UD07 — Verifica

## Parte A

1. A. --query
2. B. tsv
3. B. oggetti
4. A. eventi del control plane della subscription
5. B. instradare segnali diagnostici verso destinazioni
6. B. KQL
7. B. notifiche e azioni associate agli alert
8. B. viene valutata, ma può non essere Fired

## Parte B

9. Distingui Activity Log, Metrics e Logs.
Risposta: Activity Log, Metrics e Logs sono tre categorie di Azure Monitor. Activity Log registra le operazioni del control plane, cioé la creazione, l'aggiornamento e l'eliminazione di risorse. Le Metrics sono serie temporali numeriche che monitorano le prestazioni ed il consumo in un intervallo di tempo. I Logs sono record strutturati, contenenti metadati come timestamp, stato, payload, identità, ed utilizzabili per analisi approfondite tramite linguaggi di query.

10. Spiega l'idempotenza con un esempio amministrativo.
Risposta: Indica la capacità di uno script di essere rieseguito più volte portando sempre allo stesso stato finale desiderato, senza generare errori o duplicazioni. 
Esempio: Verificare prima se un Resource Group esiste con az group exists --name rg-demo; se restituisce false lo si crea, se restituisce true lo si riutilizza senza lanciare una creazione ridondante.

11. Distingui `table` e `tsv`.
Risposta: Usando table l'output è formattato a colonne per la lettura.
Usando tsv si hanno dei valori grezzi separati da tabulazione, privi di virgolette, ideali per riutilizzare i valori conseguentemente.

12. Spiega perché Log Analytics workspace e diagnostic setting non sono la stessa cosa.
Risposta: La diagnostic setting è la regola di instradamento che specifica quali dati inviare e verso quale destinazione. Il Log Analytics workspace è il repository di destinazione effettivo in cui i dati vengono archiviati, indicizzati e interrogati tramite KQL.

13. Distingui Alert Rule e Action Group.
Risposta: L'Alert Rule definisce la condizione da valutare e stabilisce quando scattare. L'Action Group è un componente separato che stabilisce cosa fare quando la regola scatta.

14. Perché correlazione temporale non implica causalità?
Risposta: La successione temporale tra due eventi attesta solo l'ordine cronologico degli accadimenti, non che il primo sia la causa diretta del secondo. Un aumento di carico o un errore potrebbero dipendere da fattori esterni concomitanti.

## Parte C

15. Quali fatti puoi affermare con certezza?
Risposta: Possiamo vedere che: 
Alle 10:15 è stata richiesta una modifica alla risorsa. Alle 10:16 l'operazione di write si è conclusa con esito positivo. Alle 10:20 la metrica osservata ha registrato un incremento rispetto al campione precedente. Alle 10:25 la regola di alert ha rilevato il superamento della soglia ed è passata allo stato Fired.

16. Quale ulteriore analisi è necessaria prima di affermare che la modifica delle 10:15 ha causato l'alert?
Risposta: Bisogna esaminare quali parametri sono stati cambiati alle 10:15 e accertare se abbiano un nesso tecnico diretto con la metrica aumentata. Bisogna interrogare Log Analytics nella finestra 10:15–10:25 per verificare se vi siano errori o anomalie. Bisogna controllare se vi sia stato un picco concorrente di richieste client o operazioni batch non correlate alla modifica. Bisogna verificare se ripristinando la configurazione precedente la metrica rientra nella soglia normale.
