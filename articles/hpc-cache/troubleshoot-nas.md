---
title: Risolvere i problemi relativi agli obiettivi di archiviazione NFS della cache HPC di AzureTroubleshoot Azure HPC Cache NFS storage targets
description: Suggerimenti per evitare e correggere errori di configurazione e altri problemi che possono causare errori durante la creazione di una destinazione di archiviazione NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77652087"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Risolvere i problemi relativi alla configurazione NAS e alla destinazione dell'archiviazione NFS

Questo articolo fornisce soluzioni per alcuni errori di configurazione comuni e altri problemi che potrebbero impedire ad Azure HPC Cache di aggiungere un sistema di archiviazione NFS come destinazione di archiviazione.

In questo articolo sono incluse informazioni dettagliate su come controllare le porte e su come abilitare l'accesso root a un sistema NAS. Include inoltre informazioni dettagliate sui problemi meno comuni che potrebbero causare l'esito negativo della creazione della destinazione di archiviazione NFS.

> [!TIP]
> Prima di utilizzare questa guida, leggere [i prerequisiti per le destinazioni di archiviazione NFS.](hpc-cache-prereqs.md#nfs-storage-requirements)

Se la soluzione al problema non è inclusa qui, [aprire un ticket](hpc-cache-support-ticket.md) di supporto in modo che il servizio e il supporto tecnico Microsoft possano collaborare con l'utente per analizzare e risolvere il problema.

## <a name="check-port-settings"></a>Controllare le impostazioni della porta

La cache HPC di Azure richiede l'accesso in lettura/scrittura a diverse porte UDP/TCP nel sistema di archiviazione NAS back-end. Assicurarsi che queste porte siano accessibili sul sistema NAS e che il traffico sia consentito a queste porte attraverso qualsiasi firewall tra il sistema di archiviazione e la subnet della cache. Potrebbe essere necessario collaborare con gli amministratori del firewall e di rete per il data center per verificare questa configurazione.

Le porte sono diverse per i sistemi di storage di fornitori diversi, quindi controlla i requisiti del sistema quando configuri una destinazione di archiviazione.

In generale, la cache deve accedere a queste porte:In general, the cache needs access to these ports:

| Protocollo | Porta  | Service  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | montato   |
| TCP/UDP  | 4047  | status   |

Per apprendere le porte specifiche necessarie ``rpcinfo`` per il sistema, utilizzare il comando seguente. Questo comando seguente elenca le porte e formatta i risultati pertinenti in una tabella. (Utilizzare l'indirizzo IP del sistema al posto del *<storage_IP>* termine.)

È possibile emettere questo comando da qualsiasi client Linux in cui è installata l'infrastruttura NFS. Se si utilizza un client all'interno della subnet del cluster, può anche aiutare a verificare la connettività tra la subnet e il sistema di archiviazione.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Assicurarsi che tutte le porte ``rpcinfo`` restituite dalla query consentano il traffico senza restrizioni dalla subnet della cache HPC di Azure.

Controllare queste impostazioni sia sul NAS stesso che su tutti i firewall tra il sistema di archiviazione e la subnet della cache.

## <a name="check-root-access"></a>Controllare l'accesso root

La cache HPC di Azure deve accedere alle esportazioni del sistema di archiviazione per creare la destinazione di archiviazione. In particolare, mounta le esportazioni come ID utente 0.Specifically, it mounts the exports as user ID 0.

Sistemi di storage diversi utilizzano metodi diversi per consentire questo accesso:Different storage systems use different methods to enable this access:

* I server ``no_root_squash`` Linux in genere ``/etc/exports``si aggiungono al percorso esportato in .
* I sistemi NetApp ed EMC controllano in genere l'accesso con regole di esportazione associate a reti o indirizzi IP specifici.

Se si usano le regole di esportazione, tenere presente che la cache può utilizzare più indirizzi IP diversi dalla subnet della cache. Consentire l'accesso dall'intera gamma di possibili indirizzi IP della subnet.

Collaborare con il fornitore di storage NAS per abilitare il giusto livello di accesso per la cache.

### <a name="allow-root-access-on-directory-paths"></a>Consenti accesso root sui percorsi di directory
<!-- linked in prereqs article -->

Per i sistemi NAS che esportano directory gerarchiche, la cache HPC di Azure richiede l'accesso root a ogni livello di esportazione.

Ad esempio, un sistema potrebbe mostrare tre esportazioni come queste:For example, a system might show three exports like these:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

L'esportazione ``/ifs/accounting/payroll`` è ``/ifs/accounting``figlia ``/ifs/accounting`` di , ed ``/ifs``è essa stessa un figlio di .

Se si ``payroll`` aggiunge l'esportazione come destinazione di archiviazione ``/ifs/`` della cache HPC, la cache viene effettivamente montata e accede alla directory delle retribuzioni da lì. Pertanto, Azure HPC ``/ifs`` Cache deve accedere ``/ifs/accounting/payroll`` alla directory principale per poter accedere all'esportazione.

Questo requisito è correlato al modo in cui la cache indicizza i file ed evita conflitti di file, utilizzando handle di file forniti dal sistema di archiviazione.

Un sistema NAS con esportazioni gerarchiche può fornire handle di file diversi per lo stesso file se il file viene recuperato da esportazioni diverse. Ad esempio, un ``/ifs/accounting`` client potrebbe ``payroll/2011.txt``montare e accedere al file . Un altro ``/ifs/accounting/payroll`` client monta e ``2011.txt``accede al file . A seconda di come il sistema di archiviazione assegna gli handle di file, ``<mount2>/payroll/2011.txt`` questi ``<mount3>/2011.txt``due client potrebbero ricevere lo stesso file con handle di file diversi (uno per e uno per ).

Il sistema di archiviazione back-end mantiene gli alias interni per gli handle di file, ma la cache HPC di Azure non è in grado di stabilire quali handle di file nel relativo indice fanno riferimento allo stesso elemento. Quindi è possibile che la cache può avere scritture diverse memorizzate nella cache per lo stesso file e applicare le modifiche in modo non corretto perché non sa che sono lo stesso file.

Per evitare questo possibile conflitto di file per i file in più esportazioni, la cache HPC di Azure installa automaticamente l'esportazione disponibile più superficiale nel percorso (nell'esempio)``/ifs`` e usa l'handle di file fornito da tale esportazione. Se più esportazioni usano lo stesso percorso di base, la cache HPC di Azure richiede l'accesso root a tale percorso.

## <a name="enable-export-listing"></a>Abilita elenco di esportazione
<!-- link in prereqs article -->

Il NAS deve elencare le proprie esportazioni quando la cache HPC di Azure esegue una query.

Nella maggior parte dei sistemi di archiviazione NFS, è possibile testare questo inviando la query seguente da un client Linux:``showmount -e <storage IP address>``

Se possibile, usare un client Linux dalla stessa rete virtuale della cache.

Se il comando non elenca le esportazioni, la cache avrà problemi di connessione al sistema di archiviazione. Collaborare con il fornitore NAS per abilitare l'elenco di esportazione.

## <a name="adjust-vpn-packet-size-restrictions"></a>Regolare le restrizioni relative alle dimensioni dei pacchetti VPN
<!-- link in prereqs article -->

Se si dispone di una VPN tra la cache e il dispositivo NAS, la VPN potrebbe bloccare pacchetti Ethernet a 1500 byte. Questo problema potrebbe verificarsi se gli scambi di grandi dimensioni tra il NAS e l'istanza della cache HPC di Azure non vengono completati, ma gli aggiornamenti più piccoli funzionano come previsto.

Non c'è un modo semplice per dire se il sistema ha questo problema a meno che non si conoscono i dettagli della configurazione VPN. Ecco alcuni metodi che possono aiutarti a verificare la presenza di questo problema.

* Utilizzare gli sniffer di pacchetti su entrambi i lati della VPN per rilevare quali pacchetti vengono trasferiti correttamente.
* Se la VPN consente i comandi ping, è possibile testare l'invio di un pacchetto di dimensioni intere.

  Eseguire un comando ping sulla VPN al NAS con queste opzioni. (Utilizzare l'indirizzo IP del sistema di archiviazione al posto del *valore>storage_IP<.)*

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Queste sono le opzioni del comando:

  * ``-M do``- Non frammentare
  * ``-c 1``- Invia un solo pacchetto
  * ``-s 1472``- Impostare la dimensione del payload su 1472 byte. Si tratta del payload di dimensioni massime per un pacchetto di 1500 byte dopo aver tenuto conto dell'overhead Ethernet.

  Una risposta con esito positivo ha un aspetto simile al seguente:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Se il ping non riesce con 1472 byte, potrebbe essere necessario configurare il bloccaggio MSS sulla VPN per fare in modo che il sistema remoto rilevi correttamente la dimensione massima del frame. Per ulteriori informazioni, leggere la documentazione relativa ai [parametri IPsec/IKE del gateway VPN.](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)

## <a name="check-for-acl-security-style"></a>Verificare lo stile di protezione ACL

Alcuni sistemi NAS utilizzano uno stile di sicurezza ibrido che combina gli elenchi di controllo di accesso (ACL) con la sicurezza POSIX o UNIX tradizionale.

Se il sistema segnala il proprio stile di protezione come UNIX o POSIX senza includere l'acronimo "ACL", il problema non influisce sull'utente.

Per i sistemi che usano gli ACL, la cache HPC di Azure deve tenere traccia di valori aggiuntivi specifici dell'utente per controllare l'accesso ai file. Questa operazione viene eseguita abilitando una cache di accesso. Non esiste un controllo rivolto all'utente per attivare la cache di accesso, ma è possibile aprire un ticket di supporto per richiedere che sia abilitato per le destinazioni di archiviazione interessate nel sistema di cache.

## <a name="next-steps"></a>Passaggi successivi

Se si verifica un problema che non è stato risolto in questo articolo, [aprire un ticket](hpc-cache-support-ticket.md) di supporto per ottenere assistenza da parte di esperti.
