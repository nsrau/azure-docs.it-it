---
title: Problemi noti relativi alle macchine virtuali serie HB e HC - Macchine virtuali di Azure Documenti Microsoft
description: Informazioni sui problemi noti relativi alle dimensioni delle macchine virtuali serie HB in Azure.Learn about known issues with HB-series VM sizes in Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707790"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problemi noti relativi alle macchine virtuali delle serie HB e HC

Questo articolo fornisce i problemi e le soluzioni più comuni quando si usano macchine virtuali serie HB e serie HC.

## <a name="dram-on-hb-series"></a>DRAM su serie HB

Le macchine virtuali serie HB possono esporre solo 228 GB di RAM alle macchine virtuali guest in questo momento. Ciò è dovuto a una limitazione nota dell'hypervisor di Azure per impedire l'assegnazione di pagine alla DRAM locale di AMD CCX (domini NUMA) riservata alla macchina virtuale guest.

## <a name="accelerated-networking"></a>Rete accelerata

La rete accelerata di Azure non è abilitata in questo momento, ma procederà nel corso del periodo di anteprima. Invieremo una notifica ai clienti quando questa funzionalità è supportata.

## <a name="qp0-access-restriction"></a>Restrizione di accesso qp0

Per impedire l'accesso hardware di basso livello che può causare vulnerabilità di sicurezza, la coppia 0 della coda non è accessibile alle macchine virtuali guest. Ciò dovrebbe influire solo sulle azioni in genere associate all'amministrazione della scheda di interfaccia di rete ConnectX-5 e all'esecuzione di alcuni diagnostica InfiniBand come ibdiagnet, ma non con le applicazioni dell'utente finale.

## <a name="ud-transport"></a>Trasporto UD

Al momento del lancio, la serie HB e HC non supporta il trasporto connesso a chiave (DCT). Il supporto per DCT verrà implementato nel tempo. Sono supportati i trasporti Reliable Connection (RC) e UNreliable Datagram (UD).

## <a name="gss-proxy"></a>GSS Proxy

Proxy GSS ha un bug noto in CentOS/RHEL 7.5 che può manifestarsi come una significativa penalità di prestazioni e reattività quando viene utilizzato con NFS. Questo può essere mitigato con:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Pulizia cache

Nei sistemi HPC, è spesso utile pulire la memoria al termine di un processo prima che all'utente successivo venga assegnato lo stesso nodo. Dopo aver eseguito le applicazioni in Linux si può scoprire che la memoria disponibile si riduce mentre la memoria buffer aumenta, nonostante non l'esecuzione di tutte le applicazioni.

![Schermata del prompt dei comandi](./media/known-issues/cache-cleaning-1.png)

L'uso `numactl -H` mostrerà con quali NUMAnode la memoria è memorizzata nel buffer (eventualmente tutti). In Linux, gli utenti possono pulire le cache in tre modi per restituire la memoria memorizzata nel buffer o memorizzata nella cache a 'free'. È necessario essere root o avere autorizzazioni sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Schermata del prompt dei comandi](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avvisi del kernel

Quando si avvia una macchina virtuale serie HB in Linux, è possibile che vengano visualizzati i seguenti messaggi di avviso del kernel.

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

È possibile ignorare questo avviso. Ciò è dovuto a una limitazione nota dell'hypervisor di Azure che verrà affrontata nel tempo.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'elaborazione ad alte prestazioni in Azure.Learn more about [high-performance computing](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
