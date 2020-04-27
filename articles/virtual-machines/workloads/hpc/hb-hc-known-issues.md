---
title: Problemi noti relativi alle VM serie HB e HC-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sui problemi noti relativi alle dimensioni delle macchine virtuali della serie HB in Azure.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707790"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problemi noti relativi alle macchine virtuali delle serie HB e HC

Questo articolo descrive i problemi e le soluzioni più comuni quando si usano VM serie HB e HC.

## <a name="dram-on-hb-series"></a>DRAM su serie HB

Al momento, le VM serie HB possono esporre solo 228 GB di RAM alle macchine virtuali guest. Ciò è dovuto a una limitazione nota dell'hypervisor di Azure per impedire che le pagine vengano assegnate al DRAM locale di AMD CCX (domini NUMA) riservato per la VM guest.

## <a name="accelerated-networking"></a>Rete accelerata

La rete accelerata di Azure non è abilitata in questo momento, ma si procederà durante il periodo di anteprima. I clienti riceveranno una notifica quando questa funzionalità è supportata.

## <a name="qp0-access-restriction"></a>Restrizione di accesso qp0

Per impedire l'accesso hardware di basso livello che può comportare vulnerabilità di sicurezza, la coppia di code 0 non è accessibile alle macchine virtuali guest. Questa operazione influisce solo sulle azioni generalmente associate all'amministrazione della scheda di interfaccia di rete ConnectX-5 e sull'esecuzione di una diagnostica InfiniBand come ibdiagnet, ma non sulle applicazioni dell'utente finale.

## <a name="ud-transport"></a>Trasporto UD

Al momento dell'avvio, la serie HB-and HC non supporta il trasporto con connessione dinamica (DCT). Il supporto per DCT verrà implementato nel tempo. Sono supportati i trasporti Reliable Connection (RC) e datagramma (UD) non affidabili.

## <a name="gss-proxy"></a>Proxy GSS

Il proxy GSS presenta un bug noto in CentOS/RHEL 7,5 che può manifestarsi come una significativa riduzione delle prestazioni e della velocità di risposta quando viene usato con NFS. Questo problema può essere risolto con:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Pulizia della cache

Nei sistemi HPC è spesso utile pulire la memoria al termine di un processo prima che all'utente successivo venga assegnato lo stesso nodo. Dopo l'esecuzione di applicazioni in Linux è possibile che la memoria disponibile si riduca mentre la memoria del buffer aumenta, nonostante non sia in esecuzione alcuna applicazione.

![Screenshot del prompt dei comandi](./media/known-issues/cache-cleaning-1.png)

Con `numactl -H` verrà visualizzato il NUMAnode (probabilmente All) in cui è memorizzata la memoria. In Linux, gli utenti possono pulire le cache in tre modi per restituire la memoria memorizzata nel buffer o memorizzata nella cache su "Free". È necessario avere le autorizzazioni radice o sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot del prompt dei comandi](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avvisi del kernel

Quando si avvia una macchina virtuale della serie HB in Linux, è possibile che vengano visualizzati i messaggi di avviso del kernel seguenti.

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

È possibile ignorare questo avviso. Ciò è dovuto a un limite noto dell'hypervisor di Azure che verrà risolto nel corso del tempo.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sull' [elaborazione ad alte prestazioni](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
