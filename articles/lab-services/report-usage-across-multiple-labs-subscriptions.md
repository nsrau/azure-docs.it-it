---
title: Segnalare l'utilizzo di Azure DevTest Labs in più laboratori e sottoscrizioni-Azure | Microsoft Docs
description: Informazioni su come creare report Azure DevTest Labs utilizzo in più laboratori e sottoscrizioni.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: takamath
ms.openlocfilehash: 0d930263233056d8fa74ffe6ccb176ee39429121
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829229"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Segnala l'utilizzo di Azure DevTest Labs tra più laboratori e sottoscrizioni

Molte organizzazioni di grandi dimensioni vogliono tenere traccia dell'utilizzo delle risorse in modo da renderle più efficaci grazie alla visualizzazione delle tendenze e degli outlier nell'utilizzo. In base all'utilizzo delle risorse, i proprietari o i responsabili del Lab possono personalizzare i Lab per [migliorare l'utilizzo delle risorse e i costi](https://docs.microsoft.com/azure/billing/billing-getting-started). In Azure DevTest Labs, è possibile scaricare l'utilizzo delle risorse per Lab consentendo di esaminare in modo più approfondito i modelli di utilizzo. Questi modelli di utilizzo possono contribuire a individuare le modifiche per migliorare l'efficienza. La maggior parte delle aziende desiderano sia l'utilizzo del singolo Lab sia l'utilizzo complessivo in [più laboratori e sottoscrizioni](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

Questo articolo illustra come gestire le informazioni sull'utilizzo delle risorse in più laboratori e sottoscrizioni.

![Generare report sull'utilizzo](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Utilizzo del singolo Lab

Questa sezione illustra come esportare l'utilizzo delle risorse per un singolo Lab.

Prima di poter esportare l'utilizzo delle risorse di DevTest Labs, è necessario configurare un account di archiviazione di Azure per consentire l'archiviazione dei diversi file che contengono i dati di utilizzo. Esistono due modi comuni per eseguire l'esportazione dei dati:

* [API REST di DevTest Labs](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* PowerShell AZ. Resource Module [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) con l'azione di `exportResourceUsage`, l'ID della risorsa lab e i parametri necessari. 

    L'articolo [esportare o eliminare dati personali](personal-data-delete-export.md) contiene uno script di PowerShell di esempio con informazioni dettagliate sui dati esportati. 

    > [!NOTE]
    > Il parametro date non include un timestamp, in modo che i dati includano tutto dalla mezzanotte in base al fuso orario in cui si trova il Lab.

Al termine dell'esportazione, saranno presenti più file CSV nell'archivio BLOB con le diverse informazioni sulle risorse.
  
Attualmente sono disponibili due file CSV:

* *VirtualMachines. csv* : contiene informazioni sulle macchine virtuali nel Lab
* *Disks. csv* : contiene informazioni sui diversi dischi nel Lab 

Questi file vengono archiviati nel contenitore BLOB *labresourceusage* con il nome del Lab, l'ID univoco Lab, la data di esecuzione e la data completa o la data di inizio basata sulla richiesta di esportazione. Una struttura BLOB di esempio è:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Esportazione dell'utilizzo per tutti i Lab

Per esportare le informazioni di utilizzo per più Lab, provare a usare 

* [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/), disponibili in molti linguaggi, tra cui PowerShell o 
* [Runbook di automazione di Azure](https://docs.microsoft.com/azure/automation/), usare PowerShell, Python o una finestra di progettazione grafica personalizzata per scrivere il codice di esportazione.

Utilizzando queste tecnologie, è possibile eseguire le singole esportazioni di Lab in tutti i Lab in una data e a un orario specifici. 

La funzione di Azure deve effettuare il push dei dati nell'archiviazione a lungo termine. Quando si esportano dati per più Lab, l'esportazione potrebbe richiedere del tempo. Per migliorare le prestazioni e ridurre la possibilità di duplicare le informazioni, è consigliabile eseguire ogni Lab in parallelo. Per eseguire il parallelismo, eseguire funzioni di Azure in modo asincrono. È anche possibile usare il trigger timer offerta da funzioni di Azure.

## <a name="using-a-long-term-storage"></a>Uso di un'archiviazione a lungo termine

Un'archiviazione a lungo termine consolida le informazioni di esportazione da Lab diversi in un'unica origine dati. Un altro vantaggio dell'uso dell'archiviazione a lungo termine è la possibilità di rimuovere i file dall'account di archiviazione per ridurre la duplicazione e i costi. 

L'archiviazione a lungo termine può essere usata per eseguire qualsiasi manipolazione del testo, ad esempio: 

* Aggiunta di nomi descrittivi
* creazione di raggruppamenti complessi
* aggregazione dei dati.

Di seguito sono riportate alcune soluzioni di archiviazione comuni: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)e [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). La scelta della soluzione di archiviazione a lungo termine scelta dipende dalla preferenza. È possibile scegliere lo strumento a seconda di ciò che offre in termini di disponibilità dell'interazione quando si visualizzano i dati.

## <a name="visualizing-data-and-gathering-insights"></a>Visualizzazione dei dati e raccolta di informazioni dettagliate

Usare uno strumento di visualizzazione dei dati di propria scelta per connettersi all'archiviazione a lungo termine per visualizzare i dati di utilizzo e raccogliere informazioni dettagliate per verificare l'efficienza dell'utilizzo. È ad esempio possibile utilizzare [Power bi](https://docs.microsoft.com/power-bi/power-bi-overview) per organizzare e visualizzare i dati di utilizzo. 

È possibile usare [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) per creare, collegare e gestire le risorse all'interno di un'unica interfaccia di posizione. Se è necessario un maggiore controllo, la singola risorsa può essere creata all'interno di un singolo gruppo di risorse e gestita in modo indipendente dal servizio Data Factory.  

## <a name="next-steps"></a>Fasi successive

Quando il sistema è configurato e i dati passano all'archiviazione a lungo termine, il passaggio successivo consiste nel trovare le domande a cui i dati devono rispondere. Ad esempio: 

-   Qual è l'utilizzo delle dimensioni della macchina virtuale?

    Gli utenti selezionano dimensioni delle macchine virtuali ad alte prestazioni (più costose)?
-   Quali immagini del Marketplace vengono usate?

    Sono immagini personalizzate la base di VM più comune, dovrebbe essere compilato un archivio immagini comune come [raccolta immagini condivise](../virtual-machines/windows/shared-image-galleries.md) o [Factory di immagini](image-factory-create.md).
-   Quali immagini personalizzate vengono usate o meno?
