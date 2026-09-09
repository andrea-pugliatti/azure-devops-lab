# Nota operativa — UD01

La cartella di lavoro si trova nel filesystem Linux di WSL 2 ed è stata aperta con Visual Studio Code tramite l'estensione WSL.

Il controllo che ha dimostrato il corretto contesto di esecuzione è:

```bash
pwd
```

L'output indicava un percorso interno alla home Linux e non un percorso `/mnt/c`.

`git status` è il comando più utile incontrato finora. Mostra il branch corrente, elenca le modifiche e mostra i file posizionati nella staging area. Viene raccomandato di eseguirlo sia prima sia dopo git add per verificare in tempo reale l'effetto della selezione.
