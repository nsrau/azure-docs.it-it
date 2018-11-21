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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 54a92e56df21b59430ed12f191a9cf7a918e14c9
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612900"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure
 
È necessario usare l'autenticazione dell'entità servizio di Azure AD per connettersi all'API di Servizi multimediali di Azure. L'applicazione deve richiedere un token di Azure AD che abbia i seguenti parametri:

* Endpoint del tenant di Azure AD
* URI di risorsa di Servizi multimediali
* URI di risorsa per Servizi multimediali REST
* Valori dell'applicazione Azure AD: ID client e Segreto client

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un'applicazione e un'entità servizio di Azure Active Directory (Azure AD) e ottenere i valori necessari per accedere alle risorse di Servizi multimediali di Azure.

## <a name="prerequisites"></a>Prerequisiti 

- Installare e usare l'interfaccia della riga di comando in locale. Per questo articolo è necessaria l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

    Attualmente, non tutti i comandi dell'[interfaccia della riga di comando di Servizi multimediali v3](https://aka.ms/ams-v3-cli-ref) funzionano in Azure Cloud Shell. È consigliabile usare l'interfaccia della riga di comando in locale.

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

    Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Vedere anche 

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
