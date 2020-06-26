---
title: 'Interfaccia della riga di comando di Azure: Distribuire il server FHIR open source per Azure - API di Azure per Azure'
description: Questa guida di avvio rapido illustra come distribuire il server Microsoft FHIR open source per Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "84819976"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Avvio rapido: Distribuire il server FHIR open source con l'interfaccia della riga di comando di Azure

Questa guida di avvio rapido illustra come distribuire un server FHIR&reg; open source in Azure con l'interfaccia della riga di comando di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Scegliere un nome per il gruppo di risorse che conterrà le risorse di cui è stato effettuato il provisioning e crearlo:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Distribuire il modello

Il [repository di GitHub](https://github.com/Microsoft/fhir-server) per il server Microsoft FHIR per Azure contiene un modello che distribuirà tutte le risorse necessarie. Distribuirlo con:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Verificare che il server FHIR sia in esecuzione

Ottenere una dichiarazione di funzionalità dal server FHIR con:

```console
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

La prima volta, il server impiegherà circa un minuto per rispondere.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il gruppo di risorse seguendo questa procedura:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato distribuito il server FHIR open source Microsoft per Azure nella sottoscrizione. Per informazioni su come accedere all'API FHIR tramite Postman, passare all'esercitazione su Postman.
 
>[!div class="nextstepaction"]
>[Accedere all'API FHIR con Postman](access-fhir-postman-tutorial.md)