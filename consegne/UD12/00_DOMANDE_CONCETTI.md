# UD12 — Domande sui concetti

Rispondere dopo avere studiato `00_CONCETTI.md`. Non limitarsi a definizioni di una riga: quando possibile aggiungere un piccolo esempio.

## 1. Quale problema risolve l'Infrastructure as Code rispetto a una configurazione esclusivamente manuale?
**Risposta:**
Risolve il problema della ripetibilità. Ricreare una risorsa identica nel tempo è difficile se ci si affida alla memoria per configurazione e ordine di esecuzione. L'IaC rende l'infrastruttura ripetibile, tracciabile, revisionabile e automatizzabile.

## 2. Spiega con parole semplici la differenza tra approccio imperativo e dichiarativo.
**Risposta:**
L'approccio imperativo si concentra sulle azioni da compiere in sequenza per ottenere un risultato. Al contrario quello dichiarativo si concentra sulla descrizione dello stato desiderato.

## 3. Perché continuiamo a usare Azure CLI anche se introduciamo IaC?
**Risposta:**
Perché Azure CLI rimane indispensabile per alcune operazioni. Possiamo infatti verificare lo stato effettivo delle risorse, interrogare direttamente Azure, svolgere attività di troubleshooting, preparare prerequisiti e, in generale, controllare in modo indipendente quanto distribuito da Bicep o Terraform.

## 4. Che cos'è Bicep?
**Risposta:**
È un linguaggio dichiarativo con estensione .bicep sviluppato specificamente per Azure. È progettato per descrivere le risorse Azure in modo più leggibile, compatto e semplice rispetto ai tradizionali template ARM JSON.

## 5. Bicep sostituisce Azure Resource Manager?
**Risposta:**
No, non lo sostituisce. Si interfaccia direttamente con Azure Resource Manager e funge da descrizione leggibile che ARM traduce ed esegue tramite i rispettivi Resource Provider.

## 6. Che cosa significa `param location string`?
**Risposta:**
Significa che stiamo dichiarando un parametro chiamato location il cui valore deve essere di tipo string.

## 7. A cosa serve un parametro?
**Risposta:**
Serve a consentire al file di ricevere valori dall'esterno al momento del deployment, rendendo il template flessibile e riutilizzabile in contesti o regioni differenti senza dover modificare il codice della risorsa.

## 8. A cosa servono `@minLength` e `@maxLength`?
**Risposta:**
Sono decorator che impongono regole di validazione sul parametro, nello specifico vincolandone la lunghezza minima e massima dei caratteri accettati.

## 9. Nella riga `resource storage 'Microsoft.Storage/storageAccounts@2023-05-01'`, che cosa significa `storage`?
**Risposta:**
È il nome assegnato alla risorsa all'interno del codice Bicep. Serve per referenziare la risorsa in altre parti del file.

## 10. Che cosa significa `Microsoft.Storage/storageAccounts`?
**Risposta:**
Identifica il tipo di risorsa Azure dove `Microsoft.Storage` è il Resource Provider Azure e `storageAccounts` è il tipo di risorsa gestita dal provider.

## 11. Che cosa significa `@2023-05-01`?
**Risposta:**
Rappresenta la versione dell'API di Azure utilizzata da Bicep e ARM per interpretare lo schema e le proprietà di quella specifica risorsa.

## 12. È la data di creazione dello Storage Account?
**Risposta:**
No, è unicamente la versione del contratto API di Azure.

## 13. Qual è la differenza tra nome simbolico Bicep e nome reale Azure?
**Risposta:**
Il nome simbolico Bicep è un identificatore che esiste solo all'interno del codice per referenziare l'oggetto. Il nome reale Azure, fornito dal parametro, è il nome effettivo che la risorsa assumerà su Azure e che sarà visibile nel Portale e tramite CLI.

## 14. A cosa servono gli output Bicep?
**Risposta:**
Servono a restituire valori calcolati o proprietà generate al termine del deployment, come il nome effettivo. Sono utili per una verifica immediata o come input per task successivi in una pipeline.

## 15. Che cosa fa `az bicep lint`?
**Risposta:**
Effettua un'analisi statica sul codice Bicep prima del deployment. Segnala errori sintattici, deprecazioni o violazioni delle best practice.

