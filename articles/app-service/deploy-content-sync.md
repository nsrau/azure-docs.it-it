---
title: Sincronizzare il contenuto da una cartella nel cloud - Servizio app di Azure
description: Informazioni su come distribuire l'app nel servizio app di Azure tramite la sincronizzazione dei contenuti da una cartella nel cloud.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 60caadf58a8acdc7cecb0074e189fd1656032327
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617076"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure
Questo articolo illustra come eseguire la sincronizzazione del contenuto in [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714) da Dropbox e OneDrive. 

La distribuzione per la sincronizzazione del contenuto on demand si basa sul [motore di distribuzione Kudu](https://github.com/projectkudu/kudu/wiki) del servizio app. È possibile usare il codice e il contenuto dell'app in una cartella cloud designata e quindi eseguire la sincronizzazione al servizio app con un semplice clic del mouse. La sincronizzazione del contenuto usa il server di compilazione Kudu. 

## <a name="enable-content-sync-deployment"></a>Abilitare la distribuzione per la sincronizzazione del contenuto

Per abilitare la sincronizzazione del contenuto, passare alla pagina del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **OneDrive** o **Dropbox** > **Autorizza**. Seguire le istruzioni di autorizzazione. 

![](media/app-service-deploy-content-sync/choose-source.png)

È sufficiente concedere l'autorizzazione a OneDrive o Dropbox una sola volta. Se si ha già l'autorizzazione, fare semplicemente clic su **Continua**. È possibile modificare l'account OneDrive o Dropbox autorizzato facendo clic su **Cambia account**.

![](media/app-service-deploy-content-sync/continue.png)

Nella pagina **Configura** selezionare la cartella da sincronizzare. La cartella viene creata nel percorso del contenuto designato seguente in OneDrive o Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Al termine dell'operazione, fare clic su **Continua**.

Nella pagina **Riepilogo** verificare le opzioni e fare clic su **Fine**.

## <a name="synchronize-content"></a>Sincronizzare il contenuto

Quando si vuole sincronizzare il contenuto nella cartella cloud con il servizio app, tornare alla pagina **Centro distribuzione** e fare clic su **Sincronizzazione**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > A causa delle differenze sottostanti nelle API, **OneDrive for Business** non è attualmente supportato. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Disabilitare la distribuzione per la sincronizzazione dei contenuti

Per disabilitare la sincronizzazione del contenuto, passare alla pagina del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **Disconnetti**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire dall'archivio Git locale](deploy-local-git.md)
