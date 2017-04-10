---
title: Connettere un&quot;app Web a una rete per la distribuzione di contenuti | Microsoft Docs
description: Connettere un&quot;app Web a una rete per la distribuzione di contenuti per distribuire i file statici da nodi perimetrali.
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: d616ca49da7a909a1a104409c5845f39c4a5377a
ms.lasthandoff: 04/04/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Connettere un'app Web a una rete per la distribuzione di contenuti

In questa esercitazione si creeranno un profilo e un endpoint della rete CDN di Azure per distribuire i file statici dall'app Web tramite le località POP della rete CDN di Azure.

> [!TIP]
> Vedere l'elenco aggiornato delle [località POP della rete CDN di Azure](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="step-1---login-to-azure-portal"></a>Passaggio 1: accedere al portale di Azure

Aprire un browser a scelta e passare al [portale](https://portal.azure.com) di Azure.

## <a name="step-2---create-a-cdn-profile"></a>Passaggio 2: creare un profilo della rete CDN

Nel riquadro di spostamento a sinistra fare clic sul pulsante `+ New` e quindi su **Web e dispositivi mobili**. Nella categoria Web e dispositivi mobili selezionare **Rete CDN**.

Specificare valori per **Nome**, **Località**, **Gruppo di risorse** e **Piano tariffario**, quindi fare clic su **Crea**.

Aprire l'hub dei gruppi di risorse dal riquadro di spostamento sinistro e selezionare **myResourceGroup**. Dall'elenco di risorse selezionare **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Passaggio 3: creare un endpoint della rete CDN

Fare clic su `+ Endpoint` dai comandi accanto alla casella di ricerca per aprire il pannello di creazione di endpoint.

Specificare valori per **Nome**, **Tipo di origine**, **Nome host dell'origine**, quindi fare clic su **Aggiungi**.

Verrà creato l'endpoint. Dopo aver creato l'endpoint della rete per la distribuzione di contenuti, lo stato diventerà **In esecuzione**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>Passaggio 4: uso della rete CDN

Ora che l'endpoint è in esecuzione, verificare che il contenuto sia disponibile nel server POP passando a un file statico nel server Web, quindi modificando il nome host da `http://<app_name>.azurewebsites.net/path/to-static-file` a `http://<endpoint_name>.azureedge.net/path/to-static-file`.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>Passaggi successivi


