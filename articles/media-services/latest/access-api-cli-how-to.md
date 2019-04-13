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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 1b872c5c2ff0f581300a843650d7434c7c526c84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545620"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure
 
È necessario usare l'autenticazione dell'entità servizio di Azure AD per connettersi all'API di Servizi multimediali di Azure. L'applicazione deve richiedere un token di Azure AD che abbia i seguenti parametri:

* Endpoint del tenant di Azure AD
* URI di risorsa di Servizi multimediali
* URI di risorsa per Servizi multimediali REST
* Valori dell'applicazione Azure AD: ID client e Segreto client

Per altre informazioni, vedere [lo sviluppo con le API di servizi multimediali v3](media-services-apis-overview.md).

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un'applicazione e un'entità servizio di Azure Active Directory (Azure AD) e ottenere i valori necessari per accedere alle risorse di Servizi multimediali di Azure.

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Vedere anche 

- [Ridimensionare le Media Reserved Units (S1/S2/S3) - Interfaccia della riga di comando](media-reserved-units-cli-how-to.md)
- [Creare un account di Servizi multimediali - Interfaccia della riga di comando](./scripts/cli-create-account.md) 
- [Reimpostare le credenziali dell'account - Interfaccia della riga di comando](./scripts/cli-reset-account-credentials.md)
- [Creare gli asset - Interfaccia della riga di comando](./scripts/cli-create-asset.md)
- [Caricare un file - Interfaccia della riga di comando](./scripts/cli-upload-file-asset.md)
- [Creare trasformazioni - Interfaccia della riga di comando](./scripts/cli-create-transform.md)
- [Creare i processi - Interfaccia della riga di comando](./scripts/cli-create-jobs.md)
- [Creare una Griglia di eventi - Interfaccia della riga di comando](./scripts/cli-create-event-grid.md)
- [Pubblicare un asset - Interfaccia della riga di comando](./scripts/cli-publish-asset.md)
- [Applicare un filtro - Interfaccia della riga di comando](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Passaggi successivi

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
