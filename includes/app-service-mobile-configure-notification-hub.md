---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180650"
---
Poiché le funzioni delle app per dispositivi mobili del Servizio app di Azure usano [Hub di notifica di Azure] per inviare push, si dovrà configurare un hub di notifica per l'app per dispositivi mobili.

1. Nel [portale di Azure] passare a **Servizi app** e quindi selezionare il back-end dell'app. In **Impostazioni** selezionare **Push**.
2. Per aggiungere una risorsa hub di notifica all'app selezionare **Connetti**. È possibile creare un hub o connettersi a uno esistente.

    ![Configurare un hub](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

A questo punto un hub di notifica è stato connesso al progetto di back-end dell'app per dispositivi mobili. In seguito si configurerà questo hub di notifica per la connessione a un sistema PNS (Platform Notification System) per eseguire il push ai dispositivi.

[Portale di Azure]: https://portal.azure.com/
[Hub di notifica di Azure]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
