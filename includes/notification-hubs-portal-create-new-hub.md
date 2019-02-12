---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823207"
---
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra e selezionare **Hub di notifica** nella sezione **Dispositivi mobili**. Selezionare l'asterisco (`*`) accanto al nome del servizio per aggiungerlo alla sezione **PREFERITI** nel menu a sinistra. Dopo aver aggiunto l'opzione **Hub di notifica** a **PREFERITI**, selezionarla nel menu a sinistra. 

      ![Portale di Azure - Selezionare Hub di notifica](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. Nella pagina **Hub di notifica** selezionare **Aggiungi** sulla barra degli strumenti. 

      ![Hub di notifica - Pulsante Aggiungi della barra degli strumenti](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. Nella pagina **Hub di notifica** procedere come segue: 
    1. Specificare un **nome** per l'**hub** di notifica.  
    2. Specificare un **nome** per lo **spazio dei nomi**.
    3. Selezionare la **posizione** in cui creare l'hub di notifica. 
    4. Selezionare un gruppo di risorse esistente oppure immettere un nome per il nuovo **gruppo di risorse**.
    5. Selezionare **Create**. 

        ![Portale di Azure: impostare le proprietà dell'hub di notifica](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Selezionare **Notifiche** (icona del campanello) e quindi **Vai alla risorsa** oppure aggiornare l'elenco nella pagina **Hub di notifica** e selezionare il proprio hub di notifica. 

      ![Portale di Azure - Notifiche -> Vai alla risorsa](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Selezionare **Criteri di accesso** dall'elenco. Prendere nota delle due stringhe di connessione disponibili. Sono necessarie in un secondo momento per gestire le notifiche push.

      >[!IMPORTANT]
      >**NON** usare DefaultFullSharedAccessSignature nell'applicazione. Deve essere usato solo nel back-end.
      >

      ![Portale di Azure: stringhe di connessione dell'hub di notifica](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
