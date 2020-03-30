---
title: Monitoraggio continuo della pipeline di rilascio DevOps con le pipeline di Azure e Azure Application Insights. Documenti Microsoft
description: Fornisce istruzioni per configurare rapidamente il monitoraggio continuo con Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655396"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Aggiungere un monitoraggio continuo alla pipeline di versione

Le pipeline di Azure si integrano con Azure Application Insights per consentire il monitoraggio continuo della pipeline di rilascio DevOps durante il ciclo di vita dello sviluppo software. 

Con il monitoraggio continuo, le pipeline di rilascio possono incorporare dati di monitoraggio da Application Insights e altre risorse di Azure.With continuous monitoring, release pipelines can incorporate monitoring data from Application Insights and other Azure resources. Quando la pipeline di rilascio rileva un avviso di Application Insights, la pipeline può eseguire il gate o il rollback della distribuzione finché l'avviso non viene risolto. Se tutti i controlli vengono superati, le distribuzioni possono procedere automaticamente dal test fino all'ambiente di produzione, senza la necessità di intervento manuale. 

## <a name="configure-continuous-monitoring"></a>Configurare il monitoraggio continuo

1. In [Azure DevOps](https://dev.azure.com)selezionare un'organizzazione e un progetto.
   
1. Nel menu a sinistra della pagina del progetto selezionare**Rilasci** **pipeline** > . 
   
1. Fare clic sulla freccia accanto a **Nuovo** e selezionare **Nuova pipeline di rilascio**. In alternativa, se non si dispone ancora di una pipeline, selezionare **Nuova pipeline** nella pagina visualizzata.
   
1. Nel riquadro **Selezionare un modello** cercare e selezionare Distribuzione del servizio app di **Azure con monitoraggio continuo**e quindi selezionare **Applica**. 

   ![Nuova pipeline di rilascio delle pipeline di AzureNew Azure Pipelines release pipeline](media/continuous-monitoring/001.png)

1. Nella casella **Passaggio 1** selezionare il collegamento ipertestuale per **visualizzare le attività di fase.**

   ![Visualizzare le attività della fase](media/continuous-monitoring/002.png)

1. Nel riquadro di configurazione **dello stage 1,** completare i seguenti campi: 

    | Parametro        | valore |
   | ------------- |:-----|
   | **Nome della fase**      | Specificare un nome di fase o lasciarlo alla **fase 1**. |
   | **Sottoscrizione di AzureAzure subscription** | Elenco a discesa e selezionare la sottoscrizione di Azure collegata che si vuole usare.|
   | **Tipo di app** | A discesa e seleziona il tipo di app. |
   | **Nome del servizio app** | Immettere il nome del servizio app di Azure. |
   | **Nome del gruppo di risorse per Application InsightsResource Group name for Application Insights**    | Fare drop e selezionare il gruppo di risorse che si vuole usare. |
   | **Nome risorsa di Application Insights** | Fare drop e selezionare la risorsa Application Insights per il gruppo di risorse selezionato.

1. Per salvare la pipeline con le impostazioni predefinite della regola di avviso, selezionare Salva in alto a destra nella finestra DevOps di Azure.To save the pipeline with default alert rule settings, select **Save** at upper right in the Azure DevOps window. Immettere un commento descrittivo e quindi selezionare **OK**.

## <a name="modify-alert-rules"></a>Modificare le regole di avviso

La distribuzione del servizio app di Azure con modello di **monitoraggio continuo** è insufficiente, ovvero quattro regole di avviso: **Disponibilità**, Richieste **non riuscite**, Tempo di risposta del **server**ed **Eccezioni server**. È possibile aggiungere altre regole o modificare le impostazioni delle regole per soddisfare le esigenze del livello di servizio. 

Per modificare le impostazioni delle regole di avviso:

1. Nel riquadro sinistro della pagina della pipeline di rilascio selezionare **Configura avvisi di Application Insights**.

1. Nel riquadro **Avvisi di Monitoraggio di Azure** selezionare i ellissi... accanto a Regole di **avviso**. **...**
   
1. Nella finestra di dialogo **Regole di avviso** selezionare il simbolo a discesa accanto a una regola di avviso, ad esempio **Disponibilità**. 
   
1. Modificare la **soglia** e altre impostazioni in base alle proprie esigenze.
   
   ![Modifica avviso](media/continuous-monitoring/003.png)
   
1. Selezionare OK e quindi Salva in alto a destra nella finestra DevOps di Azure.Select **OK**, and then select **Save** at upper right in the Azure DevOps window. Immettere un commento descrittivo e quindi selezionare **OK**.

## <a name="add-deployment-conditions"></a>Aggiungere le condizioni di distribuzione

Quando si aggiungono gate di distribuzione alla pipeline di rilascio, un avviso che supera le soglie impostate impedisce l'innalzamento di tempo indesiderato del rilascio. Una volta risolto l'avviso, la distribuzione può procedere automaticamente.

Per aggiungere gate di distribuzione:

1. Nella pagina della pipeline principale, in **Fasi**, selezionare il simbolo **Condizioni di pre-distribuzione** o **Condizioni post-distribuzione,** a seconda della fase che richiede un gate di monitoraggio continuo.
   
   ![Condizioni di pre-distribuzione](media/continuous-monitoring/004.png)
   
1. Nel riquadro **Configurazione condizioni di pre-distribuzione** impostare **Gate** su **Abilitato**.
   
1. Accanto a **Porte di distribuzione**selezionare **Aggiungi**.
   
1. Selezionare **Query Azure Monitor alerts (Visualizza avvisi** di Azure Monitor) dal menu a discesa. Questa opzione consente di accedere agli avvisi di Monitoraggio di Azure e di Application Insights.This option lets you can access both Azure Monitor and Application Insights alerts.
   
   ![Avvisi di Monitoraggio di Azure Query Azure Monitor alerts](media/continuous-monitoring/005.png)
   
1. In **Opzioni di valutazione**immettere i valori desiderati per le impostazioni, ad esempio Il tempo che intercorre tra la **rivalutazione dei gate** e Il timeout dopo il quale i gate hanno **esito negativo.** 

## <a name="view-release-logs"></a>Visualizzare i registri delle versioni

È possibile visualizzare il comportamento del gate di distribuzione e altri passaggi di rilascio nei log delle versioni. Per aprire i registri:

1. Selezionare **Rilasci** dal menu a sinistra della pagina della pipeline. 
   
1. Selezionare una versione qualsiasi. 
   
1. In **Fasi**selezionare una fase qualsiasi per visualizzare un riepilogo delle versioni. 
   
1. Per visualizzare i log, selezionare **Visualizza registri** nel riepilogo della versione, selezionare il collegamento ipertestuale **Riuscito** o Operazione **non riuscita** in qualsiasi fase oppure passare il mouse su una fase qualsiasi e selezionare **Registri**. 
   
   ![Visualizzare i registri delle versioni](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle pipeline di Azure, vedere la documentazione relativa alle pipeline di Azure.For more information about Azure Pipelines, see the [Azure Pipelines documentation.](https://docs.microsoft.com/azure/devops/pipelines)
