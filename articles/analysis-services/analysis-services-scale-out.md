---
title: Ridimensionamento orizzontale di Azure Analysis Services | Microsoft Docs
description: Replicare server di Azure Analysis Services con ridimensionamento orizzontale
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 213a695d99c50cea5962237c6210e6efcdbc5f6a
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411680"
---
# <a name="azure-analysis-services-scale-out"></a>Ridimensionamento orizzontale di Azure Analysis Services

Con il ridimensionamento orizzontale le query client possono essere distribuite tra più *repliche di query* in un pool di query, riducendo i tempi di risposta durante l'esecuzione di carichi di lavoro di query elevati. È anche possibile separare l'elaborazione dal pool di query, assicurando così che le prestazioni delle query dei client non vengano influenzate negativamente dalle operazioni di elaborazione. Il ridimensionamento orizzontale può essere configurato nel portale di Azure o tramite l'API REST di Analysis Services.

## <a name="how-it-works"></a>Funzionamento

In una distribuzione di server tipica, un server funge sia da server di elaborazione che da server di query. Se il numero di query client verso i modelli presenti nel server supera il numero di unità di elaborazione query (QPU, Query Processing Unit) del piano del server o se l'elaborazione dei modelli avviene contemporaneamente a carichi di lavoro di query elevati, le prestazioni possono peggiorare. 

Con il ridimensionamento orizzontale è possibile creare un pool di query con un massimo di sette risorse di replica di query aggiuntive (otto in totale, incluso il server). È possibile ridimensionare il numero di repliche di query per soddisfare le richieste di QPU nei momenti critici ed è possibile separare un server di elaborazione dal pool di query in qualsiasi momento. Tutte le repliche di query vengono create nella stessa area del server.

Indipendentemente dal numero di repliche di query presenti in un pool di query, i carichi di lavoro di elaborazione non sono distribuiti tra le repliche di query. Un unico server funge da server di elaborazione. Le repliche di query servono solo le query per i modelli sincronizzati tra ogni replica nel pool di query. 

Quando si esegue il ridimensionamento orizzontale, le nuove repliche delle query vengono aggiunte al pool di query in modo incrementale. Per includere le nuove risorse di replica delle query nel pool di query possono essere necessari fino a 5 minuti. Quando tutte le nuove repliche delle query sono attive e in esecuzione, le nuove connessioni client vengono sottoposte al bilanciamento del carico in tutte le risorse del pool di query. Le connessioni client esistenti non vengono modificate dalla risorsa alla quale sono attualmente connesse.  Durante il ridimensionamento verticale, tutte le connessioni client esistenti a una risorsa del pool di query che viene rimossa dal pool di query vengono terminate. Al termine del ridimensionamento verticale le connessioni vengono reindirizzate a una risorsa del pool di query rimanente. Questa operazione può richiedere fino a 5 minuti.

Durante l'elaborazione dei modelli, al termine delle operazioni di elaborazione, è necessario effettuare una sincronizzazione tra il server di elaborazione e le repliche di query. Quando si automatizzano le operazioni di elaborazione, è importante configurare un'operazione di sincronizzazione subito dopo il completamento delle operazioni di elaborazione stesse. La sincronizzazione può essere eseguita manualmente nel portale oppure tramite PowerShell o l'API REST. 

### <a name="separate-processing-from-query-pool"></a>Separare l'elaborazione dal pool di query

Per ottenere prestazioni ottimali sia delle operazioni di elaborazione che delle operazioni di query, è possibile scegliere di separare il server di elaborazione dal pool di query. Quando sono separati, le connessioni client nuove ed esistenti vengono assegnate alle repliche delle query solo nel pool di query. Se le operazioni di elaborazione richiedono pochi minuti, è possibile scegliere di separare il server di elaborazione dal pool di query solo per il tempo necessario per eseguire le operazioni di elaborazione e sincronizzazione e quindi includerlo nuovamente nel pool di query. 

> [!NOTE]
> Il ridimensionamento orizzontale è disponibile per i server del piano tariffario Standard. Ogni replica di query viene fatturata alla stessa tariffa del server.

