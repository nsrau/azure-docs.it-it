---
title: Caso di utilizzo - Profilo clienti
description: Informazioni su come utilizzare Data Factory di Azure per creare un flusso di lavoro basato sui dati (pipeline) per analizzare il profilo dei clienti di società di giochi.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e113d2b62311eae0eba091e59edd08ddb8582b1b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="use-case---customer-profiling"></a>Caso di utilizzo - Profilo clienti
Azure Data Factory è uno dei numerosi servizi usati per implementare i Solution Accelerator di Cortana Intelligence Suite.  Per ulteriori informazioni su Cortana Intelligence, vedere [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). In questo documento viene descritto un caso di semplice utilizzo che consente di iniziare a capire come Data Factory di Azure può risolvere problemi comuni di analisi.

## <a name="scenario"></a>Scenario
Contoso è una società di giochi online che crea giochi per più piattaforme: console di gioco, dispositivi palmari e personal computer (PC). Quando gli utenti giocano a questi giochi, vengono prodotti grandi volumi di dati di registro che tengono traccia dei modelli di uso, dello stile di gioco e delle preferenze dell'utente.  Se vengono combinati con dati demografici, regionali e del prodotto, Contoso può eseguire l'analisi per migliorare l'esperienza dei giocatori e indirizzarli ad aggiornamenti e acquisti utili al gioco. 

L'obiettivo di Contoso consiste nell'identificare opportunità di up-selling o cross-selling basate sulla cronologia dei giochi degli utenti e aggiungere nuove funzionalità interessanti per promuovere la crescita del business e fornire un'esperienza migliore ai clienti. In questo caso, usiamo una società di giochi come esempio di società. La società desidera ottimizzare i giochi in base al comportamento dei giocatori. Questi principi si applicano a qualsiasi società che desideri coinvolgere i clienti nei suoi beni e servizi e migliorare la loro esperienza.

In questa soluzione Contoso desidera valutare l'efficacia di una campagna di marketing lanciata di recente. I registri di gioco, inizialmente non elaborati, vengono successivamente elaborati con dati di georilevazione e di riferimento pubblicitario e infine vengono copiati in un database SQL di Azure per analizzare l'impatto della campagna.

## <a name="deploy-solution"></a>Distribuire la soluzione
Per accedere a questo semplice caso d'uso e per provarlo, è sufficiente disporre di una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/), di un [account di archiviazione BLOB di Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account) e di una [database SQL di Azure](../../sql-database/sql-database-get-started.md). Distribuire la pipeline di profilatura dei clienti dal riquadro **Pipeline di esempio** nella home page della data factory.

1. Creare una data factory o aprire una data factory esistente. Per la procedura di creazione di una data factory, vedere [Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
2. Nel pannello **DATA FACTORY** per la data factory, fare clic sul riquadro **Pipeline di esempio**.

    ![Riquadro Pipeline di esempio](./media/data-factory-samples/SamplePipelinesTile.png)
3. Nel pannello **Pipeline di esempio** fare clic sulla **profilatura dei clienti** da distribuire.

    ![Pannello Pipeline di esempio](./media/data-factory-samples/SampleTile.png)
4. Specificare le impostazioni di configurazione per l'esempio, ad esempio il nome e la chiave dell'account di archiviazione di Azure, il nome del server di Azure SQL, il database, l'ID utente e la password.

    ![Pannello Esempio](./media/data-factory-samples/SampleBlade.png)
5. Dopo avere specificato le impostazioni di configurazione, fare clic su **Crea** per creare/distribuire le pipeline di esempio e i servizi collegati o le tabelle usati dalle pipeline.
6. Lo stato della distribuzione sarà visualizzato sul riquadro dell'esempio selezionato in precedenza nel pannello **Pipeline di esempio** .

    ![Stato della distribuzione](./media/data-factory-samples/DeploymentStatus.png)
7. Quando viene visualizzato il messaggio **Distribuzione riuscita** nel riquadro relativo all'esempio, chiudere il pannello **Pipeline di esempio**.  
8. Nel pannello **DATA FACTORY** si potrà notare che i servizi collegati, i set di dati e le pipeline vengono aggiunti alla data factory.  

    ![Pannello Data factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Panoramica della soluzione
Questo semplice caso di utilizzo esemplifica la modalità di utilizzo di Azure Data Factory per inserire, preparare, trasformare, analizzare e pubblicare i dati.

![Flusso di lavoro end-to-end](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Questa figura illustra come vengono visualizzate le pipeline di dati nel portale di Azure dopo la distribuzione.

1. **PartitionGameLogsPipeline** legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB e crea partizioni basate su anno, mese e giorno.
2. **EnrichGameLogsPipeline** unisce gli eventi di giochi partizionati con dati di riferimento del codice geografico e arricchisce i dati eseguendo il mapping di indirizzi IP per le aree geografiche corrispondenti.
3. La pipeline **AnalyzeMarketingCampaignPipeline** usa i dati migliorati e li elabora con i dati pubblicitari per creare l'output finale contenente l'efficacia della campagna di marketing.

In questo esempio Data Factory viene usato per gestire le attività che copiano i dati di input, trasformare ed elaborare i dati e produrre i dati finali per un database SQL di Azure.  È anche possibile visualizzare la rete di pipeline di dati, gestirli e monitorarne lo stato dall'interfaccia utente.

## <a name="benefits"></a>Vantaggi
Per ottimizzare l'analisi del profilo dell'utente e allinearla agli obiettivi aziendali, le società di giochi online sono in grado di raccogliere rapidamente modelli di uso e analizzare l'efficacia delle campagne pubblicitarie.

