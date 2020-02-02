---
title: 'Griglia di eventi di Azure: abilitare i log di diagnostica per un argomento'
description: Questo articolo fornisce istruzioni dettagliate su come abilitare i log di diagnostica per un argomento di griglia di eventi di Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960503"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Log di diagnostica per un argomento di griglia di eventi di Azure
Le impostazioni di diagnostica consentono agli utenti di griglia di eventi di acquisire e visualizzare i log degli errori di pubblicazione e recapito in una delle seguenti posizioni: un account di archiviazione di Azure, un hub eventi o un'area di lavoro Log Analytics. In questo articolo vengono fornite istruzioni dettagliate per l'abilitazione dei log di diagnostica per un argomento di griglia di eventi.

## <a name="prerequisites"></a>Prerequisiti

- Un argomento di griglia di eventi con provisioning
- Destinazione sottoposta a provisioning per l'acquisizione dei log di diagnostica. Può essere una delle seguenti destinazioni:
    - Account di archiviazione di Azure
    - Hub eventi
    - Area di lavoro Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Passaggi per l'abilitazione dei log di diagnostica per un argomento

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'argomento di griglia di eventi per il quale si desidera abilitare le impostazioni del log di diagnostica. 
3. Selezionare **impostazioni di diagnostica** in **monitoraggio** nel menu a sinistra.
4. Nella pagina **impostazioni di diagnostica** selezionare **Aggiungi nuova impostazione di diagnostica**. 
    
    ![Pulsante Aggiungi impostazione diagnostica](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Specificare un **nome** per l'impostazione di diagnostica. 

    ![Impostazioni di diagnostica-nome](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Abilitare una o più destinazioni di acquisizione per i log, quindi configurarle selezionando una risorsa di acquisizione creata in precedenza. 
    - Se si seleziona **archivia in un account di archiviazione**, selezionare **account di archiviazione-configura**e quindi selezionare l'account di archiviazione nella sottoscrizione di Azure. 

        ![Archivia in un account di archiviazione di Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se si seleziona **flusso in un hub eventi**, selezionare **Hub eventi-configura**, quindi selezionare lo spazio dei nomi di hub eventi, l'hub eventi e i criteri di accesso. 
        ![flusso a un hub eventi](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se si seleziona **Invia a log Analytics**, selezionare l'area di lavoro log Analytics.
        ![inviare al Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Selezionare le opzioni **DeliveryFailures** e **PublishFailures** nella sezione **log** . 
    ![selezionare gli errori](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Selezionare **Salva**. Selezionare **X** nell'angolo destro per chiudere la pagina. 
9. A questo punto, nella pagina **impostazioni di diagnostica** , verificare che venga visualizzata una nuova voce nella tabella impostazioni di **diagnostica** . 
    ![impostazione di diagnostica nell'elenco](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     È anche possibile abilitare la raccolta di tutte le metriche per l'argomento. 

## <a name="next-steps"></a>Passaggi successivi
Se è necessaria ulteriore assistenza, pubblicare il problema nel [forum stack overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) o aprire un [ticket di supporto](https://azure.microsoft.com/support/options/). 
