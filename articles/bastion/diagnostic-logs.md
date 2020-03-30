---
title: Abilitare e usare i log di diagnostica di Azure BastionEnable and work with Azure Bastion diagnostic logs
description: In questo articolo viene illustrato come abilitare e usare i log di diagnostica di Azure Bastion.In this article, learn how to enable and work with Azure Bastion diagnostic logs.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2167a17d5d388c97ad357398c4ac2676e43be5a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989451"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Abilitare e utilizzare i log di diagnostica di BastionEnable and work with Bastion diagnostic logs

Quando gli utenti si connettono ai carichi di lavoro usando Azure Bastion, Bastion può registrare la diagnostica delle sessioni remote. È quindi possibile usare la diagnostica per visualizzare gli utenti connessi a i carichi di lavoro, a quale ora, da dove e altre informazioni di registrazione pertinenti. In order to use the diagnostics, you must enable diagnostics logs on Azure Bastion. Questo articolo consente di abilitare i log di diagnostica e quindi visualizzare i log.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>Abilitare il log di diagnosticaEnable the diagnostics log

1. Nel [portale di Azure](https://portal.azure.com)passare alla risorsa Bastion di Azure e selezionare **Impostazioni di diagnostica** nella pagina Bastione di Azure.In the Azure portal , navigate to your Azure Bastion resource and select Diagnostics settings from the Azure Bastion page.

   ![impostazioni di diagnostica](./media/diagnostic-logs/1diagnostics-settings.png)
2. Selezionare **Impostazioni di diagnostica**, quindi selezionare Aggiungi **impostazione di diagnostica** per aggiungere una destinazione per i log.

   ![aggiungere l'impostazione diagnostica](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Nella pagina **Impostazioni di diagnostica** selezionare il tipo di account di archiviazione da usare per l'archiviazione dei log di diagnostica.

   ![selezionare la posizione di archiviazione](./media/diagnostic-logs/3add-storage-account.png)
4. Una volta completate le impostazioni, sarà simile a questo esempio:When you complete the settings, it will look similar to this example:

   ![impostazioni di esempio](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Visualizzare il registro di diagnosticaView diagnostics log

Per accedere ai log di diagnostica, è possibile usare direttamente l'account di archiviazione specificato durante l'abilitazione delle impostazioni di diagnostica.

1. Passare alla risorsa dell'account di archiviazione, quindi a **Contenitori**. Viene visualizzato il BLOB **insights-logs-bastionauditlogs** creato nel contenitore BLOB dell'account di archiviazione.

   ![impostazioni di diagnostica](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Mentre navighi all'interno del contenitore, vedrai varie cartelle nel tuo blog. Queste cartelle indicano la gerarchia di risorse per la risorsa Bastione di Azure.These folders indicate the resource hierarchy for your Azure Bastion resource.

   ![aggiungere l'impostazione diagnostica](./media/diagnostic-logs/2-resource-h.png)
3. Passare alla gerarchia completa della risorsa di Azure Bastion di cui si desidera accedere/visualizzare la diagnostica. I valori 'y'' , 'm', 'd', 'h' e 'm' indicano rispettivamente l'anno, il mese, il giorno, l'ora e i minuti per i log di diagnostica.

   ![selezionare la posizione di archiviazione](./media/diagnostic-logs/3-resource-location.png)
4. Individuare il file json creato da Azure Bastion che contiene i dati del log di diagnostica per il periodo di tempo in cui ci si è spostati.

5. Scaricare il file json dal contenitore del BLOB di archiviazione. Una voce di esempio dal file json è mostrata di seguito per riferimento:An example entry from the json file is shown below for reference:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

Leggi le domande frequenti sul [Bastione](bastion-faq.md).
