---
title: Annotazioni sulla versione per Application Insights | Documentazione Microsoft
description: Aggiungere indicatori della distribuzione o della build ai grafici di Esplora metriche in Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 58f6603687838713fafbf4cd5cc3f100e22b7401
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993721"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Annotazioni sui grafici delle metriche in Application Insights

Le annotazioni mostrano dove è stata distribuita una nuova compilazione o altri eventi significativi. Le annotazioni consentono di verificare facilmente se le modifiche hanno avuto effetto sulle prestazioni dell'applicazione. Possono essere creati automaticamente dal sistema di compilazione [Azure Pipelines](/azure/devops/pipelines/tasks/) . È anche possibile creare annotazioni da PowerShell per contrassegnare qualsiasi evento.

## <a name="release-annotations-with-azure-pipelines-build"></a>Annotazioni sulla versione con Azure Pipelines compilazione

Le annotazioni sulla versione sono una funzionalità del servizio Azure Pipelines basato sul cloud di Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installare l'estensione Annotazioni (una volta)

Per poter creare annotazioni sulla versione, è necessario installare una delle numerose estensioni DevOps di Azure disponibili nella Visual Studio Marketplace.

1. Accedere al progetto [DevOps di Azure](https://azure.microsoft.com/services/devops/) .
   
1. Nella pagina di [estensione delle annotazioni](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) di Visual Studio Marketplace versione selezionare l'organizzazione DevOps di Azure e quindi selezionare **Installa** per aggiungere l'estensione all'organizzazione DevOps di Azure.
   
   ![Selezionare un'organizzazione di Azure DevOps, quindi selezionare Installa.](./media/annotations/1-install.png)
   
È necessario installare l'estensione una sola volta per l'organizzazione DevOps di Azure. È ora possibile configurare le annotazioni sulla versione per qualsiasi progetto nell'organizzazione.

### <a name="configure-release-annotations"></a>Configurare annotazioni sulla versione

Creare una chiave API separata per ogni modello di versione di Azure Pipelines.

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire la risorsa Application Insights che monitora l'applicazione. In alternativa, se non è presente, [creare una nuova risorsa Application Insights](./app-insights-overview.md).
   
1. Aprire la scheda **accesso all'API** e copiare l' **ID Application Insights**.
   
   ![In accesso all'API copiare l'ID applicazione.](./media/annotations/2-app-id.png)

1. In una finestra del browser separata, aprire o creare il modello di rilascio che gestisce le distribuzioni Azure Pipelines.
   
1. Selezionare **Aggiungi attività**, quindi selezionare l'attività **Application Insights annotazione versione** dal menu.
   
   ![Selezionare Aggiungi attività e selezionare Application Insights annotazione versione.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > L'attività di annotazione versione supporta attualmente solo gli agenti basati su Windows. non verrà eseguito in Linux, macOS o altri tipi di agenti.
   
1. In **ID applicazione** incollare l'ID Application Insights copiato dalla scheda **accesso API** .
   
   ![Incollare l'ID Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Tornare alla finestra di **accesso all'api** Application Insights selezionare **Crea chiave API**. 
   
   ![Nella scheda accesso all'API selezionare Crea chiave API.](./media/annotations/5-create-api-key.png)
   
1. Nella finestra **Crea chiave API** Digitare una descrizione, selezionare **Scrivi annotazioni** e quindi selezionare **Genera chiave**. Copiare la nuova chiave.
   
   ![Nella finestra Crea chiave API digitare una descrizione, selezionare Scrivi annotazioni e quindi selezionare Genera chiave.](./media/annotations/6-create-api-key.png)
   
1. Nella finestra modello di rilascio, nella scheda **variabili** selezionare **Aggiungi** per creare una definizione di variabile per la nuova chiave API.

1. In **nome** immettere `ApiKey` e in **valore** incollare la chiave API copiata dalla scheda **accesso API** .
   
   ![Nella scheda variabili di Azure DevOps selezionare Aggiungi, denominare la variabile ApiKey e incollare la chiave API in valore.](./media/annotations/7-paste-api-key.png)
   
1. Selezionare **Save (Salva** ) nella finestra principale modello di rilascio per salvare il modello.


   > [!NOTE]
   > I limiti per le chiavi API sono descritti nella [documentazione relativa ai limiti di frequenza delle API REST](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Visualizzare le annotazioni


   > [!NOTE]
   > Le annotazioni sulla versione non sono attualmente disponibili nel riquadro metriche di Application Insights

A questo punto, ogni volta che si usa il modello di versione per distribuire una nuova versione, viene inviata un'annotazione a Application Insights. Le annotazioni possono essere visualizzate nei percorsi seguenti:

Il riquadro Usage (utilizzo) in cui è possibile creare manualmente le annotazioni sulla versione:

![Screenshot del grafico a barre con il numero di visite utente visualizzate in un periodo di ore. Le annotazioni di versione vengono visualizzate come segni di spunta verdi sopra il grafico che indica il momento in cui si è verificata una versione](./media/annotations/usage-pane.png)

In qualsiasi query di cartella di lavoro basata su log in cui la visualizzazione Visualizza il tempo lungo l'asse x.

![Screenshot del riquadro cartelle di lavoro con query basate sul log della serie temporale con annotazioni visualizzate](./media/annotations/workbooks-annotations.png)

Per abilitare le annotazioni nella cartella di lavoro, passare a **Impostazioni avanzate** e selezionare **Mostra annotazioni**.

![Screenshot del menu impostazioni avanzate con le parole Mostra le annotazioni evidenziate con un segno di spunta accanto all'impostazione per abilitarla.](./media/annotations/workbook-show-annotations.png)

Selezionare un marcatore di annotazione per aprire i dettagli della versione, inclusi il richiedente, il ramo di controllo del codice sorgente, la pipeline di rilascio e l'ambiente.

## <a name="create-custom-annotations-from-powershell"></a>Creare annotazioni personalizzate da PowerShell
È possibile usare lo script di PowerShell [CreateReleaseAnnotation](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) da GitHub per creare annotazioni da qualsiasi processo, senza usare Azure DevOps. 

1. Creare una copia locale del [CreateReleaseAnnotation.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Usare i passaggi nella procedura precedente per ottenere l'ID Application Insights e creare una chiave API dalla scheda accesso Application Insights **API** .
   
1. Chiamare lo script di PowerShell con il codice seguente, sostituendo i segnaposto tra parentesi angolari con i valori. `-releaseProperties`Sono facoltativi. 
   
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

* [Creare elementi di lavoro](./diagnostic-search.md#create-work-item)
* [Automazione con PowerShell](./powershell.md)

