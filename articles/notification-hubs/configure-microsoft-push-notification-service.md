---
title: Configurare il servizio notifiche push Microsoft in hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare le impostazioni del servizio notifiche push Microsoft per un hub di notifica di Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 6784331dccd779390ace7dc632d54fe54aedc458
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760992"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Configurare le impostazioni del servizio notifiche push Microsoft (MPNS) nel portale di Azure

Questo articolo illustra come configurare le impostazioni di Microsoft Push Notification Service (MPNS) per un hub di notifica di Azure usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md).

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurare il servizio notifiche push Microsoft (MPNS)

Nella procedura seguente viene descritto come configurare le impostazioni di Microsoft Push Notification Service (MPNS) per un hub di notifica:

1. Nella portale di Azure nella pagina Hub di **notifica** selezionare **Windows Phone (MPNS)** nel menu a sinistra.
2. Abilitare le notifiche push non autenticate o autenticate:

   a. Per abilitare le notifiche push non autenticate, selezionare **Abilita notifiche push non autenticate** > **Salva**.

      ![Screenshot che illustra come abilitare le notifiche push non autenticate](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Per abilitare le notifiche push autenticate:
      * Selezionare **Carica certificato** sulla barra degli strumenti.
      * Selezionare l'icona del file e quindi il file del certificato.
      * Immettere la password per il certificato.
      * Selezionare **OK**.
      * Nella pagina **Windows Phone(MPNS)** selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione con istruzioni dettagliate per il push delle notifiche ai dispositivi Windows Phone usando hub di notifica di Azure e il servizio di notifica push di Microsoft (MPNS), vedere [inviare notifiche push alle app Windows Phone usando hub di notifica](notification-hubs-windows-mobile-push-notifications-mpns.md).
