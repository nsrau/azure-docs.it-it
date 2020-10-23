---
title: Ottenere una chiave di firma da un criterio usando servizi multimediali di Azure V3 .NET
description: In questo argomento viene illustrato come ottenere una chiave di firma dai criteri esistenti usando .NET SDK di Servizi multimediali v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 7107a2acdc0ca430797245125ca903f7414908da
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427067"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Ottenere una chiave di firma dai criteri esistenti

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. le API V3 non restituiscono segreti o credenziali per le operazioni **Get** o **List** . Vedere la spiegazione dettagliata qui: per altre informazioni, vedere gli [account RBAC e servizi multimediali di Azure](rbac-overview.md)

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