## 16. Che cosa fa What-If?
**Risposta:**
Confronta lo stato attuale delle risorse su Azure con quello descritto nel template Bicep e genera una previsione delle modifiche che verrebbero apportate.

## 17. What-If crea realmente le risorse?
**Risposta:**
No, mostra esclusivamente una previsione e non modifica l'infrastruttura reale.

## 18. Qual è la differenza tra What-If e deployment `create`?
**Risposta:**
`what-if` è solo un'anteprima di ciò che cambierebbe senza toccare nulla, mentre `create` applica effettivamente le modifiche creando o aggiornando le risorse reali su Azure.

## 19. Perché Bicep non richiede un file equivalente a `terraform.tfstate`?
**Risposta:**
Perché Bicep si appoggia direttamente ad Azure Resource Manager, il quale mantiene internamente lo stato reale delle risorse nel cloud. Non necessita quindi di un file di stato locale gestito dall'utente.

## 20. Che cos'è Terraform?
**Risposta:**
È uno strumento IaC dichiarativo, open source e multipiattaforma basato sul linguaggio HCL (file con estensione .tf). Grazie al concetto di provider è in grado di gestire infrastrutture su molteplici cloud e su servizi differenti.

## 21. Perché Terraform usa provider?
**Risposta:**
Perché il motore centrale non contiene la logica specifica di ogni singola piattaforma. Esso delega ai provider, sostanzialmente dei plugin, la traduzione dei blocchi nelle chiamate API appropriate per ciascun sistema.

## 22. Che ruolo ha AzureRM?
**Risposta:**
È il provider Terraform ufficiale per Microsoft Azure. Contiene la logica per gestire le risorse Azure come Resource Group, Storage Account, reti, ecc.

## 23. Che cosa contiene `versions.tf`?
**Risposta:**
Dichiara i vincoli sulle versioni. Specifica quale versione minima di Terraform è richiesta (required_version) e quali provider devono essere usati, con relativa sorgente e vincolo di versione (required_providers).

## 24. A cosa serve `providers.tf`?
**Risposta:**
Serve a inizializzare e configurare il provider e il relativo blocco obbligatorio features {}.

## 25. Perché credenziali e codice IaC devono restare separati?
**Risposta:**
Devono essere separati per ragioni di sicurezza. Il codice IaC deve poter essere archiviato e condiviso su repository Git senza esporre password, secret o token. L'autenticazione viene fornita separatamente dall'ambiente di esecuzione locale o dalla pipeline.

## 26. Che cosa contiene `variables.tf`?
**Risposta:**
`variables.tf` contiene la dichiarazione delle variabili di input configurabili.

## 27. Che differenza c'è tra `azurerm_resource_group`, `lab`, `var.resource_group_name` e `rg-ud12-tf`?
**Risposta:**
`azurerm_resource_group` è il tipo di risorsa gestito dal provider Terraform. `lab` è il nome interno assegnato alla risorsa nel codice Terraform. `var.resource_group_name` è la variabile che veicola il nome della risorsa. `rg-ud12-tf` è il nome effettivo che il Resource Group assumerà su Azure.

## 28. Nella riga `resource "azurerm_resource_group" "lab"`, che cos'è `lab`?
**Risposta:**
È il nome dato alla risorsa, utilizzato esclusivamente all'interno della configurazione per referenziare quella specifica risorsa.

## 29. Il Resource Group reale si chiamerà `lab`?
**Risposta:**
No, si chiamerà con il valore assegnato all'attributo name (nel nostro caso rg-ud12-tf).

## 30. Da dove arriva il nome reale `rg-ud12-tf`?
**Risposta:**
Arriva dal valore della variabile var.resource_group_name, il cui default impostato in variables.tf è `rg-ud12-tf`.

## 31. Che cosa significa `azurerm_resource_group.lab.name`?
**Risposta:**
Equivale a dire: prendi la risorsa Terraform di tipo `azurerm_resource_group` con nome `lab` e leggi il valore del suo attributo `name`. In questo caso restituisce `rg-ud12-tf`.

## 32. Perché lo Storage Account usa `azurerm_resource_group.lab.name`?
**Risposta:**
Per ereditare dinamicamente il nome del Resource Group in cui deve essere creato. Evitare di utilizzare stringhe reali ci aiuta a non commettere errori manuali ed esplicita il legame tra le due risorse.

## 33. Che cosa significa `azurerm_resource_group.lab.location`?
**Risposta:**
Significa leggere la proprietà location dal Resource Group `lab`.

## 34. Perché Terraform può dedurre la dipendenza fra Resource Group e Storage Account?
**Risposta:**
Perché lo Storage Account referenzia direttamente gli attributi del Resource Group (.name e .location). Leggendo questi riferimenti, Terraform sa che deve creare prima il Resource Group e solo dopo lo Storage Account.

## 35. A cosa serve la validazione di `storage_account_name`?
**Risposta:**
Serve a bloccare l'esecuzione sul nascere qualora il nome fornito non rispetti i vincoli di Azure (lunghezza tra 3 e 24 caratteri, solo lettere minuscole e numeri), evitando che il comando fallisca a runtime su Azure.

## 36. A cosa servono gli output Terraform?
**Risposta:**
Servono a stampare valori utili alla fine del deployment, per esempio nomi effettivi o endpoint. Questo è utile per consentirne la lettura, la verifica o l'utilizzo da parte di script e pipeline.

## 37. Che cosa fa `terraform init`?
**Risposta:**
Inizializza l'ambiente di lavoro per Terraform. Legge i provider richiesti, scarica i binari dei provider utilizzati, predispone la directory locale `.terraform/` e genera o aggiorna il lockfile. Non crea alcuna risorsa sul cloud.

## 38. Che differenza c'è tra `.terraform/` e `.terraform.lock.hcl`?
**Risposta:**
`.terraform/` è una directory locale che contiene i file binari scaricati dei provider. Non è da versionare. `.terraform.lock.hcl` è il lockfile delle dipendenze che registra le versioni esatte e gli hash crittografici dei provider utilizzati. Garantisce coerenza nelle esecuzioni future, quindi è consigliabile versionarlo.

## 39. Che cosa fa `terraform fmt`?
**Risposta:**
Riformatta automaticamente i file .tf secondo le convenzioni e gli standard ufficiali Terraform, rendendo il codice uniforme e pulito.

## 40. Che cosa fa `terraform validate`?
**Risposta:**
Verifica la correttezza sintattica e la coerenza interna della configurazione.

## 41. Che cosa fa `terraform plan`?
**Risposta:**
Confronta la configurazione desiderata con lo state attuale e con le informazioni lette dal cloud tramite il provider, determinando e mostrando a video cosa verrà creato, modificato o distrutto.

## 42. Perché il piano va letto prima dell'apply?
**Risposta:**
Per confermare in anticipo che le operazioni calcolate da Terraform corrispondano esattamente a ciò che si intende fare, prevenendo cancellazioni involontarie o modifiche distruttive.

## 43. Perché nel LAB salviamo il piano in `ud12.tfplan`?
**Risposta:**
Per garantire che il comando di apply applichi esattamente l'anteprima calcolata e revisionata nel plan.

## 44. Che cosa fa `terraform apply`?
**Risposta:**
Esegue le chiamate API necessarie per allineare l'infrastruttura su Azure a quanto descritto nel piano e aggiorna lo state.

## 45. Che cos'è lo state Terraform?
**Risposta:**
È un database/file `terraform.tfstate` in cui Terraform tiene traccia della configurazione reale distribuita, memorizzando metadati, ID remoti, attributi e corrispondenze tra codice e cloud.

## 46. Quale relazione mantiene lo state?
**Risposta:**
Mantiene il legame tra l'oggetto dichiarato nel codice, per esempio `azurerm_storage_account.lab`, e la risorsa fisica reale esistente in Azure, `stud12t12345678`.

## 47. Perché `terraform.tfstate` non va trattato come normale codice sorgente?
**Risposta:**
Perché non è sorgente ma uno stato dinamico. Inoltre contiene metadati di esecuzione e può contenere dati sensibili in chiaro. Questo lo rende inadatto al versionamento.

## 48. Che cosa mostra `terraform state list`?
**Risposta:**
Elenca tutti gli identificatori delle risorse attualmente tracciate e gestite all'interno di quello specifico state file Terraform.

