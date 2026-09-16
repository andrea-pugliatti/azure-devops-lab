# UD08 — Consegna laboratorio guidato

## Repository

- repository: andrea-pugliatti/azure-devops-lab
- branch principale: main
- `gh auth status` verificato: Logged in to github.com account andrea-pugliatti
- working tree iniziale pulito: nothing to commit, working tree clean

## Collaborazione ricevuta sul mio repository

- collaborator: depoteto
- invito accettato: Sì
- branch contributor: feature/ud08-collab-depoteto
- base branch: main
- numero PR: 1
- URL PR https://github.com/andrea-pugliatti/azure-devops-lab/pull/1
- prima review: Tramite interfaccia
- modifica richiesta: 
Aggiungi una sezione "## Esito" con la frase:
Review completata e modifica corretta.
- nuovo commit: docs: address UD08 review
- approvazione/commento finale: Tramite l'interfaccia
- merge: UD08: collaboration evidence (#1)
- branch remota eliminata: andrea-pugliatti deleted the feature/ud08-collab-depoteto branch
- collaboratore rimosso: Removed depoteto as a collaborator of andrea-pugliatti/azure-devops-lab

## Collaborazione eseguita sul repository altrui

- repository: depoteto/my-new-repo
- branch: feature/ud08-collab-andrea
- numero PR: 1
- prima review ricevuta: 
Aggiungi una sezione "## Esito" con la frase:
Review completata e modifica corretta.
- correzione eseguita: commit "docs: address UD08 review"
- merge completato: UD08: collaboration evidence (#1)

## Conflitto locale

- branch A: lab/conflict-a
- branch B: lab/conflict-b
- file: ud08-conflict.txt
- marker osservati: 
<<<<<<< HEAD
PORT=7000
=======
PORT=9000
>>>>>>> lab/conflict-a
- contenuto finale scelto: PORT=8000
- commit risoluzione: lab: resolve port conflict
- cleanup completato:
Eliminato `ud08-conflict.txt` e branch locali `lab/conflict-a` e `lab/conflict-b`

## Catalogo prodotti

- server avviato: Sì
```
Catalogo prodotti in ascolto su http://127.0.0.1:8000
Ctrl+C per terminare.
```
- `/health`: 200 OK
```json
{
  "status": "ok",
  "service": "catalogo-prodotti",
  "version": "1.0"
}
```
- `/api/products`:
```json
{
    "count": 4,
    "products": [
        {
            "id": "P001",
            "name": "Notebook Pro 14",
            "category": "Notebook",
            "price": 1299.0,
            "stock": 8,
            "stock_status": "OK"
        },
        {
            "id": "P002",
            "name": "Monitor 27 UHD",
            "category": "Monitor",
            "price": 349.0,
            "stock": 4,
            "stock_status": "LOW"
        },
        {
            "id": "P003",
            "name": "Dock USB-C",
            "category": "Accessori",
            "price": 119.0,
            "stock": 15,
            "stock_status": "OK"
        },
        {
            "id": "P004",
            "name": "Keyboard Business",
            "category": "Accessori",
            "price": 59.0,
            "stock": 2,
            "stock_status": "LOW"
        }
    ]
}
```
- `/api/products/P001`: 200 OK
```json
{
  "id": "P001",
  "name": "Notebook Pro 14",
  "category": "Notebook",
  "price": 1299.0,
  "stock": 8,
  "stock_status": "OK"
}
```
- `/api/products/XXX`: 404 Not Found
```json
{
  "error": "product_not_found",
  "product_id": "XXX"
}
```
- frontend browser: Verificato funzionante
- prodotti restituiti: 
P001 	Notebook Pro 14 	Notebook 	€ 1299.00 	8 	OK
P002 	Monitor 27 UHD   	Monitor 	€ 349.00 	4 	LOW
P003 	Dock USB-C      	Accessori 	€ 119.00 	15 	OK
P004 	Keyboard Business 	Accessori 	€ 59.00 	2 	LOW
- prodotto LOW:
Keyboard Business e Monitor 27 UHD

## Architettura

- frontend: static/index.html
- backend/API: server.py
- configurazione: config.json
- dati: data/products.json
- endpoint: `/`, `/health`, `/api/products`, `/api/products/`

## Baseline Git

- commit: "feat: add local product catalog"
- push/PR: No
- stato finale main: 
On branch main
Your branch is ahead of 'origin/main' by 4 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   consegne/UD08/01_LAB_GUIDATO.md

no changes added to commit (use "git add" and/or "git commit -a")
