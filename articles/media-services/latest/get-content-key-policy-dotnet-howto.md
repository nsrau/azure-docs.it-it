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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322484"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Ottenere una chiave di firma dai criteri esistenti

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. Le API della versione 3 non restituiscono segreti o credenziali per un'operazione **Get** o **List**. Le chiavi sono sempre Null, vuote o purificate dalla risposta. È necessario chiamare un metodo di azione separato per ottenere i segreti o le credenziali. Azioni distinte consentono di impostare autorizzazioni di sicurezza di controllo degli accessi in base al ruolo diverse, nel caso alcune API recuperino o visualizzino i segreti, mentre altre non lo fanno. Per informazioni su come gestire l'accesso con il controllo degli accessi in base al ruolo, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Alcuni esempi sono 

* non restituire valori ContentKey in Get per StreamingLocator, 
* non restituire le chiavi di restrizione in Get per ContentKeyPolicy, 
* non restituire la parte della stringa di query dell'URL (per rimuovere la firma) negli URL di input HTTP per i processi.

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
