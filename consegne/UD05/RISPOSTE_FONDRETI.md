1. Qual è la differenza tra switch e router?
Lo switch connette client all'interno della stessa rete. Il router instrada i pacchetti tra reti diverse.
2. Qual è la differenza tra MAC e IP?
Il MAC è l'indirizzo fisico della scheda di rete, l'identificatore. L'IP identifica la posizione del client all'interno della rete.
3. Che cosa significa `/24`?
È la notazione CIDR. Corrisponde alla maschera di rete 255.255.255.0
4. Che cos'è il default gateway?
È il nodo di rete a cui un host invia tutto il traffico indirizzato a destinazioni esterne alla propria subnet locale.
5. A cosa serve DHCP?
Automatizza l'assegnazione della configurazione di rete ai client.
6. A cosa serve DNS?
Traduce i nomi di dominio leggibili nei rispettivi indirizzi IP numerici.
7. Qual è la differenza tra TCP e UDP?
Il TCP (Transmission Control Protocol) è un protocollo di trasmissione orientato alla connessione. Garantisce la consegna dei dati, l'ordine dei pacchetti, la ritrasmissione delle perdite e il controllo di congestione, a fronte di un overhead maggiore. L'UDP (User Datagram Protocol), invece, è un protocollo senza connessione. Invia pacchetti (datagrammi) senza verificare che arrivino o siano nell'ordine corretto, garantendo massima velocità e minima latenza.
8. Che cos'è una porta?
Un identificatore numerico che permette a un singolo host di distinguere a quale specifico processo o servizio di rete recapitare i pacchetti in arrivo.
9. Che cosa fa una route?
È una regola presente all'interno della routing table di un host o router che specifica dove inviare un pacchetto IP. Definisce la rete di destinazione, il next-hop (l'indirizzo del router successivo) o l'interfaccia di rete di uscita da utilizzare.
10. Che cosa fa un firewall?
Ispeziona e controlla il traffico di rete in entrata e in uscita in base a regole di sicurezza predefinite.
11. Che cos'è una VLAN?
Una segmentazione logica di una rete fisica a Livello 2 (standard IEEE 802.1Q). Permette di isolare il traffico e suddividere i domini sullo stesso switch hardware.
12. Perché VLAN e VNet non sono la stessa cosa?
VLAN è un meccanismo di Livello 2 basato sull'inserimento di un tag numerico (VLAN ID) nell'header Ethernet del frame all'interno di un'infrastruttura di rete fisica on-premises.
Il VNet (Virtual Network) è un'astrazione SDN (Software-Defined Networking) tipica del cloud. Lavora a Livello 3 e superiore, incapsulando il traffico tramite protocolli overlay sopra l'infrastruttura fisica del provider.
13. Che cosa fa NAT?
Il NAT (Network Address Translation) modifica l'indirizzo IP nell'header dei pacchetti mentre transitano attraverso un router o firewall. Per esempio consente a centinaia di host con IP privati di condividere un unico indirizzo IP pubblico.
14. Che cosa rappresenta una DMZ?
Una sottorete separata e isolata sia dalla rete interna affidabile (LAN aziendale) sia da quella non fidata (Internet). Ospita i server che devono essere raggiungibili dall'esterno e, in caso di compromissione di un servizio in DMZ, il firewall impedisce all'attaccante di accedere direttamente alla LAN interna.
15. Qual è lo scopo di una VPN?
Una VPN (Virtual Private Nertwork) crea un tunnel cifrato e sicuro sopra una rete non fidata o pubblica. Consente a un dispositivo remoto o a un'intera sede distaccata di comunicare con la rete privata remota con riservatezza, autenticazione e integrità dei dati.
16. Che informazioni fornisce `ipconfig /all`?
Su sistemi Windows, elenca la configurazione di rete dettagliata per tutte le schede fisiche e virtuali.
17. Che cosa verifica `nslookup`?
Interroga direttamente i server DNS per verificare la corretta risoluzione dei nomi a dominio in indirizzi IP (lookup diretto) o viceversa (lookup inverso con record PTR). Permette inoltre di diagnosticare la disponibilità di record specifici (A, AAAA, MX, CNAME, TXT, NS).
18. Perché un DNS funzionante non garantisce la connettività applicativa?
Il DNS si limita a trasformare un nome in un indirizzo IP. Una volta ottenuto l'IP, la connessione effettiva può fallire per numerosi motivi indipendenti dal DNS.
