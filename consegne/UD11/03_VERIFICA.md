# UD11 — Verifica

## Parte A

1. In `myacr.example/catalog-backend:v2`, `catalog-backend` è:
- B. repository
2. Per ottenere il login server ACR corretto è preferibile:
- B. leggerlo con az acr show --query loginServer
3. `az acr login --name` richiede:
- A. il nome della risorsa ACR
4. Una Container Apps revision è:
- B. uno snapshot immutabile di una versione/configurazione della app
5. Una replica è:
- A. una istanza in esecuzione di una revision
6. Per permettere a una Container App di leggere un ACR privato senza password si può usare:
- A. managed identity
7. `AcrPull` fornisce:
- B. pull delle immagini dal registry nel modello RBAC appropriato
8. Con `minReplicas=0`:
- B. la app può scalare a zero repliche quando non necessarie

## Parte B

9. Distingui tag e digest.
Il tag è un'etichetta testuale leggibile e riutilizzabile associata a una versione dell'immagine mentre il digest è un identificatore univoco e immutabile calcolato direttamente sul contenuto dell'immagine.

10. Distingui Container Apps Environment, Container App, revision e replica.
Container Apps Environment è l'ambiente infrastrutturale in cui vivono una o più Container App. La Container App è la definizione dichiarativa dell'applicazione che comprende l'immagine, le risorse, l'ingress, lo scaling e levariabili. Una revision è uno snapshot immutabile della configurazione ad una specifica versione. Una replica è la singola istanza generata da una revision per gestire il traffico.

11. Perché il corso usa managed identity invece di ACR admin credentials?
Per evitare l'uso di credenziali statiche da dover inserire nelle configurazioni, sfruttando l'autenticazione nativa di Entra ID e il controllo degli accessi RBAC secondo il principio del minimo privilegio.

12. Perché una nuova image normalmente produce una nuova revision?
Perché l'immagine fa parte dei parametri con ambito revision-scope. La sua modifica altera la specifica di runtime e Azure crea una nuova versione immutabile.

13. Che cosa deve coincidere tra backend e ingress target port?
La target port configurata a livello di ingress deve corrispondere esattamente alla porta su cui l'applicazione interna al container è in ascolto.

14. Elenca almeno cinque controlli per una Container App non raggiungibile.
- Verificare che l'ingress sia abilitato e impostato su external.
- Verificare che la target port coincida con la porta reale di ascolto del backend.
- Controllare se la revision è attiva e healthy.
- Verificare la presenza e lo stato delle repliche in esecuzione.
- Ispezionare i log per identificare eventuali crash o errori all'avvio.

## Parte C

19. Qual è la causa più probabile e qual è la correzione minima?
Causa probabile: L'ingress tenta di inoltrare il traffico verso la porta 9000, mentre il backend è in ascolto sulla porta 8000.
Correzione minima: Aggiornare l'ingress impostando la targetPort su 8000 con il comando `az containerapp ingress update --name <nome> --resource-group <rg> --target-port 8000`.

20. Quali verifiche eseguiresti dopo la correzione prima di dichiarare risolto il problema?
- Eseguire `az containerapp ingress show` per accertarsi che targetPort sia impostata su 8000.
- Effettuare una chiamata HTTP controllando che risponda con codice 200 OK.

## Cleanup finale

- resource list verificata: sì
- Resource Group eliminato: sì
- `az group exists` = false: sì
- immagini Docker locali UD11 rimosse: sì
- prune globale usato: NO


## 15. Perché il laboratorio UD11 esegue manualmente build, push e deployment invece di partire subito da una pipeline?
Per comprendere le responsabilità di ogni fase del processo prima di automatizzarle. L'automazione deve codificare un flusso già compreso, non sostituirsi alla comprensione del processo.

## 16. Se un registry è configurato in modalità RBAC+ABAC, perché non puoi dare per scontato che `AcrPull` sia il ruolo corretto?
Perché nel modello RBAC+ABAC i ruoli legacy del registry (come AcrPull, AcrPush, AcrDelete) non vengono utilizzati per l'accesso ai repository e sono sostituiti da ruoli specifici come Container Registry Repository Reader.

## 17. In che modo il lavoro manuale di UD11 prepara UD14 e UD15?
Fornisce la sequenza esatta delle operazioni. In UD14 la build e il push su ACR saranno delegati alla pipeline di CI, mentre in UD15 l'aggiornamento della Container App e gli smoke test saranno delegati alla pipeline di CD, usando le stesse logiche utilizzate nell'UD11.

## 18. Distingui system-assigned e user-assigned managed identity.
Una Managed Identity è un’entità di sicurezza registrata in Microsoft Entra ID, un Service Principal, associata a una risorsa Azure e consente ad un'applicazione di autenticarsi in modo sicuro verso altri servizi Azure. Le credenziali crittografiche necessarie per autenticare la risorsa sono generate, custodite e ruotate automaticamente dall’infrastruttura di Azure.
System-assigned significa che l'identità è creata direttamente per la Container App e il suo lifecycle è legato alla risorsa e viene eliminata con essa. User-assigned significa che l'identità è creata come risorsa Azure indipendente, con un ciclo di vita autonomo, e riutilizzabile su più risorse.
