---
title: Sincronizzare il contenuto da una cartella Cloud
description: Informazioni su come distribuire l'app nel servizio app Azure tramite la sincronizzazione del contenuto da una cartella Cloud, tra cui OneDrive o Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 693e552c8743b435fac6fda9d5ab023be5d9adeb
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221137"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure
Questo articolo illustra come eseguire la sincronizzazione del contenuto in [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714) da Dropbox e OneDrive. 

La distribuzione per la sincronizzazione del contenuto on demand si basa sul [motore di distribuzione Kudu](https://github.com/projectkudu/kudu/wiki) del servizio app. È possibile usare il codice e il contenuto dell'app in una cartella Cloud designata e quindi sincronizzarli con il servizio app facendo clic su un pulsante. La sincronizzazione del contenuto usa il server di compilazione Kudu. 

## <a name="enable-content-sync-deployment"></a>Abilitare la distribuzione per la sincronizzazione del contenuto

Per abilitare la sincronizzazione del contenuto, passare alla pagina del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Deployment Center**  >  **OneDrive** o **Dropbox**  >  **autorizza**. Seguire le istruzioni di autorizzazione. 

![Viene illustrato come autorizzare OneDrive o Dropbox in centro distribuzione nel portale di Azure.](media/app-service-deploy-content-sync/choose-source.png)

È sufficiente concedere l'autorizzazione a OneDrive o Dropbox una sola volta. Se si ha già l'autorizzazione, fare semplicemente clic su **Continua**. È possibile modificare l'account OneDrive o Dropbox autorizzato facendo clic su **Cambia account**.

![Mostra come modificare l'account OneDrive o Dropbox autorizzato in centro distribuzione nel portale di Azure.](media/app-service-deploy-content-sync/continue.png)

Nella pagina **Configura** selezionare la cartella da sincronizzare. La cartella viene creata nel percorso del contenuto designato seguente in OneDrive o Dropbox. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

Al termine dell'operazione, fare clic su **Continua**.

Nella pagina **Riepilogo** verificare le opzioni e fare clic su **Fine**.

## <a name="synchronize-content"></a>Sincronizzare il contenuto

Quando si vuole sincronizzare il contenuto nella cartella cloud con il servizio app, tornare alla pagina **Centro distribuzione** e fare clic su **Sincronizzazione**.

![Mostra come sincronizzare la cartella Cloud con il servizio app.](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > A causa delle differenze sottostanti nelle API, **OneDrive for Business** non è attualmente supportato. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Disabilitare la distribuzione per la sincronizzazione dei contenuti

Per disabilitare la sincronizzazione del contenuto, passare alla pagina del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **centro distribuzione**  >  **Disconnetti**.

![Mostra come disconnettere la sincronizzazione delle cartelle Cloud con l'app del servizio app nel portale di Azure.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire dal repository GIT locale](deploy-local-git.md)
