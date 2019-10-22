---
title: Monitoraggio continuo della pipeline di rilascio di DevOps con Azure Pipelines e applicazione Azure Insights | Microsoft Docs
description: Vengono fornite istruzioni per configurare rapidamente il monitoraggio continuo con Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/16/2019
ms.openlocfilehash: c891cc7564a60dfd665d84d569e266332d255fa9
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677525"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Aggiungere un monitoraggio continuo alla pipeline di versione

Azure Pipelines si integra con applicazione Azure Insights per consentire il monitoraggio continuo della pipeline di rilascio DevOps durante tutto il ciclo di vita di sviluppo del software. 

Con il monitoraggio continuo, le pipeline di versione possono incorporare i dati di monitoraggio da Application Insights e altre risorse di Azure. Quando la pipeline di rilascio rileva un avviso di Application Insights, la pipeline può controllare o eseguire il rollback della distribuzione fino a quando l'avviso non viene risolto. Se vengono superati tutti i controlli, le distribuzioni possono continuare automaticamente da test fino alla produzione, senza la necessità di interventi manuali. 

## <a name="configure-continuous-monitoring"></a>Configurare il monitoraggio continuo

1. In [Azure DevOps](https://dev.azure.com)selezionare un'organizzazione e un progetto.
   
1. Nel menu a sinistra della pagina del progetto selezionare **pipeline**  > **versioni**. 
   
1. A discesa della freccia accanto a **nuovo** e selezionare **nuova pipeline di rilascio**. In alternativa, se non è ancora disponibile una pipeline, selezionare **nuova pipeline** nella pagina visualizzata.
   
1. Nel riquadro **selezionare un modello** cercare e selezionare **app Azure distribuzione del servizio con monitoraggio continuo**, quindi selezionare **applica**. 

   ![Nuova pipeline di versione Azure Pipelines](media/continuous-monitoring/001.png)

1. Nella casella **fase 1** selezionare il collegamento ipertestuale per **visualizzare le attività di fase.**

   ![Visualizzare le attività della fase](media/continuous-monitoring/002.png)

1. Nel riquadro configurazione **fase 1** completare i campi seguenti: 

    | Parametro        | Value |
   | ------------- |:-----|
   | **Nome fase**      | Specificare un nome per la fase o lasciarlo nella **fase 1**. |
   | **Sottoscrizione di Azure** | Elenco a discesa e selezionare la sottoscrizione di Azure collegata che si vuole usare.|
   | **Tipo di app** | Elenco a discesa e selezionare il tipo di app. |
   | **Nome del servizio app** | Immettere il nome del servizio app Azure. |
   | **Nome del gruppo di risorse per Application Insights**    | A discesa e selezionare il gruppo di risorse che si vuole usare. |
   | **Nome risorsa di Application Insights** | A discesa e selezionare la risorsa Application Insights per il gruppo di risorse selezionato.

1. Per salvare la pipeline con le impostazioni predefinite della regola di avviso, fare clic su **Salva** nella parte superiore destra della finestra di Azure DevOps. Immettere un commento descrittivo e quindi fare clic su **OK**.

## <a name="modify-alert-rules"></a>Modificare le regole di avviso

In modalità predefinita, il modello **di distribuzione del servizio app Azure con monitoraggio continuo** prevede quattro regole di avviso: **disponibilità**, **richieste non riuscite**, **tempo di risposta del server**ed eccezioni del **Server**. È possibile aggiungere altre regole o modificare le impostazioni delle regole per soddisfare le esigenze del livello di servizio. 

Per modificare le impostazioni delle regole di avviso:

1. Nel riquadro sinistro della pagina pipeline di rilascio selezionare **configura Application Insights avvisi**.

1. Nel riquadro **avvisi di monitoraggio di Azure** selezionare i puntini di sospensione **...** accanto a **regole di avviso**.
   
1. Nella finestra di dialogo **regole di avviso** selezionare il simbolo a discesa accanto a una regola di avviso, ad esempio **disponibilità**. 
   
1. Modificare la **soglia** e altre impostazioni per soddisfare i requisiti.
   
   ![Modifica avviso](media/continuous-monitoring/003.png)
   
1. Selezionare **OK**e quindi fare clic su **Salva** nella parte superiore destra della finestra di Azure DevOps. Immettere un commento descrittivo e quindi fare clic su **OK**.

## <a name="add-deployment-conditions"></a>Aggiungere le condizioni di distribuzione

Quando si aggiungono i controlli di distribuzione alla pipeline di rilascio, un avviso che supera le soglie impostate impedisce la promozione della versione indesiderata. Una volta risolto l'avviso, la distribuzione può procedere automaticamente.

Per aggiungere le attività di controllo di distribuzione:

1. Nella pagina principale della pipeline, in **fasi**, selezionare il simbolo **condizioni pre-distribuzione** o **condizioni post-distribuzione** , a seconda della fase in cui è necessario un controllo di monitoraggio continuo.
   
   ![Condizioni pre-distribuzione](media/continuous-monitoring/004.png)
   
1. Nel riquadro di configurazione **condizioni pre-distribuzione** impostare **Gates** su **abilitato**.
   
1. Accanto a **Gate di distribuzione**selezionare **Aggiungi**.
   
1. Selezionare **query monitoraggio di Azure avvisi** dal menu a discesa. Questa opzione consente di accedere sia a monitoraggio di Azure che a Application Insights avvisi.
   
   ![Eseguire query sugli avvisi di monitoraggio di Azure](media/continuous-monitoring/005.png)
   
1. In **Opzioni di valutazione**, immettere i valori desiderati per le impostazioni come **l'intervallo tra la rivalutazione delle attività di** controllo e **il timeout dopo il quale i cancelli hanno esito negativo**. 

## <a name="view-release-logs"></a>Visualizza log versione

È possibile visualizzare il comportamento del controllo della distribuzione e altri passaggi della versione nei log delle versioni. Per aprire i log:

1. Selezionare **versioni** dal menu a sinistra della pagina della pipeline. 
   
1. Selezionare qualsiasi versione. 
   
1. In **fasi**selezionare una fase per visualizzare un riepilogo della versione. 
   
1. Per visualizzare i log, selezionare **Visualizza log** nel riepilogo della versione, selezionare il collegamento ipertestuale **riuscito** o **non riuscito** in qualsiasi fase o passare il mouse su una fase e selezionare **log**. 
   
   ![Visualizza log versione](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Azure Pipelines, vedere la [documentazione di Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines).
