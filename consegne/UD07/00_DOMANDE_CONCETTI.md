# UD07 — Risposte alle domande sui concetti

## 1. Perché `--query` è preferibile a cercare manualmente una stringa nell'output JSON?
**Risposta:**
--query sfrutta JMESPath per navigare la struttura dell'oggetto JSON, estraendo solo le proprietà necessarie. I comandi Azure CLI restituiscono una grande quantità di dati ed è molto utile poterli filtrare.

## 2. Qual è la differenza tra `table` e `tsv` in Azure CLI?
**Risposta:**
`table` organizza i dati in un formato strutturato a colonne, ottimizzato per la lettura da parte di una persona. `tsv`, invece, restituisce valori tabulati grezzi, privi di intestazioni ed è ideale per assegnare valori direttamente a variabili e riutilizzarli nei comandi successivi tramite pipe.

## 3. Che cosa significa lavorare con oggetti in PowerShell?
**Risposta:**
Significa che i cmdlet non generano testo, ma istanze di oggetti allocati in memoria. È possibile accedere direttamente alle proprietà dell'oggetto e trasferire l'intera struttura informativa lungo la pipeline verso altri comandi senza dover fare parsing del testo.

## 4. Che cosa significa che una procedura amministrativa è idempotente?
**Risposta:**
Indica la capacità di uno script di essere eseguito più volte consecutive garantendo lo stesso stato finale desiderato, senza generare errori, duplicazioni o effetti collaterali indesiderati se la risorsa è già presente.

## 5. Distingui Activity Log, Metrics e Logs.
**Risposta:**
Activity Log, Metrics e Logs sono tre categorie di Azure Monitor. Activity Log registra le operazioni del control plane, cioé la creazione, l'aggiornamento e l'eliminazione di risorse. Le Metrics sono serie temporali numeriche che monitorano le prestazioni ed il consumo in un intervallo di tempo. I Logs sono record strutturati, contenenti metadati come timestamp, stato, payload, identità, ed utilizzabili per analisi approfondite tramite linguaggi di query.

## 6. A che cosa serve un Log Analytics workspace?
**Risposta:**
È l'ambiente di Azure Monitor deputato a raccogliere e consolidare dati di log provenienti da più sorgenti e fornisce il motore su cui eseguire query.

## 7. A che cosa serve una diagnostic setting?
**Risposta:**
Funge da regola esplicita di instradamento, cioé specifica quali categorie di log o metriche devono essere prelevate da una determinata risorsa e verso quale destinazione inviarle.

## 8. Perché Activity Log e AzureActivity non sono esattamente la stessa cosa?
**Risposta:**
L'Activity Log è il servizio che traccia gli eventi di gestione a livello di subscription. Questo può essere consultato sul portale oppure instradato verso AzureActivity. AzureActivity è invece la tabella relazionale interna a un Log Analytics Workspace in cui quegli stessi eventi vengono immagazzinati una volta configurata una diagnostic setting, rendendoli interrogabili via query.

## 9. Distingui Alert Rule e Action Group.
**Risposta:**
Un Alert Rule definisce la condizione da monitorare e determina quando la condizione è soddisfatta.
Un Action Group stabilisce le azioni concrete da intraprendere quando la regola scatta ed è disaccoppiato per poter essere riutilizzato da regole diverse.

## 10. Perché un alert Fired non equivale automaticamente a un incidente?
**Risposta:**
Un alert è semplicemente un segnale che notifica il superamento di una soglia preimpostata. Un incidente, invece, comporta un impatto reale sul servizio. Soglie errate o troppo reattive creano alert noise senza che esista una reale anomalia.

## 11. Qual è la differenza tra correlazione e causalità?
**Risposta:**
Correlazione significa la constatazione che due eventi si sono verificati nello stesso arco temporale o in sequenza cronologica. Causalità significa dimostrare tecnicamente e in modo verificabile che il primo evento è la causa diretta del secondo. Per dimostrarla bisogna verificare se la metrica osservata è tecnicamente collegata alla modifica, se esistono i log di dettaglio, se il problema è riproducibile e se il problema si risolve tramite rollback.

## 12. Quali sono i passaggi essenziali di un troubleshooting ripetibile?
**Risposta:**
I passaggi sono:
1. Descrivere il sintomo: Identificare il comportamento anomalo rilevato.
2. Chiarire il risultato atteso: Definire come il sistema dovrebbe operare correttamente.
3. Raccogliere evidenze: Interrogare lo stato, l'account, le configurazioni e i log prima di intervenire.
4. Formulare un'ipotesi: Individuare la causa più probabile sulla base dei dati raccolti.
5. Verificare l'ipotesi: Eseguire controlli mirati per confermare o smentire la supposizione.
6. Applicare la modifica minima: Eseguire un solo intervento alla volta per isolare la correzione.
7. Ripetere il test: Constatare se l'esito corrisponde alle attese dopo la modifica.
8. Documentare: Tenere traccia della causa, della soluzione e aggiornare i runbook.
