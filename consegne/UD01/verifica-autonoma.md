# Verifica autonoma

La macchina utilizzata si basa su macOS, quindi `cat /etc/os-release` restituisce un errore di file inesistente.
Questa discrepanza evidenzia un tipico errore di contesto, cioè il tentativo di eseguire istruzioni previste per ambiente Linux su un sistema operativo differente. Ciò è verificabile utilizzando il comando uname -a, che ritorna `Darwin Kernel` e la versione utilizzata `27.0.0`.
Il repository locale si trova nel percorso `/Users/<User>/Documents/Code/azure-devops-lab/` mentre il codice si trova su GitHub al remote  https://github.com/andrea-pugliatti/azure-devops-lab.git.
Il flusso di lavoro di Git è organizzato in quattro parti. Il working tree, ovvero la directory fisica sul file system in cui risiedono i file modificabili; la staging area, una zona intermedia che funge da "sala d'attesa" per predisporre le modifiche destinate al salvataggio; i commit locali, che registrano la sequenza cronologica  del progetto completi di hash SHA, autore, timestamp e messaggio; e infine il repository remoto, che custodisce la copia centralizzata e condivisibile su server esterno.
Per verificare l'installazione di Azure CLI è stato utilizzato il comando `az version` ed, infine, è stato verificato che il docente è un collaboratore attivo.
