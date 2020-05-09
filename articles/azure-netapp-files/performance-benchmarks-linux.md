---
title: Benchmark delle prestazioni Azure NetApp Files per Linux | Microsoft Docs
description: Descrive i benchmark delle prestazioni Azure NetApp Files recapita per Linux.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614590"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Benchmark delle prestazioni Azure NetApp Files per Linux

Questo articolo descrive i benchmark delle prestazioni Azure NetApp Files offre per Linux.

## <a name="linux-scale-out"></a>Scalabilità orizzontale di Linux

Questa sezione descrive i benchmark delle prestazioni della velocità effettiva del carico di lavoro Linux e del carico di lavoro.

### <a name="linux-workload-throughput"></a>Velocità effettiva del carico di lavoro Linux  

Il grafico seguente rappresenta un carico di lavoro sequenziale 64-Kibibyte (KiB) e un working set 1-TiB. Indica che un singolo volume di Azure NetApp Files può gestire tra circa 1.600 scritture sequenziali pure di MiB/s e ~ 4.500 le letture sequenziali pure.  

Il grafico illustra un calo del 10% alla volta, dalla scrittura pura a quella pura. Viene illustrato ciò che è possibile aspettarsi quando si usa un rapporto di lettura/scrittura variabile (100%: 0%, 90%: 10%, 80%: 20% e così via).

![Velocità effettiva del carico di lavoro Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>IOPS del carico di lavoro Linux  

Il grafico seguente rappresenta un carico di lavoro casuale da 4 Kibibyte (KiB) e un working set 1-TiB. Il grafico mostra che un volume di Azure NetApp Files può gestire tra circa 130.000 scritture casuali pure e le letture casuali pure ~ 460.000.  

In questo grafico viene illustrato un calo del 10% alla volta, da letture pure a scritture pure. Viene illustrato ciò che è possibile aspettarsi quando si usa un rapporto di lettura/scrittura variabile (100%: 0%, 90%: 10%, 80%: 20% e così via).

![IOPS del carico di lavoro Linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Scalabilità verticale Linux  

Il kernel Linux 5,3 Abilita la rete con scalabilità orizzontale a client singolo`nconnect`per NFS. I grafici in questa sezione mostrano i risultati dei test di convalida per l'opzione di montaggio sul lato client con NFSv3. La funzionalità è disponibile in SUSE (a partire da SLES12SP4) e Ubuntu (a partire dalla versione 19,10). Si tratta di un concetto simile a SMB multicanale e Oracle Direct NFS.

I grafici confrontano i vantaggi `nconnect` di a un volume montato non connesso. Nei grafici, FIO ha generato il carico di lavoro da una singola istanza di D32s_v3 nell'area di Azure US-West2.

### <a name="linux-read-throughput"></a>Velocità effettiva di lettura Linux  

I grafici seguenti mostrano letture sequenziali di ~ 3.500 MiB/s letture con `nconnect`, approssimativamente 2,3 x non`nconnect`.

![Velocità effettiva di lettura Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Velocità effettiva di scrittura Linux  

I grafici seguenti mostrano Scritture sequenziali. Indicano che `nconnect` non ha vantaggi evidenti per le Scritture sequenziali. 1.500 MiB/s è approssimativamente il limite superiore del volume di scrittura sequenziale e il limite di uscita dell'istanza D32s_v3.

![Velocità effettiva di scrittura Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>IOPS lettura Linux  

I grafici seguenti mostrano letture casuali di ~ 200.000 operazioni di i `nconnect`/o di lettura con`nconnect`, approssimativamente 3x non-.

![IOPS lettura Linux](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>IOPS di scrittura Linux  

I grafici seguenti mostrano scritture casuali di ~ 135.000 operazioni di `nconnect`i/`nconnect`o al secondo con circa 3x non.

![IOPS di scrittura Linux](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Passaggi successivi

- [Azure NetApp Files: sfruttare al meglio l'archiviazione cloud](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
