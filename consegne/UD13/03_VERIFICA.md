# UD13 — Verifica

## 1. Perché lo state locale non è ideale in un team?
In un team lo state locale non offre una sorgente di verità condivisa e si rischiano sovrascritture accidentali da esecuzioni simultanee e confusione su quale sia lo stato reale dell'infrastruttura.

## 2. Che differenza c'è tra stage, job e step?
Uno stage raggruppa una macro-fase logica dell'intero flusso CI/CD. Un job è l'insieme di step assegnato ed eseguito interamente su un singolo agent. Lo step è la singola operazione elementare all'interno di un job.

## 3. Che cosa rappresenta `checkout: self`?
Indica alla pipeline di scaricare nell'area di lavoro dell'agent il repository Git in cui la pipeline stessa è definita.

## 4. Perché la service connection è limitata a `rg-ud13-15-delivery`?
la service connection è limitata a `rg-ud13-15-delivery` per applicare il principio del minimo privilegio. Impedisce che la pipeline abbia accesso all'intera sottoscrizione o ad altri Resource Group.

## 5. Perché usiamo Workload Identity Federation?
Perché elimina la necessità di gestire secret statici, password o PAT all'interno della pipeline.

## 6. Perché in pipeline Terraform viene validato ma non applicato?
Perché non è stato configurato un backend remoto per la gestione condivisa dello state. Eseguire apply in pipeline senza uno state remoto condiviso non ha senso. Inoltre nel corso del lab il primo deploy infrastrutturale reale è affidato a Bicep.

## 7. Quale risorsa deve sopravvivere a UD13 e perché?
L'ACR creato dentro rg-ud13-15-delivery è una dipendenza necessaria per le unità successive. Ospiterà l'immagine creata dal job di CI in UD14 e servirà per il deployment su Container Apps in UD15.

## 8. Se `az bicep lint` fallisce per un file inesistente, quale classe di problema stiamo diagnosticando?
È un errore di file / path del repository. Il file non viene trovato all'interno del workspace di checkout. Non è un problema di Azure, di autenticazione, di agent o di sintassi yaml.

## 9. Perché la prima pipeline UD13 usa il self-hosted Agent?
Per rendere trasparente il legame tra i tool installati localmente nel WSL2, le capabilities/path dell'Agent registrato e i comandi eseguiti dal job, prima di introdurre l'astrazione degli agent cloud.

## 10. A che cosa serve `workspace: clean: all`?
Pulisce il workspace dell'Agent prima dell'esecuzione del job e impedisce che file temporanei o cache rimaste da esecuzioni precedenti facciano superare i controlli a una pipeline in realtà configurata male.

## 11. Distingui la GitHub App dalla Azure Resource Manager service connection.
La GitHub App gestisce l'autenticazione verso il repository sorgente su GitHub per consentire ad Azure Pipelines di effettuare il checkout del codice. L'Azure Resource Manager service connection gestisce l'autenticazione e i permessi RBAC verso la piattaforma cloud Azure per autorizzare l'esecuzione dei comandi infrastrutturali.

## 12. Perché non usiamo `~/workspace/azure-devops-lab` dentro il YAML?
Perché è un percorso personale e assoluto che appartiene all'ambiente locale. Nelle pipeline si devono usare variabili di sistema e percorsi relativi al repository clonato dall'agent.

## 13. Che cosa cambierà quando in UD14 useremo `vmImage: ubuntu-latest`?
Si passerà da un ambiente persistente, cioè il self-hosted agent, a un ambiente Microsoft-hosted, cioè una macchina virtuale temporanea fornita da Azure, istanziata da zero per il singolo job e distrutta al termine dell'esecuzione.
