# UD06 — Consegna laboratorio guidato

## VM

- image: Ubuntu Server 24.04 LTS
- size: Standard_B2ts_v2 westcentralus
- private IP: 172.16.0.4
- public IP: 20.165.198.7
- NIC: vm-ud06-linux234
- subnet: vnet-ud06/snet-vm
- OS disk: vm-ud06-linux_OsDisk_1_2186d67b4f92417c855136499535ad6e

## Accesso e workload

- SSH: Dentro la VM mi sono collegato tramite ssh ed effettuato i comandi:
Comando: `hostname` Risposta: `vm-ud06-linux`
Comando: `ip addr`
Risposta:
```sh
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether e4:fa:de:02:80:8e brd ff:ff:ff:ff:ff:ff
    inet 172.16.0.4/24 metric 100 brd 172.16.0.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::e6fa:deff:fe02:808e/64 scope link
       valid_lft forever preferred_lft forever
3: enP24251s1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master eth0 state UP group default qlen 1000
    link/ether e4:fa:de:02:80:8e brd ff:ff:ff:ff:ff:ff
    altname enP24251p0s2
```
Comando: `uname -a` Risposta: `Linux vm-ud06-linux 6.17.0-1022-azure #22-Ubuntu SMP Mon Jul 27 17:24:03 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux`
Comando: `df -h`
Risposta:
```sh
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        29G  1.7G   27G   6% /
tmpfs           421M     0  421M   0% /dev/shm
tmpfs           169M  992K  168M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
efivarfs        128K   38K   86K  31% /sys/firmware/efi/efivars
/dev/sda16      881M   64M  756M   8% /boot
/dev/sda15      105M  6.2M   99M   6% /boot/efi
tmpfs            85M   12K   85M   1% /run/user/1000
```
- Nginx: Ho installato nginx tramite `sudo apt install -y nginx`.
- test localhost:
Con `systemctl status` verifico che il servizio `nginx.service` sia avviato.
Con `curl -I http://localhost` testa il server facendo una chiamata locale all'interno della VM.
```sh
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Mon, 14 Sep 2026 12:19:47 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Mon, 14 Sep 2026 12:19:17 GMT
Connection: keep-alive
ETag: "6aa7e645-267"
Accept-Ranges: bytes
```
- test esterno:
Con `curl -I http://$LAB_VM_IP` testa il server facendo una chiamata dall'esterno.
```sh
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Mon, 14 Sep 2026 12:29:47 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Mon, 14 Sep 2026 12:19:17 GMT
Connection: keep-alive
ETag: "6aa7e645-267"
Accept-Ranges: bytes
```
- IP Flow Verify: Deny
- regola responsabile: DenyAllInBound

## Azure Monitor

- metrica VM: Percentage CPU
- intervallo: Ultima ora
- aggregazione: Average
- osservazione: 0.42%

- metrica VM: Network In
- intervallo: Ultima ora
- aggregazione: Sum
- osservazione: 44MiB

- metrica VM: Network Out
- intervallo: Ultima ora
- aggregazione: Average
- osservazione: 6.3MiB


## VMSS / Autoscale

- min: 1
- default: 1
- max: 3
- metrica: Percentage CPU
- soglia: 70 avg 5m
- azione: scale out 1
- perché serve un max: Il limite massimo impedisce un'espansione incontrollata del numero di macchine virtuale, contenendo l'impatto economico.

## App Service

- App Service Plan: plan-ud06-17381
- tier: Free (F1)
- Web App: ud06-web-4481-14762
- hostname: ud06-web-4481-14762.azurewebsites.net
- test HTTPS:
```bash
HTTP/1.1 200 OK
Content-Length: 8480
Content-Type: text/html; charset=utf-8
Date: Mon, 14 Sep 2026 13:13:18 GMT
Accept-Ranges: bytes
Cache-Control: public, max-age=0
ETag: W/"2120-19fb1bcb450"
Last-Modified: Thu, 30 Jul 2026 06:36:02 GMT
Set-Cookie: ARRAffinity=2867cd6b770480db8ef27fa320a4d00181ff932a280fc065596d97644768af1e;Path=/;HttpOnly;Secure;Domain=ud06-web-4481-14762.azurewebsites.net
Set-Cookie: ARRAffinitySameSite=2867cd6b770480db8ef27fa320a4d00181ff932a280fc065596d97644768af1e;Path=/;HttpOnly;SameSite=None;Secure;Domain=ud06-web-4481-14762.azurewebsites.net
X-Powered-By: Express
```

## Scaling App Service

| Modalità | Basata su |
|---|---|
| Manual | Nello scaling manuale siamo noi a impostare il numero di istanze |
| Azure Monitor Autoscale | Gestito tramite delle regole e soglie esplicite impostate dall'amministratore |
| Automatic Scaling | Gestito automaticamente dalla piattaforma |

## Azure Monitor App Service

- metrica: Requests Count 
- osservazione: 2

- metrica: Response Time Average
- osservazione: 152.50 ms

## Backup

- Recovery Services vault: vault949
- frequenza ipotizzata: Daily at 2:00 AM UTC, una frequenza giornaliera notturna evita carichi sull'applicazione e garantisce un RPO di 24 ore.
- retention: 30 giorni, sono un buon intervallo di tempo per identificare e ripristinare modifiche errate.
- recovery point: vm-ud06-linux_OsDisk_1_2186d67b4f92417c855136499535ad6e
- backup reale avviato?: no — non previsto nella UD

## HA / Backup / DR

- Scenario A: High Availability
- Scenario B: Backup
- Scenario C: Disaster Recovery

## Cleanup

- Resource Group eliminato:
- `az group exists`:
