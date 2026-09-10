# Consegna UD04 — Laboratorio guidato

## Contesto anonimizzato

- resource group: rg-cea-storage-3a39b252
- storage account: stcea3a39b252
- region: italynorth
- tipo e ridondanza: StorageV2 Standard_LRS

## Servizi e configurazione

| Elemento | Configurazione | Motivazione |
|---|---|---|
| Blob container | documents | Fornisce una separazione dedicata a oggetti non strutturati, consentendo di applicare permessi circoscritti |
| access tier | Hot | Ideale per dati ad accesso frequente |
| accesso pubblico | false | Rispetta la sicurezza di base e il principio del minimo privilegio |
| trasferimento/TLS | Https TLS1.2 | Rispetta lo standard minimo richiesto dalle policy moderne di Azure |

## Autorizzazione e lifecycle

Documenta ruolo dati e scope, differenze tra Entra ID, Shared Key e SAS e configurazione della lifecycle rule senza riportare chiavi, SAS o URL firmate.

Durante il lab abbiamo assegnato il ruolo Storage Blob Data Contributor al nostro utente sul Data plane, necessario per autorizzare le operazioni sui dati tramite identità. Questo è differente dai ruoli del management plane come Contributor. Il ruolo è stato assegnato a livello di singolo Storage Account ($LAB_STORAGE). Questo garantisce il minimo privilegio senza estendere i permessi all'intero Resource Group o alla Subscription.
Entra ID basa l'accesso sull'identità dell'utente e sui ruoli RBAC. Non espone chiavi e offre token a breve scadenza gestiti automaticamente.
La Shared Key concede accesso totale e indiscriminato a tutti i servizi e dati dell'account, senza scadenza automatica, mentre la User Delegation SAS permette un accesso temporaneo delegato e firmato tramite identità Entra ID.
Nel nostro caso la SAS consente solo lettura limitata al singolo file (documento-lab.txt) e scade dopo 30 minuti.
Infine abbiamo creato e abilitato una regola lifecycle chiamata `delete-temporary`, configurata per eliminare i block blob con prefisso `documents/temporary/` dopo un giorno dall'ultima modifica.

## Verifiche, costi e cleanup

Riporta esiti essenziali, principali driver di costo, rimozione del ruolo temporaneo e verifica dell'eliminazione del resource group.

Innanzitutto abbiamo creato uno storage account tramite CLI
```json
{
  "BlobEndpoint": "https://stcea3a39b252.blob.core.windows.net/",
  "Https": true,
  "Kind": "StorageV2",
  "PublicBlob": false,
  "Sku": "Standard_LRS",
  "Tls": "TLS1_2"
}
```
Su portale abbiamo creato un container chiamato `documents`. Abbiamo caricato dei file di testo sia da portale sia da CLI (cmp ha restituito codice zero senza differenze rispetto al file originale). La policy `delete-temporary` è risultata attiva e correttamente configurata sul prefisso `documents/temporary/` con azione di delete a 1 giorno dall'ultima modifica (riscontro positivo tramite `az storage account management-policy show`).
```json
[
  {
    "DeleteAfter": 1.0,
    "Enabled": true,
    "Name": "delete-temporary",
    "Prefixes": [
      "documents/temporary/"
    ]
  }
]
```

I principali driver di costo sono:
- Capacità di archiviazione: Volume di dati effettivamente conservato.
- Livello di ridondanza: Scelta dello SKU (LRS è la configurazione più economica rispetto a ZRS o replica geografica GRS).
- Access Tier: Hot comporta un costo di conservazione per GB più alto rispetto a Cool/Archive, ma costi nettamente inferiori per singola operazione/transazione e nessuna penale di permanenza o tempo di recupero.

Cleanup:
Rimossa l'assegnazione RBAC del ruolo Storage Blob Data Contributor dal portale IAM dello storage account. Rimosse le variabili di sessione e i token dalla shell. È stato eliminato il Resource Group contenente lo storage account e tutti i dati con `az group delete --name "$LAB_RG" --yes --no-wait`
Attesa con `az group wait --name "$LAB_RG" --deleted`
Verifica con `az group exists --name "$LAB_RG"`
Che risponde con `false` 


## Rilevanza professionale

Motiva una scelta tra Blob e Files e il relativo metodo di autorizzazione.

Per Blob Storage il classico scenario può essere un'applicazione web che deve permettere agli utenti di caricare e scaricare dei dati. Per Files sarebbe stato scelto solo nel caso in cui è necessario montare un'unità di rete accessibile con protocolli SMB o NFS. Il metodo di autorizzazione è Microsoft Entra ID tramite ruoli del data plane (Storage Blob Data Contributor o Reader), con User Delegation SAS per accessi esterni temporanei.