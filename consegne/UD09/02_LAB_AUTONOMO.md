# UD09 — Consegna laboratorio autonomo

## 1. Organization e Project

- Organization: azdo-andreapugliatti-01
- Project: az900-az104-devops
- Visibility: Private
- Source repository: andrea-pugliatti/azure-devops-lab

1. Evita di esporre pubblicamente le configurazioni, i permessi, gli Agent Pool e le Service Connection (Principio di Least Privilege).
2. Duplicare lo stesso codice sorgente anche su Azure Repos comporterebbe confusione su quale sia il codice valido, sincronizzazione inutile tra due repository differenti e rischio concreto di divergenza della codebase.

## 2. Gruppi

| Gruppo | Scopo | Tutti? |
|---|---|---|
| Project Administrators | Hanno il controllo totale a livello di singolo progetto | No, assegnarlo a tutti darebbe a tutti la capacità di cancellare l'intero progetto |
| Contributors | È il gruppo operativo principale pensato per sviluppatori e membri del team | No, solo al team che lavora attivamente sul progetto |
| Readers | Hanno accesso in sola lettura all'intero progetto | Potenzialmente sì. Essendo un ruolo in sola lettura, non comporta rischi di danneggiamento o alterazione dei dati |
| Build Administrators | Hanno permessi completi di amministrazione specifici per l'area CI/CD (Azure Pipelines) | No, conferisce il controllo totale sull'infrastruttura CI/CD, inclusa la gestione di agenti, retention policy e permessi delle pipeline |

## 3. GitHub integration readiness

- repository: andrea-pugliatti/azure-devops-lab
- private: false
- gh auth: Logged in to github.com account andrea-pugliatti
- git fetch: eseguito
1. In questa UD ci si concentra sulla preparazione dell'infrastruttura di base
2. Per collegare Azure Pipelines a GitHub si userà l'integrazione tramite GitHub App
3. Duplicare lo stesso codice sorgente anche su Azure Repos comporterebbe confusione su quale sia il codice valido, sincronizzazione inutile tra due repository differenti e rischio concreto di divergenza della codebase.

## 4. Parallel jobs

- hosted: 0
- self-hosted: 1
1. No, il corso non è bloccato.
2. Esattamente 1 job alla volta.
3. No, aggiungere agent non aumenta la capacità di concorrenza senza aver prima incrementato i parallel job.

## 5. Agent audit

- pool: pool-ud09-wsl
- agent: wsl-ud09-andreapugliatti
- status: Idle
- version: 5.279.0
- Agent.OS: Darwin
- Agent.Version: 5.279.0
- git: /usr/bin/git
- python: `/Users/<omitted>/bin/python`

## 6. Registration authentication audit

- metodo: PAT
- status PAT, se applicabile: Revoked
1. Il PAT viene utilizzato esclusivamente durante la configurazione iniziale.
2. Richiedere Full access viola il principio fondamentale di least privilege. Per completare la configurazione è sufficiente lo scope `Agent Pools (Read & manage)`.
3. Perché allargare una policy PAT abbassa il livello di sicurezza per tutti.

## 7. Offline/Online

- stop: Effettuato
- stato: Offline
- restart: Effettuato
- stato: Online

## 8. Troubleshooting

1. Verificare che `./run.sh` sia in esecuzione e `Listening for Jobs`.
2. Verificare che l'agente si possa collegare con Azure tramite `curl -I https://dev.azure.com
` .
3. Verificare che l'agente non sia stato configurato con il nome dell'organizzazione sbagliato.
4. Verificare che il pool in cui è stato registrato l'agente sia corretto.
5. Verificare la directory di installazione dell'agente.
6. Eseguire il comando di diagnostica `./run.sh --diagnostics`.

## 9. Readiness

| Controllo | PASS/FAIL | Nota |
|---|---|---|
| Organization | PASS | azdo-andreapugliatti-01 |
| Project | PASS | az900-az104-devops |
| GitHub readiness | PASS | andrea-pugliatti/azure-devops-lab |
| Hosted | PASS | Self-hosted |
| Pool | PASS | pool-ud09-wsl |
| Agent | PASS| wsl-ud09-andreapugliatti |
| PAT / Device Code Flow | PASS | Revoked |
| Capability | PASS | Checked |
| Restart | PASS | Tested |
| Secrets | PASS | Tested |
