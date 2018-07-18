---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 22c1d24042072de5d57d41da379a5fad18180de7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972523"
---
1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Crea una risorsa** > **Dispositivi mobili** > **Hub di notifica**.
   
      ![Portale di Azure: creare un hub di notifica](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Nella casella **Hub di notifica** digitare un nome univoco. Selezionare l'**area**, la **sottoscrizione** e il **gruppo di risorse** (se già disponibili). 
   
      Se non si ha già uno spazio dei nomi del bus di servizio, è possibile usare il nome predefinito, che viene creato in base al nome dell'hub (se lo spazio dei nomi è disponibile).
    
      Se si ha già un spazio dei nomi del bus di servizio in cui si vuole creare l'hub, seguire questa procedura

    a. Nell'area **Spazio dei nomi** selezionare il collegamento **Seleziona esistente**. 
   
    b. Selezionare **Create**.
   
      ![Portale di Azure: impostare le proprietà dell'hub di notifica](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Selezionare **Notifiche** (icona a forma di campanello) e selezionare **Vai alla risorsa**. 

      ![Portale di Azure - Notifiche -> Vai alla risorsa](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
5. Selezionare **Criteri di accesso** dall'elenco. Prendere nota delle due stringhe di connessione disponibili. Sono necessarie in un secondo momento per gestire le notifiche push.

      >[!IMPORTANT]
      >**NON** usare DefaultFullSharedAccessSignature nell'applicazione. Deve essere usato solo nel back-end.
      >
   
      ![Portale di Azure: stringhe di connessione dell'hub di notifica](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

