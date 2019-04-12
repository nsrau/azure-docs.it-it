---
title: Sviluppo con le API v3 - Azure | Microsoft Docs
description: Questo articolo illustra le regole che si applicano a entità e le API durante lo sviluppo con servizi multimediali v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492142"
---
# <a name="developing-with-media-services-v3-apis"></a>Sviluppo con servizi multimediali v3 API

Questo articolo illustra le regole che si applicano a entità e le API durante lo sviluppo con servizi multimediali v3.

## <a name="naming-conventions"></a>Convenzioni di denominazione

I nomi delle risorse di Servizi multimediali di Azure v3 (ad esempio, asset, processi e trasformazioni) sono soggetti ai vincoli di denominazione di Azure Resource Manager. In conformità con Azure Resource Manager, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID). 

I nomi delle risorse di Servizi multimediali non possono includere "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", virgolette singole o caratteri di controllo. Sono consentiti tutti gli altri caratteri. La lunghezza massima di un nome di risorsa è di 260 caratteri. 

Per altre informazioni sulla denominazione di Azure Resource Manager, vedere: [Requisiti di denominazione](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenzioni di denominazione](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>principi di progettazione di API v3 e RBAC

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. API v3 non restituiscono i segreti o le credenziali sul **ottenere** oppure **elenco** operazioni. Le chiavi sono sempre Null, vuote o purificate dalla risposta. L'utente deve chiamare un metodo di azione separata per ottenere i segreti o le credenziali. Il **lettore** ruolo non è possibile chiamare le operazioni in modo che non è possibile chiamare operazioni come ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas, StreamingLocator.ListContentKeys,. Con azioni separate consente di impostare le autorizzazioni di sicurezza più granulari RBAC in un ruolo personalizzato, se lo si desidera.

Per altre informazioni, vedere:

- [Definizioni di ruolo predefinite](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Usare RBAC per gestire l'accesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Controllo di accesso basato sui ruoli per gli account di servizi multimediali](rbac-overview.md)
- [Ottenere i criteri di chiave simmetrica - .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Operazioni con esecuzione prolungata

Le operazioni contrassegnate con `x-ms-long-running-operation` in servizi multimediali di Azure [swagger file](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) sono lunghe operazioni in esecuzione. 

Per informazioni dettagliate su come tenere traccia delle operazioni asincrone, vedere [operazioni asincrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Servizi multimediali ha le seguenti operazioni a esecuzione prolungata:

* Creare LiveEvent
* Aggiornamento Live
* Elimina LiveEvent
* Avvia Live
* Arresta Live
* Reimpostare LiveEvent
* Creare LiveOutput
* Elimina LiveOutput
* Creare un'entità StreamingEndpoint
* Aggiornare l'entità StreamingEndpoint
* Eliminare l'entità StreamingEndpoint
* Avviare un'entità StreamingEndpoint
* Arrestare entità StreamingEndpoint
* Scalabilità StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali

Vedere [filtro, ordinamento, paging delle entità di servizi multimediali di Azure](entities-overview.md)

## <a name="next-steps"></a>Passaggi successivi

[Iniziare a sviluppare con l'API Servizi multimediali v3 usando gli SDK e/o gli strumenti](developers-guide.md)
