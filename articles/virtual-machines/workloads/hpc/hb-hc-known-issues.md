---
title: Problemi noti con HB-HC-VM serie e - macchine virtuali di Azure | Microsoft Docs
description: Informazioni sui problemi noti relativi a dimensioni delle macchine virtuali serie HB in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707790"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problemi noti relativi alle macchine virtuali delle serie HB e HC

Questo articolo fornisce i problemi e soluzioni più comuni quando si usa HB-HC-VM serie e.

## <a name="dram-on-hb-series"></a>DRAM HB serie

Macchine virtuali della serie HB possono esporre solo 228 GB di RAM per le macchine virtuali guest in questo momento. Si tratta di una limitazione nota dell'hypervisor di Azure per evitare che le pagine vengano assegnate le locale DRAM di AMD CCX (domini NUMA) riservata per la macchina virtuale guest.

## <a name="accelerated-networking"></a>Rete accelerata

Rete accelerata di Azure non è abilitata in questo momento, ma verrà man mano che progrediamo attraverso il periodo di anteprima. I clienti Microsoft informerà quando questa funzionalità è supportata.

## <a name="qp0-access-restriction"></a>Limitazione dell'accesso qp0

Per impedire l'accesso hardware a basso livello che può comportare vulnerabilità di sicurezza, coppie di code 0 non è accessibile alle macchine virtuali guest. Questo dovrebbe interessano solo le azioni in genere associata con l'amministrazione dell'interfaccia di rete ConnectX-5 e l'esecuzione di alcune funzionalità di diagnostica InfiniBand, ad esempio ibdiagnet, ma non le applicazioni degli utenti finali stessi.

## <a name="ud-transport"></a>Trasporto di dominio di aggiornamento

Al momento del lancio, le serie HB e connessione ibrida non supportano in modo dinamico connesso trasporto (DCT). Supporto per DCT verrà implementato nel corso del tempo. Sono supportati i trasporti di connessione (RC) e Datagram inaffidabili (UD) affidabile.

## <a name="gss-proxy"></a>GSS Proxy

Proxy GSS dispone di un bug noto nella 7.5, CentOS/RHEL può risolversi in un significativo delle prestazioni e riduzione della velocità di risposta quando usato con NFS. Ciò può essere attenuata con:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>La pulizia della cache

Nei sistemi HPC, è spesso utile pulire la memoria dopo che un processo è stato completato prima che l'utente successivo viene assegnato lo stesso nodo. Dopo l'esecuzione di applicazioni in Linux si potrebbe scoprire che consente di ridurre la memoria disponibile durante l'aumento della memoria del buffer, nonostante non in esecuzione tutte le applicazioni.

![Screenshot del prompt dei comandi](./media/known-issues/cache-cleaning-1.png)

Usando `numactl -H` mostrerà quali NUMAnode(s) viene memorizzato nel buffer di memoria (possibilmente tutti). In Linux, gli utenti possono pulire le cache in tre modi per restituire memorizzato nel buffer o memorizzato nella cache della memoria per 'gratuito'. È necessario essere radice o disporre delle autorizzazioni di sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot del prompt dei comandi](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avvisi di kernel

Si possono vedere i messaggi di avviso di kernel seguenti quando si avvia una VM di serie HB in Linux.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

È possibile ignorare questo avviso. Ciò è dovuto a una limitazione nota l'hypervisor di Azure che verrà risolto nel corso del tempo.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [high performance computing](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
