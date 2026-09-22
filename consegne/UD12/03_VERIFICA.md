# UD12 — Verifica individuale

## Parte A

1. B. lo stato desiderato
2. A. Azure Resource Manager
3. B. prevedere le modifiche di un deployment
4. B. prepara la directory e i provider
5. B. controlla la configurazione senza applicare risorse
6. B. collegare gli oggetti Terraform alle risorse gestite
7. B. rimuove le risorse gestite dalla configurazione/state
8. B. codice IaC, strumenti e agent configurato

## Parte B

9. Distingui approccio imperativo e dichiarativo.
L'approccio imperativo si concentra sulle azioni da compiere in sequenza per ottenere un risultato. Al contrario quello dichiarativo si concentra sulla descrizione dello stato desiderato.

10. Perché è utile separare parametri e definizione delle risorse?
Permette di rendere il codice riutilizzabile. La definizione della risorsa agisce come un blueprint, mentre parametri e variabili consentono di applicare lo stesso modello a più ambienti semplicemente cambiando i valori di input, senza modificare il blocco dichiarativo.

11. Distingui Bicep What-If e Terraform Plan.
Entrambi mostrano un'anteprima prima di applicare modifiche ma, per farlo, usano meccanismi diversi. What-If è una funzionalità interna di Azure Resource Manager che confronta il file Bicep direttamente con lo stato remoto rilevato da Azure. Terraform Plan è un processo del client Terraform che confronta il codice .tf con lo state file e con le informazioni lette tramite il provider AzureRM.

12. Perché il Terraform state non deve essere committato come un normale sorgente?
Il Terraform state non va committato principalmente per motivi di sicurezza e perché non è un file sorgente ma uno stato dinamico. Se versionato da più persone contemporaneamente genererebbe merge conflict. Inoltre memorizza in chiaro tutte le proprietà delle risorse gestite, incluse credenziali, certificati, stringhe di connessione o segreti.

13. Distingui `terraform validate`, `plan` e `apply`.
`validate` controlla solo la correttezza della sintassi e dei tipi HCL localmente, senza contattare il cloud o verificare lo state. `plan` interroga le API e lo state per calcolare le modifiche da applicare, generando un'anteprima. `apply` esegue effettivamente le chiamate API verso Azure per realizzare le modifiche e aggiorna lo state file.

14. Perché in questa UD distruggiamo le risorse Azure ma conserviamo i file IaC?
Perché le risorse Azure sono l'infrastruttura reale, mentre il codice IaC rappresenta una sorta di blueprint del progetto. Il codice IaC, invece, viene versionato su Git perché verrà riutilizzato ed esteso nei moduli successivi.

## Parte C

15. Deve eseguire immediatamente `terraform apply`? Spiega il perché.
No. Il piano mostra 2 to destroy, a fronte dell'aspettativa di aggiungere soltanto un tag. L'esecuzione immediata causerebbe la distruzione irreversibile di due risorse.

16. Quali controlli dovrebbe fare prima di applicare qualsiasi modifica?
- Usare git diff per verificare esattamente quali righe di codice sono state modificate rispetto all'ultimo commit.
- Fare una lettura approfondita dell'output di terraform plan. Spesso la modifica di un attributo non modificabile (come il nome di una risorsa, la regione o la rimozione di un intero blocco) costringe Terraform a distruggere e ricreare la risorsa.
- Verificare con terraform state list se ci sono stati cambi di identificativo logico (ad esempio una risorsa rinominata nel codice senza l'uso del blocco moved).

## Gate 2

- agent Online: sì
- Terraform disponibile: sì
- Azure CLI/Bicep disponibile: sì
- Git/Python/Docker disponibili: sì

## 17. Perché installare Terraform in WSL2 prepara il self-hosted Agent ma non garantisce nulla sul Microsoft-hosted Agent?
Perché il self-hosted Agent opera localmente e i tool installati nell'ambiente persistono. Il Microsoft-hosted Agent, invece, viene eseguito all'interno di una VM gestita da Microsoft e temporanea.

## 18. Perché un Job Microsoft-hosted non deve dipendere da file creati dal Job precedente?
Perché ogni Job assegnato a un Microsoft-hosted Agent gira su una macchina virtuale temporanea. Al completamento del Job, la VM viene distrutta insieme al suo filesystem. Il Job successivo partirà su un'istanza completamente nuova e pulita. Se servono dati persistenti, devono essere scambiati tramite artifact di pipeline o storage esterni.

## 19. Perché è utile verificare `terraform version`, `az version` e `az bicep version` all'interno di una pipeline?
Conferma che i tool necessari siano presenti nell'ambiente e pronti all'uso prima di eseguire task critici e registra nei log di esecuzione le versioni esatte impiegate, consentendo di individuare rapidamente problemi imputabili ad aggiornamenti inattesi dei tool.

## 20. Qual è il vantaggio e quale il rischio di usare `ubuntu-latest` rispetto a un'immagine versionata?
Il vantaggio è che riceve automaticamente aggiornamenti di sicurezza e toolchain aggiornate senza richiedere manutenzione manuale dei file yaml. Il rischio è la mancanza di determinismo. Quando Microsoft aggiorna l'immagine latest a una nuova versione, la pipeline può fallire improvvisamente per incompatibilità, senza che sia stato toccato il codice del repository.

## Gate 2A — self-hosted

- Agent Online: sì
- Terraform: 1.16.3
- Azure CLI: 2.90.0
- Bicep: 0.47.16
- Git: 2.54.0
- Python: 3.13.15
- Docker: 29.8.1

## Gate 2B — Microsoft-hosted

- stato: MICROSOFT_HOSTED_READY
