---
title: Utilizzo di Azure DevTest Labs in più lab e sottoscrizioniAzure DevTest Labs usage across multiple labs and subscriptions
description: Informazioni su come segnalare l'utilizzo di Azure DevTest Labs in più lab e sottoscrizioni.
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
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169190"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Segnalare l'utilizzo di Azure DevTest Labs in più lab e sottoscrizioniReport Azure DevTest Labs usage across multiple labs and subscriptions

La maggior parte delle organizzazioni di grandi dimensioni desidera tenere traccia dell'utilizzo delle risorse per essere più efficace con tali risorse visualizzando tendenze e outlier nell'utilizzo. In base all'utilizzo delle risorse, i proprietari o i responsabili dei lab possono personalizzare i lab per [migliorare l'utilizzo delle risorse e i costi.](https://docs.microsoft.com/azure/billing/billing-getting-started) In Azure DevTest Labs è possibile scaricare l'utilizzo delle risorse per laboratorio, consentendo un'analisi cronologica più approfondita dei modelli di utilizzo. Questi modelli di utilizzo consentono di individuare le modifiche per migliorare l'efficienza. La maggior parte delle aziende desidera sia l'utilizzo individuale del lab che l'utilizzo complessivo in [più lab e sottoscrizioni.](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 

In questo articolo viene illustrato come gestire le informazioni sull'utilizzo delle risorse in più lab e sottoscrizioni.

![Generare report sull'utilizzo](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Utilizzo del singolo laboratorio

In questa sezione viene illustrato come esportare l'utilizzo delle risorse per un singolo lab.

Prima di poter esportare l'utilizzo delle risorse di DevTest Labs, è necessario configurare un account di Archiviazione di Azure per consentire l'archiviazione dei diversi file che contengono i dati di utilizzo. Esistono due modi comuni per eseguire l'esportazione dei dati:

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* Il modulo Di PowerShell Az.Resource [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) con l'azione di `exportResourceUsage`, l'ID della risorsa lab e i parametri necessari. 

    L'articolo [Esportare o eliminare dati personali](personal-data-delete-export.md) contiene uno script di PowerShell di esempio con informazioni dettagliate sui dati esportati. 

    > [!NOTE]
    > Il parametro date non include un timestamp in modo che i dati includano tutto da mezzanotte in base al fuso orario in cui si trova il lab.

Al termine dell'esportazione, nell'archivio BLOB saranno presenti più file CSV con le diverse informazioni sulle risorse.
  
Attualmente ci sono due file CSV:

* *virtualmachines.csv* - contiene informazioni sulle macchine virtuali nel lab
* *disks.csv* - contiene informazioni sui diversi dischi del laboratorio 

Questi file vengono archiviati nel contenitore BLOB *labresourceusage* sotto il nome del lab, l'ID univoco lab, la data di esecuzione e la data di inizio basata sulla richiesta di esportazione. Una struttura BLOB di esempio potrebbe essere:An example blob structure would be:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Esportazione dell'utilizzo per tutti i laboratori

Per esportare le informazioni sull'utilizzo per più lab, è consigliabile 

* [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/), disponibile in molte lingue, tra cui PowerShell, o 
* [Runbook di Automazione di Azure,](https://docs.microsoft.com/azure/automation/)usare PowerShell, Python o una finestra di progettazione grafica personalizzata per scrivere il codice di esportazione.

Utilizzando queste tecnologie, è possibile eseguire le singole esportazioni lab su tutti i lab in una data e un'ora specifiche. 

La funzione di Azure deve eseguire il push dei dati nell'archiviazione a lungo termine. Quando si esportano dati per più laboratori, l'esportazione potrebbe richiedere del tempo. Per migliorare le prestazioni e ridurre la possibilità di duplicazione delle informazioni, è consigliabile eseguire ogni laboratorio in parallelo. Per eseguire il parallelismo, eseguire Funzioni di Azure in modo asincrono. Sfrutta anche il trigger del timer offerto da Funzioni di Azure.Also take advantage of the timer trigger that Azure Functions offer.

## <a name="using-a-long-term-storage"></a>Utilizzo di un archivio a lungo termine

Un'archiviazione a lungo termine consolida le informazioni di esportazione da laboratori diversi in un'unica origine dati. Un altro vantaggio dell'uso dell'archiviazione a lungo termine è la possibilità di rimuovere i file dall'account di archiviazione per ridurre la duplicazione e i costi. 

L'archiviazione a lungo termine può essere utilizzata per eseguire qualsiasi manipolazione del testo, ad esempio:The long-term storage can be used to do any text manipulation, for example: 

* aggiunta di nomi descrittivi
* creazione di raggruppamenti complessi
* l'aggregazione dei dati.

Alcune soluzioni di archiviazione comuni sono: [SQL Server](https://azure.microsoft.com/services/sql-database/), Azure [Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)e [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). La scelta della soluzione di archiviazione a lungo termine scelta dipende dalle preferenze. Si potrebbe prendere in considerazione la scelta dello strumento a seconda di ciò che offre in termini di disponibilità di interazione durante la visualizzazione dei dati.

## <a name="visualizing-data-and-gathering-insights"></a>Visualizzazione dei dati e raccolta di informazioni dettagliate

Utilizzare uno strumento di visualizzazione dei dati di propria scelta per connettersi all'archiviazione a lungo termine per visualizzare i dati di utilizzo e raccogliere informazioni dettagliate per verificare l'efficienza dell'utilizzo. Ad esempio, [Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) può essere usato per organizzare e visualizzare i dati di utilizzo. 

È possibile usare [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) per creare, collegare e gestire le risorse all'interno di un'unica interfaccia di posizione. Se è necessario un maggiore controllo, la singola risorsa può essere creata all'interno di un singolo gruppo di risorse e gestita indipendentemente dal servizio Data Factory.If greater control is needed, the individual resource can be created within a single resource group and managed independently of the Data Factory service.  

## <a name="next-steps"></a>Passaggi successivi

Una volta che il sistema è impostato e i dati vengono trasferiti all'archiviazione a lungo termine, il passaggio successivo consiste nel trovare le domande a cui i dati devono rispondere. Ad esempio: 

-   Che cos'è l'utilizzo delle dimensioni della macchina virtuale?

    Gli utenti selezionano dimensioni di VM ad alte prestazioni (più costose)?
-   Quali immagini del Marketplace vengono utilizzate?

    Le immagini personalizzate sono la base VM più comune, se un archivio immagini comune deve essere compilato come [Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md) o Image [factory](image-factory-create.md).
-   Quali immagini personalizzate vengono utilizzate o non utilizzate?
