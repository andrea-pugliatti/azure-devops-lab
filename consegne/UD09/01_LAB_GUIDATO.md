# UD09 — Consegna laboratorio guidato

## Organization

- nome: azdo-andreapugliatti-01
- geography: West Europe
- Azure subscription collegata: sì

## Project

- nome: az900-az104-devops
- visibility: Private
- version control: Git
- process: Agile

## Gruppi

| Gruppo | Funzione essenziale |
|---|---|
| Project Administrators | Hanno il controllo totale a livello di singolo progetto |
| Contributors | È il gruppo operativo principale pensato per sviluppatori e membri del team |
| Readers | Hanno accesso in sola lettura all'intero progetto |
| Build Administrators | Hanno permessi completi di amministrazione specifici per l'area CI/CD (Azure Pipelines) |

## GitHub

- repository: andrea-pugliatti/azure-devops-lab
- repository privato: no
- `gh auth status`: Logged in to github.com account andrea-pugliatti
- `git fetch`: Effettuato
- connessione OAuth/PAT creata in UD09: NO
- metodo raccomandato per CI futura: Azure Pipelines GitHub App

## Parallel jobs

- Microsoft-hosted: MICROSOFT_HOSTED_READY
- esito hosted: Disponibile Free tier 1 parallel job
- self-hosted: Disponibile 1 parallel job
- billing verificato: sì

## Agent Pool

- creato da Project settings: sì
- nome: pool-ud09-wsl
- tipo: Self-hosted
- accesso automatico a tutte le pipeline: sì

## Autenticazione registrazione agent

- metodo usato: PAT / Device Code Flow
- PAT name, se usato: ud09-agent-registration
- PAT scope, se usato:
- expiration: 30 giorni
- inserito in file/repository: NO
- PAT revocato, se usato:

## Agent

- nome: wsl-ud09-andreapugliatti
- pool: pool-ud09-wsl
- OS: Darwin (macOS)
- version: 5.279.0
- status: Idle
- modalità: self-hosted
- credenziale di registrazione chiusa e agent ancora Online: no

## Capability

- Agent.OS: Darwin
- Agent.Version: 5.279.0
- git: /usr/bin/git
- python: `/Users/<omitted>/bin/python3`
- PATH verificato: sì

## Readiness

- Organization: azdo-andreapugliatti-01
- Project: az900-az104-devops
- GitHub: andrea-pugliatti/azure-devops-lab
- hosted: no
- self-hosted: pool-ud09-wsl
- agent: wsl-ud09-andreapugliatti
- security: Check effettuato
