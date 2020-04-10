---
title: Annotazioni sulla versione per Application Insights | Documentazione Microsoft
description: Aggiungere indicatori della distribuzione o della build ai grafici di Esplora metriche in Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 0ad773ca6a7102ac718d43dfbbf6a4f834e681a0
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010723"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Annotazioni sui grafici delle metriche in Application Insights

Le annotazioni mostrano dove è stata distribuita una nuova build o altri eventi significativi. Le annotazioni consentono di vedere facilmente se le modifiche hanno avuto un effetto sulle prestazioni dell'applicazione. Possono essere creati automaticamente dal sistema di compilazione [Azure Pipelines.They](https://docs.microsoft.com/azure/devops/pipelines/tasks/) can be automatically created by the Azure Pipelines build system. È anche possibile creare annotazioni da PowerShell per contrassegnare qualsiasi evento.

## <a name="release-annotations-with-azure-pipelines-build"></a>Rilasciare le annotazioni con la compilazione di Azure PipelinesRelease annotations with Azure Pipelines build

Le annotazioni di rilascio sono una funzionalità del servizio Pipeline di Azure basato su cloud di DevOps di Azure.Release annotations are a feature of the cloud-based Azure Pipelines service of Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installare l'estensione Annotazioni (una volta)

Per poter creare annotazioni sulla versione, è necessario installare una delle numerose estensioni DevOps di Azure disponibili in Visual Studio Marketplace.

1. Accedere al progetto DevOps di Azure.Sign in to your [Azure DevOps](https://azure.microsoft.com/services/devops/) project.
   
1. Nella pagina di estensione Annotazioni di rilascio di Visual Studio Marketplace selezionare l'organizzazione DevOps di Azure e quindi selezionare Installa per aggiungere l'estensione all'organizzazione DevOps di Azure.On the Visual Studio Marketplace [Release Annotations extension](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) page, select your Azure DevOps organization, and then select **Install** to add the extension to your Azure DevOps organization.
   
   ![Select an Azure DevOps organization and then select Install.](./media/annotations/1-install.png)
   
È sufficiente installare l'estensione una sola volta per l'organizzazione DevOps di Azure.You only need to install the extension once for your Azure DevOps organization. È ora possibile configurare le annotazioni di rilascio per qualsiasi progetto nell'organizzazione.

### <a name="configure-release-annotations"></a>Configurare annotazioni sulla versione

Creare una chiave API separata per ogni modello di versione delle pipeline di Azure.Create a separate API key for each of your Azure Pipelines release templates.

1. Accedere al [portale](https://portal.azure.com) di Azure e aprire la risorsa Application Insights che esegue il monitoraggio dell'applicazione. In alternativa, se non si dispone di una [risorsa, creare una nuova risorsa di Application Insights.](../../azure-monitor/app/app-insights-overview.md)
   
1. Aprire la scheda **Accesso API** e copiare l'ID di **Application Insights**.
   
   ![In Accesso API copiare l'ID applicazione.](./media/annotations/2-app-id.png)

1. In una finestra del browser separata aprire o creare il modello di rilascio che gestisce le distribuzioni delle pipeline di Azure.In a separate browser window, open or create the release template that manages your Azure Pipelines deployments.
   
1. Selezionare **Aggiungi attività**, quindi selezionare l'attività **Annotazione rilascio di Application Insights** dal menu .
   
   ![Select Add Task and select Application Insights Release Annotation.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > L'attività Rilascia annotazione supporta attualmente solo agenti basati su Windows. non verrà eseguito su Linux, macOS o altri tipi di agenti.
   
1. In **ID applicazione**incollare l'ID di Application Insights copiato dalla scheda Accesso **API.**
   
   ![Incollare l'ID di Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Nella finestra **Accesso API** di Application Insights selezionare Crea **chiave API**. 
   
   ![Nella scheda Accesso API selezionare Crea chiave API.](./media/annotations/5-create-api-key.png)
   
1. Nella finestra **Crea chiave API** digitare una descrizione, selezionare Scrivi **annotazioni**, quindi **selezionare Genera chiave**. Copiare la nuova chiave.
   
   ![Nella finestra Crea chiave API digitare una descrizione, selezionare Scrivi annotazioni e quindi Genera chiave.](./media/annotations/6-create-api-key.png)
   
1. Nella finestra del modello di rilascio, nella scheda **Variabili,** selezionare **Aggiungi** per creare una definizione di variabile per la nuova chiave API.

1. In **Nome** `ApiKey`immettere e in **Valore**incollare la chiave API copiata dalla scheda **Accesso API.**
   
   ![In the Azure DevOps Variables tab, select Add, name the variable ApiKey, and paste the API key under Value.](./media/annotations/7-paste-api-key.png)
   
1. Selezionare **Salva** nella finestra principale del modello di release per salvare il modello.

## <a name="view-annotations"></a>Visualizzare le annotazioni


   > [!NOTE]
   > Le annotazioni di rilascio non sono attualmente disponibili nel riquadro Metriche di Application InsightsRelease annotations are not currently available in the Metrics pane of Application Insights

A questo punto, ogni volta che si usa il modello di versione per distribuire una nuova versione, viene inviata un'annotazione ad Application Insights.Now, whenever you use the release template to deploy a new release, an annotation is sent to Application Insights. Le annotazioni possono essere visualizzate nelle seguenti posizioni:

Riquadro di utilizzo in cui è inoltre possibile creare manualmente le annotazioni di rilascio:The usage pane where you also have the ability to manually create release annotations:

![Screenshot del grafico a barre con il numero di visite utente visualizzate in un periodo di ore. Le annotazioni di rilascio vengono visualizzate come segni di spunta verdi sopra il grafico che indicano il momento in cui si è verificata una release](./media/annotations/usage-pane.png)

In qualsiasi query della cartella di lavoro basata su log in cui la visualizzazione visualizza il tempo lungo l'asse x.

![Screenshot del riquadro delle cartelle di lavoro con query basata su log serie temporali con annotazioni visualizzate](./media/annotations/workbooks-annotations.png)

Per abilitare le annotazioni nella cartella di lavoro, passare a **Impostazioni avanzate** e selezionare **Mostra annotazioni**.

![Screenshot del menu Impostazioni avanzate con le parole che mostrano le annotazioni evidenziate con un segno di spunta accanto all'impostazione per abilitarla.](./media/annotations/workbook-show-annotations.png)

Selezionare un indicatore di annotazione per aprire i dettagli sulla versione, inclusi il richiedente, il ramo del controllo del codice sorgente, la pipeline di rilascio e l'ambiente.

## <a name="create-custom-annotations-from-powershell"></a>Creare annotazioni personalizzate da PowerShell
È possibile usare lo script di PowerShell CreateReleaseAnnotation da GitHub per creare annotazioni da qualsiasi processo desiderato, senza usare DevOps di Azure.You can use the [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell script from GitHub to create annotations from any process you like, without using Azure DevOps. 

1. Creare una copia locale di [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Usare i passaggi della procedura precedente per ottenere l'ID di Application Insights e creare una chiave API dalla scheda Accesso API di Application Insights.Use the steps in the preceding procedure to get your Application Insights ID and create an API key from your Application Insights **API Access** tab.
   
1. Chiamare lo script di PowerShell con il codice seguente, sostituendo i segnaposto tra parentesi angolari con i valori. `-releaseProperties` Sono facoltativi. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

È possibile modificare lo script, ad esempio per creare annotazioni per il passato.

## <a name="next-steps"></a>Passaggi successivi

* [Creare elementi di lavoro](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automazione con PowerShell](../../azure-monitor/app/powershell.md)
