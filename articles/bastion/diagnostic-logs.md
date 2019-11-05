---
title: Abilitare e usare i log di diagnostica di Azure Bastion | Microsoft Docs
description: Questo articolo illustra come abilitare e usare i log di diagnostica di Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 8ae421b48f3cfa8055f636052c990f99e0c775b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512405"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Abilitare e usare i log di diagnostica Bastion

Poiché gli utenti si connettono ai carichi di lavoro con Azure Bastion, Bastion può registrare la diagnostica delle sessioni remote. È quindi possibile usare la diagnostica per visualizzare gli utenti connessi ai carichi di lavoro, al momento, da dove e altre informazioni di registrazione rilevanti. Per usare la diagnostica, è necessario abilitare i log di diagnostica in Azure Bastion. Questo articolo consente di abilitare i log di diagnostica e di visualizzare i log.

## <a name="enable"></a>Abilitare il log di diagnostica

1. Nella [portale di Azure](https://portal.azure.com)passare alla risorsa di Azure Bastion e selezionare impostazioni di **diagnostica** dalla pagina del Bastion di Azure.

   ![impostazioni di diagnostica](./media/diagnostic-logs/1diagnostics-settings.png)
2. Selezionare **impostazioni di diagnostica**, quindi selezionare **+ Aggiungi** impostazioni di diagnostica per aggiungere una destinazione per i log.

   ![Aggiungi impostazione di diagnostica](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Nella pagina **impostazioni di diagnostica** selezionare il tipo di account di archiviazione da usare per l'archiviazione dei log di diagnostica.

   ![Selezionare il percorso di archiviazione](./media/diagnostic-logs/3add-storage-account.png)
4. Una volta completate le impostazioni, l'aspetto sarà simile a questo esempio:

   ![impostazioni di esempio](./media/diagnostic-logs/4example-settings.png)

## <a name="view"></a>Visualizza log di diagnostica

Per accedere ai log di diagnostica, è possibile usare direttamente l'account di archiviazione specificato durante l'abilitazione delle impostazioni di diagnostica.

1. Passare alla risorsa dell'account di archiviazione e quindi ai **contenitori**. Viene visualizzato il BLOB **Insights-logs-bastionauditlogs** creato nel contenitore BLOB dell'account di archiviazione.

   ![impostazioni di diagnostica](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Quando ci si sposta all'interno del contenitore, nel Blog vengono visualizzate diverse cartelle. Queste cartelle indicano la gerarchia delle risorse per la risorsa di Azure Bastion.

   ![Aggiungi impostazione di diagnostica](./media/diagnostic-logs/2-resource-h.png)
3. Passare alla gerarchia completa della risorsa di Azure Bastion i cui log di diagnostica si vuole accedere/visualizzare. ' Y =',' m'=',' d'=',' h =' è m'=' indicano rispettivamente l'anno, il mese, il giorno, l'ora e il minuto per i log di diagnostica.

   ![Selezionare il percorso di archiviazione](./media/diagnostic-logs/3-resource-location.png)
4. Individuare il file JSON creato da Azure Bastion che contiene i dati del log di diagnostica per il periodo di tempo a cui si è passati.

5. Scaricare il file JSON dal contenitore BLOB di archiviazione. Di seguito è riportata una voce di esempio del file JSON per riferimento:

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

Leggere le [domande frequenti su Bastion](bastion-faq.md).
