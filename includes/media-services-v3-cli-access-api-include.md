---
title: File di inclusione
description: File di inclusione
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/29/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 7454e96a2a05bf89a0455674a4f144534c374c71
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34667878"
---
## <a name="access-the-media-services-api"></a>Accedere all'API di Servizi multimediali

Per connettersi alle API di Servizi multimediali di Azure, si usa l'autenticazione dell'entità servizio di Azure AD. Il comando seguente consente di creare un'applicazione Azure AD e di collegare un'entità servizio all'account. È consigliabile usare i valori restituiti per configurare l'applicazione.

Prima di eseguire lo script, è possibile sostituire `amsaccount` e `amsResourceGroup` con i nomi scelti durante la creazione di queste risorse. `amsaccount` è il nome dell'account di Servizi multimediali di Azure al quale collegare l'entità servizio.

Il comando seguente restituisce un output `json`:

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Con questo comando viene generata una risposta simile alla seguente:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Se si vuole ottenere un output `xml` nella risposta, usare il comando seguente:

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
