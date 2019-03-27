---
title: Configurare il servizio di notifica Push Microsoft in hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare le impostazioni di servizio di notifica Push di Microsoft per un hub di notifica di Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488351"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni di Microsoft Push Notification Service (MPNS) per un hub di notifica nel portale di Azure
Questo articolo illustra come configurare le impostazioni di Microsoft Push Notification Service (MPNS) per un hub di notifica di Azure usando il portale di Azure. 

## <a name="prerequisites"></a>Prerequisiti
Se non hai già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurare il servizio di notifica Push Microsoft (MPNS)

La procedura seguente illustra i passaggi per configurare le impostazioni di Microsoft Push Notification Service (MPNS) per un hub di notifica: 

1. Nel portale di Azure, nelle **Hub di notifica** pagina, selezionare **Windows Phone (MPNS)** nel menu a sinistra.
1. Abilitare entrambe le notifiche push non autenticate o autenticato:

   a. Per abilitare le notifiche push non autenticate, selezionare **abilitare le notifiche push non autenticate** > **salvare**.

      ![Screenshot che illustra come abilitare le notifiche push non autenticate](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Per abilitare le notifiche push autenticato:
      * Sulla barra degli strumenti, selezionare **Upload Certificate**.
      * Selezionare l'icona di file e quindi selezionare il file del certificato.
      * Immettere la password per il certificato.
      * Selezionare **OK**.
      * Nel **Windows Phone (MPNS)** pagina, selezionare **salvare**.

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per effettuare il push di notifiche a dispositivi Windows Phone usando gli hub di notifica di Azure e Microsoft Push Notification Service (MPNS), vedere [le notifiche Push alle app di Windows Phone usando la notifica Hub](notification-hubs-windows-mobile-push-notifications-mpns.md).

