---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523191"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Archiviazione Premium di Azure: progettata per prestazioni elevate

Questo articolo fornisce indicazioni per lo sviluppo di applicazioni a prestazioni elevate mediante l'Archiviazione Premium di Azure. È possibile usare le istruzioni disponibili in questo documento insieme alle procedure consigliate per le prestazioni applicabili alle tecnologie usate dall'applicazione. Per illustrare le indicazioni, è stato usato SQL Server in esecuzione nell'Archiviazione Premium come esempio nell'intero documento.

In questo articolo vengono trattati scenari relativi alle prestazioni per il livello dell'archiviazione, ma sarà necessario ottimizzare il livello dell'applicazione. Ad esempio, se si ospita una farm SharePoint nell'Archiviazione Premium di Azure, è possibile usare gli esempi relativi a SQL Server di questo articolo per ottimizzare il server di database. È anche possibile ottimizzare il server Web e il server applicazioni della farm SharePoint per ottenere prestazioni migliori.

Questo articolo è utile per rispondere alle domande comuni seguenti sull'ottimizzazione delle prestazioni dell'applicazione nell'Archiviazione Premium di Azure:

* Come si misurano le prestazioni dell'applicazione?  
* Perché non si ottengono le prestazioni elevate previste?  
* Quali fattori influenzano le prestazioni dell'applicazione nell'Archiviazione Premium?  
* In che modo questi fattori influenzano le prestazioni dell'applicazione nell'Archiviazione Premium?  
* Come si ottiene l'ottimizzazione per IOPS, larghezza di banda e latenza?  

Queste indicazioni sono specifiche per l'Archiviazione Premium, perché i carichi di lavoro in esecuzione nell'Archiviazione Premium sono influenzati in modo significativo dalle prestazioni. Sono disponibili esempi nei casi appropriati. È anche possibile applicare alcune indicazioni alle applicazioni in esecuzione su VM IaaS con dischi di archiviazione Standard.