> [!NOTE]
> Il ridimensionamento orizzontale non aumenta la quantità di memoria disponibile per il server. Per aumentare la memoria, è necessario aggiornare il piano.

## <a name="region-limits"></a>Limiti delle aree

Il numero di repliche di query che è possibile configurare dipende dall'area in cui risiede il server. Per altre informazioni, vedere [Disponibilità per area geografica](analysis-services-overview.md#availability-by-region).

## <a name="monitor-qpu-usage"></a>Monitorare l'utilizzo di QPU

 Per determinare se per il server è necessario un ridimensionamento orizzontale, monitorare il server nel portale di Azure tramite le metriche. Se le QPU si esauriscono regolarmente, significa che il numero di query verso i modelli supera il limite di QPU per il piano. La metrica relativa alla lunghezza della coda dei processi del pool di query aumenta anche quando il numero di query nella coda del pool di thread di query supera le QPU disponibili. Per altre informazioni, vedere [Monitorare le metriche dei server](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Configurare il ridimensionamento orizzontale

### <a name="in-azure-portal"></a>Nel portale di Azure

1. Nel portale di Azure fare clic su **Aumenta**. Usare il dispositivo di scorrimento per selezionare il numero di server di replica di query. Il numero di repliche scelto viene aggiunto al server esistente.

2. In **Separare il server di elaborazione dal pool di query** selezionare Sì per escludere il server di elaborazione dal server di query. Le connessioni client che usano la stringa di connessione predefinita (senza :rw) vengono reindirizzate alle repliche nel pool di query. 

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

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell

Prima di usare PowerShell, [installare il modulo AzureRM più recente o effettuare l'aggiornamento a questo](https://github.com/Azure/azure-powershell/releases). 

Per impostare il numero di repliche di query, usare [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Specificare il parametro facoltativo `-ReadonlyReplicaCount`.

Per eseguire la sincronizzazione, usare [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>connessioni

Nella pagina Panoramica del server sono presenti due nomi di server. Se il ridimensionamento orizzontale non è stato ancora configurato per un server, entrambi i nomi di server funzionano allo stesso modo. Dopo che per un server è stato configurato il ridimensionamento orizzontale, è necessario specificare il nome del server appropriato a seconda del tipo di connessione. 

Per le connessioni client destinate agli utenti finali, ad esempio Power BI Desktop, Excel e le applicazioni personalizzate, usare il **nome del server**. 

Per SSMS ed SSDT, nonché per le stringhe di connessione in PowerShell, per le app di Funzioni di Azure e AMO, usare il **nome del server di gestione**. Il nome del server di gestione include un qualificatore (lettura e scrittura) `:rw` speciale. Tutte le operazioni di elaborazione si verificano nel server di gestione.

![Nomi dei server](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Risolvere problemi

**Problema:** viene restituito un errore per segnalare che **non è possibile trovare l'istanza del server '\<nome del server>' in modalità di connessione 'ReadOnly'.**

**Soluzione:** quando si seleziona l'opzione **Separare il server di elaborazione dal pool di query**, le connessioni client che usano la stringa di connessione predefinita (senza :rw) vengono reindirizzate alle repliche del pool di query. Se le repliche nel pool di query non sono ancora online perché la sincronizzazione non è stata ancora completata, le connessioni client reindirizzate possono avere esito negativo. Quando si esegue una sincronizzazione, per evitare errori di connessione, nel pool di query devono essere presenti almeno due server. Ogni server viene sincronizzato singolarmente, mentre gli altri rimangono online. Se si sceglie di non tenere il server di elaborazione all'interno del pool di query durante l'elaborazione, è possibile rimuoverlo dal pool per l'elaborazione e quindi riaggiungerlo al termine di questa, ma prima della sincronizzazione. Usare le metriche di memoria e di QPU per monitorare lo stato della sincronizzazione.

## <a name="related-information"></a>Informazioni correlate

[Monitorare le metriche dei server](analysis-services-monitor.md)   
[Gestire Azure Analysis Services](analysis-services-manage.md) 

