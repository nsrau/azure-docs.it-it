---
title: Risolvere i problemi relativi alle destinazioni di archiviazione NFS di cache HPC
description: Suggerimenti per evitare e correggere errori di configurazione e altri problemi che possono causare un errore durante la creazione di una destinazione di archiviazione NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652087"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Risolvere i problemi di configurazione NAS e di destinazione archiviazione NFS

Questo articolo fornisce soluzioni per alcuni errori di configurazione comuni e altri problemi che possono impedire a cache HPC di Azure di aggiungere un sistema di archiviazione NFS come destinazione di archiviazione.

Questo articolo include informazioni dettagliate su come controllare le porte e come abilitare l'accesso radice a un sistema NAS. Include anche informazioni dettagliate sui problemi meno comuni che potrebbero causare un errore nella creazione della destinazione di archiviazione NFS.

> [!TIP]
> Prima di usare questa guida, leggere [prerequisiti per le destinazioni di archiviazione NFS](hpc-cache-prereqs.md#nfs-storage-requirements).

Se la soluzione per il problema non è inclusa, [aprire un ticket di supporto](hpc-cache-support-ticket.md) in modo che il servizio Microsoft e il supporto tecnico possano collaborare con l'utente per analizzare e risolvere il problema.

## <a name="check-port-settings"></a>Controllare le impostazioni della porta

Per la cache HPC di Azure è necessario l'accesso in lettura/scrittura a diverse porte UDP/TCP sul sistema di archiviazione NAS back-end. Assicurarsi che queste porte siano accessibili nel sistema NAS e che il traffico sia consentito a tali porte attraverso qualsiasi firewall tra il sistema di archiviazione e la subnet della cache. Per verificare questa configurazione, potrebbe essere necessario collaborare con gli amministratori di rete e del firewall per la data center.

Le porte sono diverse per i sistemi di archiviazione di fornitori diversi. Controllare quindi i requisiti del sistema durante la configurazione di una destinazione di archiviazione.

In generale, la cache deve accedere a queste porte:

| Protocollo | Porta  | Service  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | mountd   |
| TCP/UDP  | 4047  | status   |

Per informazioni sulle porte specifiche necessarie per il sistema, usare il comando ``rpcinfo`` seguente. Questo comando seguente elenca le porte e formatta i risultati rilevanti in una tabella. (Usare l'indirizzo IP del sistema al posto del *< storage_IP termine >* .)

È possibile eseguire questo comando da qualsiasi client Linux in cui è installata l'infrastruttura NFS. Se si usa un client all'interno della subnet del cluster, può essere utile anche per verificare la connettività tra la subnet e il sistema di archiviazione.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Assicurarsi che tutte le porte restituite dalla query di ``rpcinfo`` consentano il traffico illimitato dalla subnet della cache HPC di Azure.

Controllare queste impostazioni sia sul NAS che su tutti i firewall tra il sistema di archiviazione e la subnet della cache.

## <a name="check-root-access"></a>Controllare l'accesso alla radice

La cache HPC di Azure deve accedere alle esportazioni del sistema di archiviazione per creare la destinazione di archiviazione. In particolare, monta le esportazioni come ID utente 0.

Sistemi di archiviazione diversi usano metodi diversi per abilitare l'accesso:

* I server Linux aggiungono in genere ``no_root_squash`` al percorso esportato ``/etc/exports``.
* I sistemi NetApp e EMC in genere controllano l'accesso con le regole di esportazione associate a reti o indirizzi IP specifici.

Se si usano le regole di esportazione, tenere presente che la cache può usare più indirizzi IP diversi dalla subnet della cache. Consente l'accesso dalla gamma completa di possibili indirizzi IP della subnet.

Collaborare con il fornitore dell'archiviazione NAS per abilitare il livello di accesso appropriato per la cache.

### <a name="allow-root-access-on-directory-paths"></a>Consenti accesso alla radice nei percorsi di directory
<!-- linked in prereqs article -->

Per i sistemi NAS che esportano directory gerarchiche, la cache HPC di Azure necessita dell'accesso radice a ogni livello di esportazione.

Ad esempio, un sistema potrebbe visualizzare tre esportazioni come le seguenti:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Il ``/ifs/accounting/payroll`` di esportazione è figlio di ``/ifs/accounting``e ``/ifs/accounting`` è figlio di ``/ifs``.

Se si aggiunge l'esportazione ``payroll`` come destinazione di archiviazione della cache HPC, la cache monta effettivamente ``/ifs/`` e accede alla directory Payroll da questa posizione. Per accedere al ``/ifs/accounting/payroll`` Export, quindi, la cache HPC di Azure deve avere accesso alla radice ``/ifs``.

Questo requisito è correlato al modo in cui la cache indicizza i file ed evita i conflitti di file, usando gli handle di file forniti dal sistema di archiviazione.

Un sistema NAS con esportazioni gerarchiche può fornire handle di file diversi per lo stesso file se il file viene recuperato da esportazioni diverse. Ad esempio, un client può montare ``/ifs/accounting`` e accedere al ``payroll/2011.txt``file. Un altro client monta ``/ifs/accounting/payroll`` e accede al ``2011.txt``file. A seconda del modo in cui il sistema di archiviazione assegna gli handle di file, questi due client potrebbero ricevere lo stesso file con handle di file diversi (uno per ``<mount2>/payroll/2011.txt`` e uno per ``<mount3>/2011.txt``).

Il sistema di archiviazione back-end mantiene gli alias interni per gli handle di file, ma la cache HPC di Azure non è in grado di stabilire quali handle di file nel relativo indice fanno riferimento allo stesso elemento. È quindi possibile che nella cache siano presenti scritture diverse memorizzate nella cache per lo stesso file e che le modifiche vengano applicate in modo errato perché non sono in grado di stabilire che si tratta dello stesso file.

Per evitare questo possibile conflitto di file per i file in più esportazioni, cache HPC di Azure monta automaticamente l'esportazione più superficiale disponibile nel percorso (``/ifs`` nell'esempio) e usa l'handle di file fornito da tale esportazione. Se più esportazioni usano lo stesso percorso di base, la cache HPC di Azure richiede l'accesso alla radice di tale percorso.

## <a name="enable-export-listing"></a>Abilita elenco di esportazione
<!-- link in prereqs article -->

Il NAS deve elencare le esportazioni quando la cache HPC di Azure le esegue una query.

Nella maggior parte dei sistemi di archiviazione NFS è possibile testare questa operazione inviando la query seguente da un client Linux: ``showmount -e <storage IP address>``

Usare un client Linux dalla stessa rete virtuale della cache, se possibile.

Se il comando non elenca le esportazioni, la cache avrà problemi di connessione al sistema di archiviazione. Collaborare con il fornitore del NAS per abilitare l'elenco delle esportazioni.

## <a name="adjust-vpn-packet-size-restrictions"></a>Modificare le restrizioni delle dimensioni del pacchetto VPN
<!-- link in prereqs article -->

Se si dispone di una VPN tra la cache e il dispositivo NAS, la VPN potrebbe bloccare i pacchetti Ethernet a 1500 byte a dimensione massima. Questo problema può verificarsi se scambi di grandi dimensioni tra il NAS e l'istanza di cache HPC di Azure non sono completati, ma gli aggiornamenti più piccoli funzionano come previsto.

Non esiste un modo semplice per stabilire se il sistema presenta questo problema, a meno che non si conoscano i dettagli della configurazione VPN. Ecco alcuni metodi che consentono di verificare questo problema.

* Usare gli sniffer di pacchetti su entrambi i lati della VPN per individuare i pacchetti trasferiti correttamente.
* Se la VPN consente i comandi ping, è possibile testare l'invio di un pacchetto di dimensioni complete.

  Eseguire un comando ping sulla VPN per il NAS con queste opzioni. (Usare l'indirizzo IP del sistema di archiviazione al posto del valore *< storage_IP >* .)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Queste sono le opzioni nel comando:

  * ``-M do`` non frammento
  * ``-c 1``-Invia solo un pacchetto
  * ``-s 1472``: impostare la dimensione del payload su 1472 byte. Questo è il payload delle dimensioni massime per un pacchetto di 1500 byte dopo aver contato il sovraccarico Ethernet.

  Una risposta con esito positivo ha un aspetto simile al seguente:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Se il ping ha esito negativo con 1472 byte, potrebbe essere necessario configurare il bloccaggio MSS sulla VPN per fare in modo che il sistema remoto rilevi correttamente le dimensioni massime del frame. Per altre informazioni, vedere la [documentazione relativa ai parametri IPSec/IKE del gateway VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) .

## <a name="check-for-acl-security-style"></a>Verifica lo stile di sicurezza ACL

Alcuni sistemi NAS usano uno stile di sicurezza ibrido che combina gli elenchi di controllo di accesso (ACL) con la protezione tradizionale POSIX o UNIX.

Se il sistema segnala lo stile di sicurezza come UNIX o POSIX senza includere l'acronimo "ACL", questo problema non influisce sull'utente.

Per i sistemi che usano gli ACL, la cache HPC di Azure deve tenere traccia dei valori aggiuntivi specifici dell'utente per controllare l'accesso ai file. Questa operazione viene eseguita abilitando una cache di accesso. Non è disponibile un controllo per l'utente per attivare la cache di accesso, ma è possibile aprire un ticket di supporto per richiedere che sia abilitato per le destinazioni di archiviazione interessate nel sistema di cache.

## <a name="next-steps"></a>Passaggi successivi

Se si riscontra un problema che non è stato risolto in questo articolo, [aprire un ticket di supporto](hpc-cache-support-ticket.md) per ottenere assistenza per gli esperti.
