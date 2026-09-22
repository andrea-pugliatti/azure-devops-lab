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

## 27.
**Risposta:**

## 28.
**Risposta:**

## 29.
**Risposta:**

## 30.
**Risposta:**

## 31.
**Risposta:**

## 32.
**Risposta:**

## 33.
**Risposta:**

## 34.
**Risposta:**

## 35.
**Risposta:**

## 36.
**Risposta:**

## 37.
**Risposta:**

## 38.
**Risposta:**

## 39.
**Risposta:**

## 40.
**Risposta:**

## 41.
**Risposta:**

## 42.
**Risposta:**

