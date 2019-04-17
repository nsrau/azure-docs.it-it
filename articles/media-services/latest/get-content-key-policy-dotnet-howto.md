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
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615830"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Ottenere una chiave di firma dai criteri esistenti

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. API v3 non restituiscono i segreti o le credenziali sul **ottenere** oppure **elenco** operazioni. Vedere una descrizione dettagliata di seguito: Per altre informazioni, vedere [account RBAC e servizi multimediali](rbac-overview.md)

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
