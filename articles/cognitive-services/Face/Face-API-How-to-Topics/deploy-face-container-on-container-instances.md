---
title: Eseguire il contenitore Face nelle istanze del contenitore di AzureRun Face container in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore Face in un'istanza del contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 4f8a08697443ec6920b3fa73de19a950e54e7f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716255"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Distribuire il contenitore Face in Istanze del contenitore di AzureDeploy the Face container to Azure Container Instances

Informazioni su come distribuire il contenitore [Per](../face-how-to-install-containers.md) dispositivi cognitivi in [istanze del contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. Questa procedura illustra la creazione di una risorsa Azure Face.This procedure demonstrates the creation of an Azure Face resource. Quindi viene illustrato il pull dell'immagine contenitore associata. Infine, evidenziamo la possibilità di esercitare l'orchestrazione dei due da un browser. L'utilizzo dei contenitori può spostare l'attenzione degli sviluppatori dalla gestione dell'infrastruttura al contrario concentrandosi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Richiedere l'accesso al registro contenitori privato

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]