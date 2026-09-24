# UD13 — Domande concetti

## 1. Perché lo state locale è accettabile in UD12 ma problematico in un team?
**Risposta:**
Se si lavora da soli le risorse sono isolate, non condivise. In un team, lo state locale non offre una sorgente di verità condivisa e si rischiano sovrascritture accidentali da esecuzioni simultanee e confusione su quale sia lo stato reale dell'infrastruttura.

## 2. A che cosa serve un backend remoto?
**Risposta:**
Serve a conservare il file di stato di Terraform in una posizione centralizzata, garantendo che tutti i membri del team e le pipeline operino sempre sull'ultima versione sincronizzata.

## 3. Distingui stage, job e step.
**Risposta:**
Uno stage raggruppa una macro-fase logica dell'intero lifecycle della pipeline, per esempio Validate, Deploy, Test. Un job è l'insieme ordinato di step assegnato ed eseguito interamente su un singolo agent. Lo step è la singola operazione elementare all'interno di un job.

## 4. Che cosa significa `checkout: self`?
**Risposta:**
Istruisce la pipeline a clonare/scaricare nell'area di lavoro dell'agent il repository in cui è definita la pipeline stessa.

## 5. Perché una pipeline self-hosted non deve assumere di trovarsi in `~/workspace/...`?
**Risposta:**
Perché `~/workspace/...` è un percorso arbitrario legato al nostro lavoro interattivo. La pipeline viene eseguita all'interno della directory gestita dall'Agent `$(Build.SourcesDirectory)`. Usare percorsi personali rompe la portabilità dell'automazione.

## 6. A che cosa serve una service connection?
**Risposta:**
Fornisce un'identità autorizzata e limitata che consente ad Azure Pipelines di autenticarsi ed eseguire operazioni su altre risorse senza esporre o riutilizzare credenziali e account utente personali.

## 7. Perché preferire Workload Identity Federation a un client secret?
**Risposta:**
Perché sfrutta un trust federato basato su token scambiati tra Azure DevOps e Microsoft Entra ID. Questo approccio elimina alla radice la gestione di password, PAT o client secret statici nella pipeline.

## 8. Perché limitiamo la service connection a un Resource Group?
**Risposta:**
Limitiamo la service connection a un Resource Group per applicare il principio del minimo privilegio. In questo modo un'eventuale compromissione della pipeline non potrà estendersi ad altre risorse o all'intera subscription.

## 9. Perché Terraform viene validato ma non applicato dalla pipeline UD13?
**Risposta:**
Perché non viene implementato il backend remoto per la gestione dello state. Eseguire l'apply in una pipeline senza backend condiviso non avrebbe senso. Terraform viene quindi limitato alla verifica di sintassi e coerenza, lasciando il primo deploy a Bicep.

## 10. Perché l'ACR creato in UD13 non deve essere eliminato?
**Risposta:**
Perché costituisce una dipendenza per le unità successive. Ospiterà l'immagine creata dalla CI in UD14 e servirà da sorgente per il deployment su Container Apps in UD15. Eliminarlo comprometterebbe l'ambiente di delivery.

## 11. A che cosa serve `trigger: none`?
**Risposta:**
Disattiva l'esecuzione automatica della pipeline a ogni push sul repository e vincola l'avvio esclusivamente a un trigger manuale.

## 12. Qual è un ordine razionale per diagnosticare una pipeline che non parte correttamente?
**Risposta:**
- Verificare se la pipeline è partita (sintassi YAML / trigger).
- Verificare se il job ha acquisito un agent disponibile nel pool.
- Verificare l'esito del checkout del codice.
- Verificare la correttezza dei path.
- Verificare la presenza dei tool necessari sul sistema dell'agent (presenti nel path, capabilities).
- Verificare l'autorizzazione della service connection.
- Verificare i permessi RBAC sulle risorse Azure.

## 13. Perché l'ACR UD13–UD15 fissa esplicitamente `LegacyRegistryPermissions`?
**Risposta:**
Per bloccare esplicitamente il registro sul modello RBAC classico di Azure e utilizzare ruoli come AcrPull/AcrPush, evitando che l'ambiente di laboratorio subisca problemi legati all'evoluzione verso il modello RBAC+ABAC a livello di repository.

## 14. Perché UD13 usa deliberatamente il self-hosted Agent come prima pipeline?
**Risposta:**
Per rendere trasparente il legame tra gli strumenti installati nel proprio ambiente WSL2, le capability/path esposte dall'Agent e i comandi eseguiti dal Job, evitando l'astrazione di un ambiente cloud preconfigurato.

## 15. A che cosa serve `workspace: clean: all` su un self-hosted Agent?
**Risposta:**
Pulisce la directory di lavoro dell'Agent prima dell'avvio del Job, impedendo che cache o file residui di esecuzioni precedenti facciano passare per funzionante una pipeline incompleta.

## 16. Distingui `workspace: clean: all` e `checkout: self, clean: true`.
**Risposta:**
`workspace: clean: all` opera a livello di job e cancella l'area di lavoro dell'Agent prima dell'inizio del job. `checkout: self, clean: true` opera a livello di step Git e ripulisce la copia Git del repository prima del fetch dei commit.

## 17. Perché la pipeline non deve usare percorsi come `~/workspace/azure-devops-lab`?
**Risposta:**
Perché le pipeline devono basarsi sulle variabili di ambiente predefinite e percorsi relativi al repository clonato dall'agent. I percorsi assoluti personali legano l'esecuzione a una specifica postazione e causano fallimenti non appena il contesto cambia.

## 18. Che cosa cambierebbe passando da `pool-ud09-wsl` a `vmImage: ubuntu-latest`?
**Risposta:**
Si passerebbe da un agent self-hosted su macchina locale a un agent Microsoft-hosted, cioè una macchina virtuale cloud temporanea.

## 19. Perché la prima pipeline GitHub usa la Azure Pipelines GitHub App invece di creare un PAT manuale?
**Risposta:**
Perché la GitHub App centralizza l'autenticazione a livello di integrazione del servizio, evita la dipendenza da account individuali o PAT e ci dà la possibilità di limitare l'accesso ai singoli repository autorizzati.

## 20. In produzione, che cosa rappresenterebbe `pool-ud09-wsl` rispetto a un vero Agent Pool aziendale?
**Risposta:**
Rappresenterebbe un singolo nodo esecutore all'interno di un raggruppamento logico. In un contesto aziendale, il pool aggrega molteplici agent pronti a servire richieste concorrenti in base a disponibilità e capability, mentre la macchina WSL2 ne costituisce una simulazione a nodo singolo.

## 21. Perché un Pool con tre Agent non garantisce da solo tre Job paralleli?
**Risposta:**
Perché la concorrenza reale è limitata dalla capacità di Parallel Jobs impostata a livello dell' organizzazione Azure DevOps. Anche in presenza di tre agent liberi, se l'organizzazione dispone di un solo job parallelo, gli altri resteranno in coda.
