---
title: Ottenere una chiave di firma dai criteri esistenti usando .NET SDK di Servizi multimediali v3 - Azure | Microsoft Docs
description: In questo argomento viene illustrato come ottenere una chiave di firma dai criteri esistenti usando .NET SDK di Servizi multimediali v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496319"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Ottenere una chiave di firma dai criteri esistenti

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. API v3 non restituiscono i segreti o le credenziali sul **ottenere** oppure **elenco** operazioni. Le chiavi sono sempre Null, vuote o purificate dalla risposta. L'utente deve chiamare un metodo di azione separata per ottenere i segreti o le credenziali. Il **lettore** ruolo non è possibile chiamare le operazioni in modo che non è possibile chiamare operazioni come ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas, StreamingLocator.ListContentKeys,. Con azioni separate consente di impostare le autorizzazioni di sicurezza più granulari RBAC in un ruolo personalizzato, se lo si desidera.

Per altre informazioni, vedere [account RBAC e servizi multimediali](rbac-overview.md)

L'esempio di questo articolo mostra come usare .NET per ottenere una chiave di firma dai criteri esistenti. 
 
## <a name="download"></a>Download 

Clonare nel computer un repository GitHub contenente l'esempio .NET completo usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Il ContentKeyPolicy con chiavi private di esempio si trova nella cartella [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM).

## <a name="get-contentkeypolicy-with-secrets"></a>Ottenere ContentKeyPolicy con chiavi private 

Per ottenere la chiave, usare **GetPolicyPropertiesWithSecretsAsync**, come illustrato nell'esempio seguente.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Passaggi successivi

[Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md) 
