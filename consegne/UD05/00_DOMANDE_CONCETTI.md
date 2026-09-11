# Consegna UD05 — Domande sui concetti

Per ciascuna domanda 1–8 riporta una risposta motivata.

1. Perché il routing del traffico richiede spazi di indirizzi IP univoci. Se i CIDR fossero sovrapposti si creerebbero conflitti di indirizzamento, rendendo impossibile stabilire verso quale delle due reti instradare i pacchetti.
2. È più grande la rete /24. Un prefisso CIDR numericamente più alto indica una rete più piccola, poiché maschera una quantità maggiore di bit. /24 lascia 8 bit per gli host, cioé 256 indirizzi (251 in Azure), mentre /26 contiene solo 64 indirizzi.
3. Avere un IP pubblico non equivale a essere raggiungibile perché esso rappresenta un endpoint effettivo solo se è associato a una risorsa attiva in ascolto, le route consentono il percorso corretto e gli NSG e il firewall del sistema operativo autorizzano il flusso di traffico.
4. Viene scelta in base alla priorità con valori da 100 a 4096. La regola con il numero più basso viene valutata per prima. Al primo match l'elaborazione si interrompe e viene applicata l'azione prevista dalla regola, ignorando le successive.
5. Significa che riconosce lo stato della connessione. Se un flusso è consentito in una direzione, il traffico di risposta appartenente a quello stesso flusso viene automaticamente autorizzato e non richiede una ulteriore regola nella direzione opposta. Ciò, tuttavia, non autorizza nuove connessioni indipendenti.
6. Quando sono presenti entrambi gli NSG, il traffico viene valutato a entrambi i livelli e deve essere consentito da ciascuno di essi. Se la subnet contiene un Deny, il traffico viene bloccato e una regola Allow sulla NIC non ha la capacità di sovrascrivere il divieto imposto dalla subnet.
7. Il DNS ha il solo compito di tradurre i nomi host in indirizzi IP. Per routing si intende l'instradamento dei pacchetti, cioé viene definito il percorso che i pacchetti devono seguire. Una route associa un prefisso di destinazione a un next hop. L'NSG, invece, funge da filtro di sicurezza (firewall). Contiene delle regole che determinano se autorizzare (Allow) o bloccare (Deny) il traffico.
8. Perché IP Flow Verify, che è uno strumento di Network Watcher, richiede una VM attiva su cui verificare il flusso reale del traffico.
