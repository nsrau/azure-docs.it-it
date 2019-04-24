---
title: Configurare il servizio di notifica Push Windows negli hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare le impostazioni di Windows Push Notification Service per un hub di notifica di Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240275"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni di Windows Push Notification Service (WNS) per un hub di notifica nel portale di Azure
Questo articolo illustra come configurare le impostazioni di Strumentazione gestione Windows (WNS, Windows Notification Service) per un hub di notifica di Azure usando il portale di Azure.  

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurare Windows Push Notification Service (WNS)

La procedura seguente illustra i passaggi per configurare le impostazioni di Windows Push Notification Service (WNS) per un hub di notifica: 

1. Nel portale di Azure, nelle **Hub di notifica** pagina, selezionare **Windows (WNS)** nel menu a sinistra.
2. Immettere i valori per **SID pacchetto** e **Chiave di sicurezza**.
3. Selezionare **Salva**.

   ![Screenshot che illustra le caselle SID pacchetto e Chiave di sicurezza](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per l'invio di notifiche alle applicazioni (Universal Windows Platform) con hub di notifica di Azure e Windows Push Notification Service (WNS), vedere [inviare notifiche alle App UWP con Azure Hub di notifica](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


