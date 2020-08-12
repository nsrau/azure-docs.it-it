---
title: Creare un hub di notifica di Azure con il portale di Azure | Microsoft Docs
description: Questa esercitazione mostra come creare un hub di notifica di Azure con il portale di Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 981e23a2b021cc0eb8085aa943830f87352aee69
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828524"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Guida introduttiva: Creare un hub di notifica di Azure nel portale di Azure

Hub di notifica di Azure offre un motore di push di facile uso e con scalabilità orizzontale che consente di inviare notifiche a qualsiasi piattaforma (iOS, Android, Windows, Kindle, Baidu e così via) da qualsiasi back-end (cloud o locale). Per altre informazioni sul servizio, vedere [Informazioni su Hub di notifica](notification-hubs-push-notification-overview.md).

In questa guida introduttiva viene creato un hub di notifica nel portale di Azure. La prima sezione illustra i passaggi per creare uno spazio dei nomi Hub di notifica e un hub in tale spazio dei nomi. La seconda sezione descrive i passaggi per creare un hub di notifica in uno spazio dei nomi Hub di notifica esistente.

## <a name="create-a-namespace-and-a-notification-hub"></a>Creare uno spazio dei nomi e un hub di notifica

In questa sezione viene creato uno spazio dei nomi e un hub nello spazio dei nomi.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Creare un hub di notifica in uno spazio dei nomi esistente

In questa sezione viene creato un hub di notifica in uno spazio dei nomi esistente.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nel menu a sinistra, cercare **Hub di notifica**, selezionare la **stella** (`*`) accanto a **Spazi dei nomi dell'hub di notifica** per aggiungerlo alla sezione **PREFERITI** nel menu a sinistra. Selezionare **Spazi dei nomi dell'hub di notifica**.

      ![Portale di Azure - Selezionare Spazi dei nomi dell'hub di notifica](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Nella pagina **Spazi dei nomi dell'hub di notifica** selezionare lo spazio dei nomi nell'elenco.

      ![Selezionare lo spazio dei nomi nell'elenco](./media/create-notification-hub-portal/select-namespace.png)
4. Nella pagina **Spazi dei nomi dell'hub di notifica** selezionare **Aggiungi hub** nella barra degli strumenti.

      ![Spazi dei nomi dell'hub di notifica - Pulsante Aggiungi hub](./media/create-notification-hub-portal/add-hub-button.png)
5. Nella pagina **Nuovo hub di notifica** immettere un nome per l'hub di notifica e selezionare **OK**.

      ![Pagina Nuovo hub di notifica -> Immettere un nome per l'hub](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Selezionare **Notifiche** (icona a forma di campanello) nella parte superiore per visualizzare lo stato della distribuzione del nuovo hub. Selezionare **X** nell'angolo destro per chiudere la finestra di notifica.

      ![Notifica della distribuzione](./media/create-notification-hub-portal/deployment-notification.png)
7. Aggiornare la pagina Web **Spazi dei nomi dell'hub di notifica** per visualizzare il nuovo hub nell'elenco.

      ![Portale di Azure - Notifiche -> Vai alla risorsa](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Selezionare l'**hub di notifica** per visualizzare la home page dell'hub di notifica.

      ![Portale di Azure - Notifiche -> Vai alla risorsa](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un hub di notifica. Per informazioni su come configurare l'hub con le impostazioni PNS (Platform Notification System), vedere [Configurare un hub di notifica con le impostazioni PNS](configure-notification-hub-portal-pns-settings.md).
