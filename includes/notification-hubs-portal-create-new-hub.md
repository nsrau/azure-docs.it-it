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
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509151"
---
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Scegliere **Tutti i servizi** dal menu a sinistra e quindi selezionare **Hub di notifica** nella sezione **Dispositivi mobili**. Selezionare l'icona a forma di stella accanto al nome del servizio per aggiungere il servizio alla sezione **PREFERITI** nel menu a sinistra. Dopo aver aggiunto **Hub di notifica** a **PREFERITI**, selezionarlo nel menu a sinistra.

      ![Portale di Azure - Selezionare Hub di notifica](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Nella pagina **Hub di notifica** selezionare **Aggiungi** sulla barra degli strumenti.

      ![Hub di notifica - Pulsante Aggiungi della barra degli strumenti](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Nella pagina **Hub di notifica** procedere come segue:

    1. Immettere un nome in **Hub di notifica**.  

    1. Immettere un nome in **Crea un nuovo spazio dei nomi**. Uno spazio dei nomi contiene uno o più hub.

    1. Selezionare un valore nell'elenco a discesa **Posizione**. Questo valore specifica la posizione in cui creare l'hub.

    1. Selezionare un gruppo di risorse esistente in **Gruppo di risorse** o creare un nome per un nuovo gruppo di risorse.

    1. Selezionare **Create** (Crea).

        ![Portale di Azure: impostare le proprietà dell'hub di notifica](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Selezionare **Notifiche** (icona a forma di campanello) e quindi selezionare **Vai alla risorsa**. È anche possibile aggiornare l'elenco nella pagina **Hub di notifica** e selezionare l'hub.

      ![Portale di Azure - Notifiche -> Vai alla risorsa](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Selezionare **Criteri di accesso** dall'elenco. Prendere nota delle due stringhe di connessione disponibili. Sono necessarie in un secondo momento per gestire le notifiche push.

      >[!IMPORTANT]
      >*Non* usare il criterio **DefaultFullSharedAccessSignature** nell'applicazione. Deve essere usato solo nel back-end.
      >

      ![Portale di Azure: stringhe di connessione dell'hub di notifica](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