## 49. `terraform state list` mostra tutte le risorse della Subscription?
**Risposta:**
No. Mostra esclusivamente le risorse gestite da quella specifica configurazione Terraform.

## 50. `terraform destroy` elimina anche i file `.tf`?
**Risposta:**
No. Distrugge le risorse fisiche create su Azure, ma lascia intatto il codice sorgente nei file .tf.

## 51. Qual è la differenza principale nel percorso Bicep→Azure rispetto a Terraform→Azure?
**Risposta:**
Bicep non usa provider intermedi o file di state dedicati. È ARM che gestisce lo stato direttamente. Terraform mantiene e consulta un file di stato proprio e traduce le chiamate tramite il provider AzureRM.

## 52. In che cosa What-If e Plan sono simili?
**Risposta:**
Hanno lo stesso scopo concettuale. Mostrano in anteprima quali risorse verranno create, modificate o eliminate prima di dare avvio all'esecuzione vera e propria.

## 53. Perché non sono lo stesso meccanismo?
**Risposta:**
Perché `what-if` è un motore proprietario di Azure Resource Manager che opera confrontando il template direttamente con il cloud. `plan` è invece il motore interno di Terraform che valuta il codice HCL combinandolo con lo state e con i dati ottenuti dal provider AzureRM.

## 54. In quale tipo di organizzazione Bicep può essere particolarmente naturale?
**Risposta:**
In aziende con infrastruttura prevalentemente o interamente incentrata su Microsoft Azure, Azure DevOps ed Entra ID.

## 55. In quale tipo di organizzazione Terraform può essere particolarmente naturale?
**Risposta:**
In aziende con ambienti multi-cloud o eterogenei (Azure, AWS, GitHub, Cloudflare, VMware), oppure realtà che hanno già standardizzato i loro processi, pipeline e competenze interne su Terraform.

## 56. Perché non ha senso dire in assoluto che uno dei due è sempre migliore?
**Risposta:**
Perché la scelta migliore dipende sempre dal contesto aziendale, dalle piattaforme utilizzate, dalle competenze pregresse del team e dagli standard di governance interni.

## 57. Perché i file IaC devono rimanere nel repository?
**Risposta:**
Perché costituiscono il codice sorgente dell'infrastruttura. Conservarli in Git garantisce tracciabilità, code review tramite Pull Request, riutilizzabilità ed esecuzione automatica in pipeline DevOps.

## 58. Le directory `infra/bicep/` e `infra/terraform/` verranno ricreate da zero in UD13?
**Risposta:**
No. Verranno mantenute ed estese nelle unità successive con nuove configurazioni.

## 59. Perché installare Terraform nel WSL2 è utile per le UD successive?
**Risposta:**
Perché il WSL2 personale funge da host per il self-hosted Agent di Azure DevOps. Installarvi la toolchain assicura che l'Agent abbia i tool necessari per eseguire le future pipeline.

## 60. Su quale componente vengono realmente eseguiti i comandi di una pipeline?
**Risposta:**
Vengono eseguiti su un Agent e nello specifico tramite i tool installati sulla macchina host dell'Agent.

## 61. Che cosa rappresenta il WSL2 del partecipante nel modello self-hosted?
**Risposta:**
Rappresenta un ambiente che simula un build server aziendale dedicato e gestito dall'azienda.

## 62. Qual è la differenza principale fra persistenza self-hosted e ambiente Microsoft-hosted?
**Risposta:**
In una macchina self-hosted i tool installati e le configurazioni restano disponibili nel tempo tra un'esecuzione e l'altra. Una macchina virtuale Microsoft-hosted è temporanea e generata per il singolo job. Viene eliminata al termine dell'esecuzione ripartendo da zero ogni volta, quindi l'ambiente non persiste tra job.

## 63. Perché eliminare le risorse Azure non significa eliminare il codice IaC?
**Risposta:**
Perché le risorse Azure sono l'infrastruttura reale, mentre il codice IaC rappresenta una sorta di blueprint del progetto. Per poter ricreare l'infrastruttura identica in qualsiasi momento, lo conserviamo nel repository Git per essere mantenuto a lungo termine, versionato e riutilizzato.
