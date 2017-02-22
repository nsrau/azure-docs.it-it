---
title: Elenco delle versioni dello schema di configurazione di Diagnostica di Azure | Documentazione Microsoft
description: "Usato per configurare la raccolta di contatori delle prestazioni in Macchine virtuali, set di scalabilità di macchine virtuali, Service Fabric e Servizi cloud di Azure."
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 2e1bc45b55cd79af8579a5ddaf43cf0db019c92f


---
# <a name="list-of-azure-diagnostics-versions"></a>Elenco delle versioni di Diagnostica di Azure
Questa pagina rappresenta l'indice delle versioni degli schemi di Diagnostica di Azure forniti con Microsoft Azure SDK.  

> [!NOTE]
> Diagnostica di Azure è il componente usato per raccogliere i contatori delle prestazioni e altre statistiche da Macchine virtuali, set di scalabilità di macchine virtuali, Service Fabric e Servizi cloud di Azure.  Questa pagina è utile solo se si usa uno di questi servizi.
>

Lo strumento Diagnostica di Azure viene utilizzato in combinazione con altri prodotti di diagnostica Microsoft, quali Monitoraggio di Azure, Application Insights e Log Analytics.

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Grafico delle versioni fornite per Azure SDK e Diagnostica  

|Versione di Azure SDK | Versione di Diagnostica di Azure | Modello|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | plug-in|  
|2.0 - 2.4         |1.0                         |"|  
|2.5               |1.2                         |estensione|  
|2.6               |1.3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1,5                         |"|  

 Diagnostica di Azure versione 1.0 era inizialmente disponibile in un modello plug-in, vale a dire che con l'installazione del pacchetto Azure SDK si otteneva la versione di Diagnostica di Azure contenuta al suo interno.  

 A partire dall'SDK 2.5 (diagnostica versione 1.2) lo strumento Diagnostica di Azure è passata a un modello di estensione. Gli strumenti per usare le nuove funzionalità erano disponibili solo nei pacchetti Azure SDK più recenti, ma qualsiasi servizio cloud o macchina virtuale che usasse la diagnostica prelevava la versione più recente direttamente da Azure.  

 Gli utenti che ancora usavano SDK 2.5 avevano ad esempio a disposizione Diagnostica di Azure 1.5, a prescindere che usassero o meno le funzionalità più recenti.  

## <a name="azure-diagnostics-schemas-index"></a>Indice degli schemi di Diagnostica di Azure  
[Schema di configurazione di Diagnostica 1.0](azure-diagnostics-schema-1dot0.md)  

[Schema di configurazione di Diagnostica 1.2](azure-diagnostics-schema-1dot2.md)  

[Schema di configurazione di Diagnostica da 1.3 a 1.5](azure-diagnostics-schema-1dot3-to-1dot5.md)  



<!--HONumber=Jan17_HO5-->


