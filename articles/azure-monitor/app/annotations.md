---
title: Annotazioni sulla versione per Application Insights | Documentazione Microsoft
description: Aggiungere indicatori della distribuzione o della build ai grafici di Esplora metriche in Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604552"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Annotazioni sui grafici delle metriche in Application Insights

Le annotazioni sul [Esplora metriche](../../azure-monitor/app/metrics-explorer.md) grafici mostrano dove è stata distribuita una nuova build o altri eventi significativi. Le annotazioni rendono più semplice verificare se le modifiche hanno avuto effetto sulle prestazioni dell'applicazione. Possono essere create automaticamente per il [pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/) sistema di compilazione. È anche possibile creare annotazioni da PowerShell per contrassegnare qualsiasi evento.

> [!NOTE]
> Questo articolo descrive l'**esperienza classica delle metriche** che è stata deprecata. Le annotazioni sono attualmente disponibili solo nell'esperienza classica e nelle **[cartelle di lavoro](../../azure-monitor/app/usage-workbooks.md)** . Per altre informazioni sull'esperienza di metriche correnti, vedere [avanzate funzionalità di Esplora metriche di Azure](../../azure-monitor/platform/metrics-charts.md).

![Esempi di annotazioni](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Annotazioni sulla versione con le pipeline di Azure build

Le annotazioni sulla versione sono una funzionalità del servizio di pipeline di Azure basato su cloud di Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installare l'estensione Annotazioni (una volta)
Per poter creare le annotazioni sulla versione, è necessario installare una delle molte estensioni per Azure DevOps disponibili in Visual Studio Marketplace.

1. Accedi per i [Azure DevOps](https://azure.microsoft.com/services/devops/) progetto.
   
1. In Visual Studio Marketplace [estensione annotazioni sulla versione](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) pagina, selezionare l'organizzazione di Azure DevOps e quindi selezionare **installare** per aggiungere l'estensione per l'organizzazione di Azure DevOps.
   
   ![Selezionare un'organizzazione di Azure DevOps e quindi scegliere Installa.](./media/annotations/1-install.png)
   
È sufficiente installare l'estensione una sola volta per l'organizzazione di Azure DevOps. È ora possibile configurare le annotazioni sulla versione per qualsiasi progetto all'interno dell'organizzazione.

### <a name="configure-release-annotations"></a>Configurare annotazioni sulla versione

Creare una chiave API separata per ciascuno dei modelli di rilascio pipeline di Azure.

1. Accedi per il [portale di Azure](https://portal.azure.com) e aprire la risorsa di Application Insights che monitora l'applicazione. O se non disponibile, [creare una nuova risorsa di Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Aprire il **l'accesso all'API** scheda e copiare la **ID di Application Insights**.
   
   ![In accesso all'API, copiare l'ID applicazione.](./media/annotations/2-app-id.png)

1. In una finestra del browser separata, aprire o creare il modello di rilascio che gestisce le distribuzioni di pipeline di Azure.
   
1. Selezionare **Aggiungi attività**, quindi selezionare la **annotazione sulla versione di Application Insights** attività dal menu di scelta.
   
   ![Selezionare Aggiungi attività e annotazione sulla versione di Application Insights.](./media/annotations/3-add-task.png)
   
1. Sotto **ID applicazione**, incollare l'ID di Application Insights è stato copiato dalle **l'accesso all'API** scheda.
   
   ![Incollare l'ID di Application Insights](./media/annotations/4-paste-app-id.png)
   
1. In Application Insights **l'accesso all'API** finestra, seleziona **Crea chiave API**. 
   
   ![Nella scheda accesso all'API, selezionare Crea chiave API.](./media/annotations/5-create-api-key.png)
   
1. Nel **Crea chiave API** della finestra, digitare una descrizione, selezionare **scrivere le annotazioni**, quindi selezionare **Genera chiave**. Copiare la nuova chiave.
   
   ![Nella finestra di chiavi API di creazione, digitare una descrizione, selezionare le annotazioni di scrittura, quindi Genera chiave.](./media/annotations/6-create-api-key.png)
   
1. Nella finestra di modello di rilascio, nelle **variabili** scheda, seleziona **Add** per creare una definizione di variabile per la nuova chiave API.

1. Sotto **Name**, immettere `ApiKey`e in **valore**, incollare la chiave API copiata dal **accesso all'API** scheda.
   
   ![Nella scheda variabili di DevOps di Azure, scegliere Aggiungi, nome variabile ApiKey e incollare la chiave API in valore.](./media/annotations/7-paste-api-key.png)
   
1. Selezionare **salvare** nella finestra di modello di rilascio principale per salvare il modello.

## <a name="view-annotations"></a>Visualizzare le annotazioni
A questo punto, quando si usa il modello di rilascio per distribuire una nuova versione, viene inviata un'annotazione ad Application Insights. Le annotazioni visualizzate nei grafici **Esplora metriche**.

Selezionare un marcatore di annotazione (freccia di colore grigio chiaro) per aprire i dettagli sulla versione, tra cui richiedente, ramo di controllo di origine, pipeline di rilascio e ambiente.

![Selezionare un marcatore di annotazione versione.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Creare annotazioni personalizzate da PowerShell
È possibile usare la [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) script di PowerShell da GitHub per creare annotazioni da qualsiasi processo desiderato, senza l'utilizzo di Azure DevOps. 

1. Creare una copia locale di [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Usare i passaggi nella procedura precedente per ottenere l'ID di Application Insights e il nome di una chiave API di Application Insights **l'accesso all'API** scheda.
   
1. Chiamare lo script di PowerShell con il codice seguente, sostituendo i segnaposto racchiusi tra parentesi quadre angolo con i valori. Il `-releaseProperties` sono facoltativi. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

È possibile modificare lo script, ad esempio per creare le annotazioni negli ultimi.

## <a name="next-steps"></a>Passaggi successivi

* [Creare elementi di lavoro](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automazione con PowerShell](../../azure-monitor/app/powershell.md)
