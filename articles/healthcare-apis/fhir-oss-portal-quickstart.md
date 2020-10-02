---
title: 'Portale di Azure: Distribuire il server FHIR open source per Azure - API di Azure per FHIR'
description: Questa guida di avvio rapido illustra come distribuire il server FHIR open source Microsoft con il portale di Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978577"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Avvio rapido: Distribuire il server FHIR open source con il portale di Azure

Questa guida di avvio rapido illustra come distribuire il server FHIR open source in Azure con il portale di Azure. Verranno usati collegamenti di distribuzione semplici nel [repository open source](https://github.com/Microsoft/fhir-server)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="github-open-source-repository"></a>Repository open source in GitHub

Passare alla [pagina di distribuzione di GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) e individuare i pulsanti "Distribuisci in Azure":

>[!div class="mx-imgBorder"]
>![Pagina di distribuzione open source](media/quickstart-oss-portal/deployment-page-oss.png)

Fare clic sul pulsante distribuzione per aprire il portale di Azure.

## <a name="fill-in-deployment-parameters"></a>Specificare i parametri di distribuzione

Scegliere di creare un nuovo gruppo di risorse e assegnargli un nome. Gli unici altri parametri necessario sono il nome del servizio e la password di amministratore di SQL.

>[!div class="mx-imgBorder"]
>![Parametri di distribuzione personalizzati](media/quickstart-oss-portal/deployment-custom-parameters.png)

Dopo aver specificato i dettagli, è possibile avviare la distribuzione.

## <a name="validate-fhir-server-is-running"></a>Verificare che il server FHIR sia in esecuzione

Una volta completata la distribuzione, è possibile puntare il browser all'indirizzo `https://SERVICENAME.azurewebsites.net/metadata` per ottenere una dichiarazione di funzionalità. La prima volta, il server impiegherà circa un minuto per rispondere.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile eliminare il gruppo di risorse e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse contenente le risorse di cui è stato effettuato il provisioning, selezionare **Elimina gruppo di risorse**, quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito il server FHIR open source Microsoft per Azure nella sottoscrizione. Per informazioni su come accedere all'API FHIR tramite Postman, passare all'esercitazione su Postman.
 
>[!div class="nextstepaction"]
>[Accedere all'API FHIR con Postman](access-fhir-postman-tutorial.md)