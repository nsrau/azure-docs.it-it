---
title: Ridimensionamento orizzontale di Azure Analysis Services | Microsoft Docs
description: Replicare server di Azure Analysis Services con ridimensionamento orizzontale
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: d00f6bbc285cca028f22ced69ad03d8a2814d76a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="azure-analysis-services-scale-out"></a>Ridimensionamento orizzontale di Azure Analysis Services

Con il ridimensionamento orizzontale le query client possono essere distribuite tra più *repliche di query* in un pool di query, riducendo i tempi di risposta durante l'esecuzione di carichi di lavoro di query elevati. È anche possibile separare l'elaborazione dal pool di query, assicurando così che le prestazioni delle query dei client non vengano influenzate negativamente dalle operazioni di elaborazione. Il ridimensionamento orizzontale può essere configurato nel portale di Azure o tramite l'API REST di Analysis Services.

## <a name="how-it-works"></a>Funzionamento

In una distribuzione di server tipica, un server funge sia da server di elaborazione che da server di query. Se il numero di query client verso i modelli presenti nel server supera il numero di unità di elaborazione query (QPU, Query Processing Unit) del piano del server o se l'elaborazione dei modelli avviene contemporaneamente a carichi di lavoro di query elevati, le prestazioni possono peggiorare. 

Con il ridimensionamento orizzontale è possibile creare un pool di query con un massimo di sette repliche di query aggiuntive (otto in totale, incluso il server). È possibile ridimensionare il numero di repliche di query per soddisfare le richieste di QPU nei momenti critici ed è possibile separare un server di elaborazione dal pool di query in qualsiasi momento. 

Indipendentemente dal numero di repliche di query presenti in un pool di query, i carichi di lavoro di elaborazione non sono distribuiti tra le repliche di query. Un unico server funge da server di elaborazione. Le repliche di query servono solo le query verso i modelli sincronizzati tra ogni replica nel pool di query. 

Al termine delle operazioni di elaborazione, è necessario effettuare una sincronizzazione tra il server di elaborazione e i server delle repliche di query. Quando si automatizzano le operazioni di elaborazione, è importante configurare un'operazione di sincronizzazione subito dopo il completamento delle operazioni di elaborazione stesse. La sincronizzazione può essere eseguita manualmente nel portale oppure tramite PowerShell o l'API REST.

> [!NOTE]
> Il ridimensionamento orizzontale è disponibile per i server del piano tariffario Standard. Ogni replica di query viene fatturata alla stessa tariffa del server.

> [!NOTE]
> Il ridimensionamento orizzontale non aumenta la quantità di memoria disponibile per il server. Per aumentare la memoria, è necessario aggiornare il piano.

## <a name="monitor-qpu-usage"></a>Monitorare l'utilizzo di QPU

 Per determinare se per il server è necessario un ridimensionamento orizzontale, monitorare il server nel portale di Azure tramite le metriche. Se le QPU si esauriscono regolarmente, significa che il numero di query verso i modelli supera il limite di QPU per il piano. La metrica relativa alla lunghezza della coda dei processi del pool di query aumenta anche quando il numero di query nella coda del pool di thread di query supera le QPU disponibili. Per altre informazioni, vedere [Monitorare le metriche dei server](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurare il ridimensionamento orizzontale

### <a name="in-azure-portal"></a>Nel portale di Azure

1. Nel portale di Azure fare clic su **Aumenta**. Usare il dispositivo di scorrimento per selezionare il numero di server di replica di query. Il numero di repliche scelto viene aggiunto al server esistente.

2. In **Separare il server di elaborazione dal pool di query** selezionare Sì per escludere il server di elaborazione dal server di query.

   ![Dispositivo di scorrimento di ridimensionamento orizzontale](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Fare clic su **Salva** per effettuare il provisioning dei nuovi server di replica di query. 

I modelli tabulari nel server primario vengono sincronizzati con i server di replica. Al termine della sincronizzazione, il pool di query inizia la distribuzione delle query in ingresso tra i server di replica. 


## <a name="synchronization"></a>Sincronizzazione 

Quando si esegue il provisioning di nuove repliche di query, Azure Analysis Services replica automaticamente i modelli su tutte le repliche. È anche possibile eseguire una sincronizzazione manuale tramite il portale o l'API REST. Quando si elaborano i modelli, è consigliabile eseguire una sincronizzazione in modo che gli aggiornamenti vengano sincronizzati tra le repliche di query.

### <a name="in-azure-portal"></a>Nel portale di Azure

In **Panoramica** > modello > **Sincronizza modello**.

![Dispositivo di scorrimento di ridimensionamento orizzontale](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>API REST
Usare l'operazione **sync**.

#### <a name="synchronize-a-model"></a>Sincronizzare un modello   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Ottenere lo stato di sincronizzazione  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Per eseguire la sincronizzazione da PowerShell, [eseguire l'aggiornamento alla versione più recente](https://github.com/Azure/azure-powershell/releases) 5.01 o successiva del modulo AzureRM. Usare [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>connessioni

Nella pagina Panoramica del server sono presenti due nomi di server. Se il ridimensionamento orizzontale non è stato ancora configurato per un server, entrambi i nomi di server funzionano allo stesso modo. Dopo che per un server è stato configurato il ridimensionamento orizzontale, è necessario specificare il nome del server appropriato a seconda del tipo di connessione. 

Per le connessioni client destinate agli utenti finali, ad esempio Power BI Desktop, Excel e le applicazioni personalizzate, usare il **nome del server**. 

Per SSMS ed SSDT, nonché per le stringhe di connessione in PowerShell, per le app di Funzioni di Azure e AMO, usare il **nome del server di gestione**. Il nome del server di gestione include un qualificatore (lettura e scrittura) `:rw` speciale. Tutte le operazioni di elaborazione si verificano nel server di gestione.

![Nomi dei server](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Informazioni correlate

[Monitorare le metriche dei server](analysis-services-monitor.md)   
[Gestire Azure Analysis Services](analysis-services-manage.md) 

