---
title: "Avvio rapido: Distribuire l'API di Azure per FHIR con l'interfaccia della riga di comando di Azure"
description: Questa guida di avvio rapido illustra come distribuire l'API di Azure per FHIR in Azure con l'interfaccia della riga di comando di Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: b09a72538dd7a6886811b9a23c915316627da093
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339442"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Avvio rapido: Distribuire l'API di Azure per FHIR con l'interfaccia della riga di comando di Azure

Questa guida di avvio rapido illustra come distribuire l'API di Azure per FHIR in Azure con l'interfaccia della riga di comando di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>Aggiungere l'estensione HealthcareAPIs

```azurecli-interactive
az extension add --name healthcareapis
```

Ottenere un elenco di comandi per HealthcareAPIs:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Creare un gruppo di risorse di Azure

Scegliere un nome per il gruppo di risorse che conterrà le risorse dell'API di Azure per FHIR e crearlo:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Distribuire l'API di Azure per FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Recuperare la dichiarazione di funzionalità dell'API FHIR

Ottenere una dichiarazione di funzionalità dall'API FHIR con:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il gruppo di risorse seguendo questa procedura:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata distribuita l'API di Azure per FHIR nella sottoscrizione. Per configurare le impostazioni aggiuntive nell'API di Azure per FHIR, passare alla guida pratica sulle impostazioni aggiuntive. Se si vuole iniziare a usare l'API di Azure per FHIR, leggere gli articoli relativi alla registrazione delle applicazioni.

>[!div class="nextstepaction"]
>[Impostazioni aggiuntive nell'API di Azure per FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Panoramica della registrazione di applicazioni](fhir-app-registration.md)
