---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279192"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definizioni delle tabelle delle dimensioni

- La capacità di archiviazione viene visualizzata in unità di GiB o 1.024^3 byte. Quando si confrontano i dischi misurati in GB (1000 ^ 3 byte) con dischi misurati in GiB (1024 ^ 3), tenere presente che i numeri di capacità specificati in GiB possono apparire più piccoli. Ad esempio, 1023 GiB = 1098,4 GB.
- La velocità effettiva del disco viene misurata in operazioni di input/output al secondo (IOPS) e MBps, dove il valore di MBps corrisponde a 10^6 byte al secondo.
- I dischi dati possono operare in modalità memorizzata nella cache o non memorizzata nella cache. Per il funzionamento dei dischi dati memorizzati nella cache, la modalità di cache host è impostata su **ReadOnly** o su **ReadWrite**.  Per il funzionamento dei dischi dati non memorizzati nella cache, la modalità di cache host è impostata su **None**.
- Per ottenere prestazioni ottimali per le macchine virtuali, è necessario limitare il numero di dischi dati a due dischi per ogni vCPU.
- La larghezza di banda prevista è la larghezza di banda aggregata massima allocata per ogni tipo di VM in tutte le schede di **rete** per tutte le destinazioni. Per altre informazioni, vedere [larghezza di banda di rete della macchina virtuale](../articles/virtual-network/virtual-machine-network-throughput.md).

  I limiti massimi non sono garantiti. I limiti offrono indicazioni per la selezione del tipo di macchina virtuale appropriato per l'applicazione desiderata. Le prestazioni di rete effettive dipenderanno da diversi fattori, tra cui la congestione della rete, i carichi delle applicazioni e le impostazioni di rete. Per informazioni sull'ottimizzazione della velocità effettiva della rete, vedere [ottimizzare la velocità effettiva della rete per le macchine virtuali di Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Per ottenere le prestazioni di rete previste in Linux o Windows, potrebbe essere necessario selezionare una versione specifica o ottimizzare la macchina virtuale. Per ulteriori informazioni, vedere la pagina relativa al [test della larghezza di banda/velocità effettiva (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



