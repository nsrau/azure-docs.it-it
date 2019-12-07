---
title: Accedere all'API di Servizi multimediali - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Per accedere all'API di servizi multimediali di Azure, seguire la procedura illustrata in questo articolo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896147"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure
 
Per usare l'autenticazione dell'entità servizio Azure AD per connettersi all'API di servizi multimediali di Azure, l'applicazione deve richiedere un token Azure AD con i parametri seguenti:

* Endpoint del tenant di Azure AD
* URI di risorsa di Servizi multimediali
* URI di risorsa per Servizi multimediali REST
* Valori dell'applicazione Azure AD: ID client e Segreto client

Per una spiegazione dettagliata, vedere [accesso alle API di servizi multimediali V3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un'applicazione e un'entità servizio di Azure Active Directory (Azure AD) e ottenere i valori necessari per accedere alle risorse di Servizi multimediali di Azure.

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Vedi anche

- [Ridimensionare le Media Reserved Units (S1/S2/S3) - Interfaccia della riga di comando](media-reserved-units-cli-how-to.md)
- [Creare un account di Servizi multimediali - Interfaccia della riga di comando](create-account-cli-how-to.md) 
- [Reimpostare le credenziali dell'account - Interfaccia della riga di comando](cli-reset-account-credentials.md)
- [Creare gli asset - Interfaccia della riga di comando](cli-create-asset.md)
- [Caricare un file - Interfaccia della riga di comando](cli-upload-file-asset.md)
- [Creare trasformazioni - Interfaccia della riga di comando](cli-create-transform.md)
- [Codificare con una trasformazione personalizzata-CLI](custom-preset-cli-howto.md)
- [Creare i processi - Interfaccia della riga di comando](cli-create-jobs.md)
- [Creare una Griglia di eventi - Interfaccia della riga di comando](job-state-events-cli-how-to.md)
- [Pubblicare un asset - Interfaccia della riga di comando](cli-publish-asset.md)
- [Applicare un filtro - Interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)
- [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passaggi successivi

L'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato in esecuzione. Il comando CLI seguente avvia l'endpoint di streaming predefinito:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

