---
title: Griglia di eventi di Azure - Abilitare i log di diagnostica per un argomentoAzure Event Grid - Enable diagnostic logs for a topic
description: Questo articolo fornisce istruzioni dettagliate su come abilitare i log di diagnostica per un argomento della griglia di eventi di Azure.This article provides step-by-step instructions on how to enable diagnostic logs for an Azure event grid topic.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960503"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Log di diagnostica per un argomento della griglia di eventi di AzureDiagnostic logs for an Azure event grid topic
Le impostazioni di diagnostica consentono agli utenti di Griglia di eventi di acquisire e visualizzare i log degli errori di pubblicazione e recapito in una delle posizioni seguenti: un account di archiviazione di Azure, un hub eventi o un'area di lavoro di Log Analytics.Diagnostic settings allow Event Grid users to capture and view publish and delivery failure Logs in one of the following places: an Azure storage account, an event hub, or an Log Analytics workspace. In questo articolo vengono fornite istruzioni dettagliate per abilitare i log di diagnostica per un argomento della griglia di eventi.

## <a name="prerequisites"></a>Prerequisiti

- Argomento della griglia degli eventi di cui è stato eseguito il provisioning
- Destinazione di cui è stato eseguito il provisioning per l'acquisizione dei log di diagnostica. Può una delle seguenti destinazioni:
    - Account di archiviazione di Azure
    - Hub eventi
    - Area di lavoro Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Passaggi per l'abilitazione dei log di diagnostica per un argomentoSteps for enabling diagnostic logs for a topic

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Passare all'argomento della griglia di eventi per il quale si desidera abilitare le impostazioni del log di diagnostica. 
3. Selezionare **Impostazioni di diagnostica** in **Monitoraggio** nel menu a sinistra.
4. Nella pagina **Impostazioni di diagnostica** selezionare Aggiungi nuova **impostazione diagnostica**. 
    
    ![Pulsante Aggiungi impostazione diagnostica](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Specificare un **nome** per l'impostazione di diagnostica. 

    ![Impostazioni di diagnostica - nome](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Abilitare una o più destinazioni di acquisizione per i log e quindi configurarle selezionando una risorsa di acquisizione creata in precedenza. 
    - Se si seleziona **Archivia in un account di archiviazione,** selezionare **Account di archiviazione - Configura**e quindi selezionare l'account di archiviazione nella sottoscrizione di Azure.If you select Archive to a storage account , select Storage account - Configure , and then select the storage account in your Azure subscription. 

        ![Archiviare in un account di archiviazione di AzureArchive to an Azure storage account](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se si seleziona **Flusso in un hub eventi,** selezionare Hub eventi - **Configura**e quindi selezionare lo spazio dei nomi Hub eventi, l'hub eventi e i criteri di accesso. 
        ![Streaming in un hub eventi](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se si seleziona **Invia a Log Analytics**, selezionare l'area di lavoro Log Analytics.
        ![Invia a Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Selezionare le opzioni **DeliveryFailures** e **PublishFailures** nella sezione **Log.** 
    ![Selezionare gli errori](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Selezionare **Salva**. Selezionare **X** nell'angolo destro per chiudere la pagina. 
9. A questo punto, nella pagina **Impostazioni di diagnostica,** verificare che venga visualizzata una nuova voce nella tabella **Impostazioni di diagnostica.** 
    ![Impostazione di diagnostica nell'elenco](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     È inoltre possibile abilitare la raccolta di tutte le metriche per l'argomento. 

## <a name="next-steps"></a>Passaggi successivi
Se hai bisogno di ulteriore aiuto, pubblica il tuo problema nel [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) o apri un ticket di [supporto.](https://azure.microsoft.com/support/options/) 
