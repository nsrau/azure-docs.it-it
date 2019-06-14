---
title: Accedere all'API di Servizi multimediali - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Seguire i passaggi di questa procedura per accedere all'API di Servizi multimediali di Azure.
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
ms.openlocfilehash: 5dbcf446a609adcd0f1902fcca2ac19ad87f17b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65779661"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure
 
Per usare l'autenticazione dell'entità servizio di Azure AD per connettersi all'API servizi multimediali di Azure, l'applicazione deve richiedere un token di Azure AD con i parametri seguenti:

* Endpoint del tenant di Azure AD
* URI di risorsa di Servizi multimediali
* URI di risorsa per Servizi multimediali REST
* Valori dell'applicazione Azure AD: ID client e Segreto client

Per informazioni dettagliate, vedere [l'accesso a servizi multimediali v3 API](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un'applicazione e un'entità servizio di Azure Active Directory (Azure AD) e ottenere i valori necessari per accedere alle risorse di Servizi multimediali di Azure.

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Vedere anche

- [Ridimensionare le Media Reserved Units (S1/S2/S3) - Interfaccia della riga di comando](media-reserved-units-cli-how-to.md)
- [Creare un account di Servizi multimediali - Interfaccia della riga di comando](create-account-cli-how-to.md) 
- [Reimpostare le credenziali dell'account - Interfaccia della riga di comando](cli-reset-account-credentials.md)
- [Creare gli asset - Interfaccia della riga di comando](cli-create-asset.md)
- [Caricare un file - Interfaccia della riga di comando](cli-upload-file-asset.md)
- [Creare trasformazioni - Interfaccia della riga di comando](cli-create-transform.md)
- [Codificare contenuti con una trasformazione personalizzata - CLI](custom-preset-cli-howto.md)
- [Creare i processi - Interfaccia della riga di comando](cli-create-jobs.md)
- [Creare una Griglia di eventi - Interfaccia della riga di comando](job-state-events-cli-how-to.md)
- [Pubblicare un asset - Interfaccia della riga di comando](cli-publish-asset.md)
- [Applicare un filtro - Interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passaggi successivi

L'Endpoint di Streaming da cui vuole trasmettere il contenuto deve essere nello stato in esecuzione. Il comando seguente avvia l'Endpoint di Streaming predefinito:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

