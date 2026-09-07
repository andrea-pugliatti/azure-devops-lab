# Evidenza — Preparazione dell'ambiente e metodo di lavoro

## Ambiente verificato

La postazione utilizza Windows con WSL 2 e una distribuzione Ubuntu supportata. I progetti sono conservati nel filesystem Linux e vengono aperti con Visual Studio Code tramite l'estensione WSL.

La mia postazione utilizza macOS. Le verifica vengono fatte tramite comandi nativi.

| Componente | Versione o stato pubblicabile | Verifica eseguita |
|---|---|---|
| WSL | NA | NA |
| Ubuntu | NA | NA |
| Visual Studio Code | 1.136.1 | Si |
| Git in Ubuntu | 2.54.0 | Si |
| Azure CLI | 2.90.0 | Si |

## Workflow completato

Descrivi in cinque-dieci righe come hai ottenuto il materiale del corso e pubblicato questa evidenza nel repository personale. Integra nella spiegazione la funzione di `git status`, `git add`, `git commit` e `git push`, senza trasformare ciascun comando in una sezione separata.

Tutti gli strumenti erano già installati. Ho clonato il repository tramite git clone. Separatamente ho creato il repository personale su GitHub. Ho clonato anche quest'ultima, ho creato le cartelle evidenze e laboratori e ho aggiunto la nota operativa e la prima evidenza.
Ho installato azure cli e verificato il funzionamento. 

## Verifica finale

Ultimo commit visibile su GitHub:

Stato dell'invito al docente (`inviato` oppure `accettato`): Accettato

```text
<HASH_ABBREVIATO> <MESSAGGIO_COMMIT>
9738072 Completa setup ed evidenza dell'UD01 
```

Problema incontrato o possibile:

Prima verifica diagnostica: 
| Ubuntu | Sì | non rilevata | `/etc/os-release` leggibile |
| Kernel Linux | Sì | 27.0.0 | `uname -r` riuscito |
| Cartella corrente | Sì | `/Users/<USER>/Documents/Code/corso-azure-devops/UD01` | Controllare che il progetto non sia sotto `/mnt/c` |
| Git in Ubuntu | Sì | git version 2.54.0 (Apple Git-157) | Comando disponibile |
| Visual Studio Code da WSL | Sì | 1.136.1 | Verificare anche `code .` |
| Azure CLI | Sì | 2.90.0 | Comando disponibile |
| Docker CLI | Sì | docker version 6.0.2 | Rilevazione soltanto nell’UD01 |
| Python 3 | Sì | Python 3.13.15 | Rilevazione soltanto |
| Identità Git | Sì | nome: configurato; e-mail: configurata | I valori non vengono riportati per privacy |

## Controllo sicurezza

- [X] Non sono presenti password, token, chiavi o codici temporanei.
- [X] Non sono presenti e-mail o nomi utente non necessari.
- [X] Non sono presenti subscription ID o tenant ID.
- [X] Gli screenshot eventuali sono stati controllati e ritagliati.
- [X] Gli output riportati sono limitati alle informazioni utili.
