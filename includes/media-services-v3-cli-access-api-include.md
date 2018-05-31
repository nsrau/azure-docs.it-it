---
title: File di inclusione
description: File di inclusione
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: acb9bdf294dd66005df203f957c155540b658698
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33830103"
---
## <a name="access-the-media-services-api"></a>Accedere all'API di Servizi multimediali

Per connettersi alle API di Servizi multimediali di Azure, si usa l'autenticazione dell'entità servizio di Azure AD. Il comando seguente consente di creare un'applicazione Azure AD e di collegare un'entità servizio all'account. Per configurare l'app .NET, si useranno i valori restituiti, come illustrato nel passaggio seguente.

Prima di eseguire lo script, è possibile sostituire `amsaccount` e `amsResourceGroup` con i nomi scelti durante la creazione di queste risorse. `amsaccount` è il nome dell'account di Servizi multimediali di Azure al quale collegare l'entità servizio. <br/>Nel comando seguente si usa l'opzione `xml` che restituisce il codice XML da incollare nel file app.config. Se si omette l'opzione `xml`, la risposta sarà disponibile in formato `json`.

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

Con questo comando verrà generata una risposta simile alla seguente:

```xml
<add key="Region" value="West US 2" />
<add key="ResourceGroup" value="amsResourceGroup" />
<add key="AadEndpoint" value="https://login.microsoftonline.com" />
<add key="AccountName" value="amsaccount" />
<add key="SubscriptionId" value="111111111-0000-2222-3333-55555555555" />
<add key="ArmAadAudience" value="https://management.core.windows.net/" />
<add key="AadTenantId" value="2222222222-0000-2222-3333-6666666666666" />
<add key="AadSecret" value="33333333-0000-2222-3333-55555555555" />
<add key="AadClientId" value="44444444-0000-2222-3333-55555555555" />
<add key="ArmEndpoint" value="https://management.azure.com/" />
```

### <a name="configure-the-sample-app"></a>Configurare l'app di esempio

Per eseguire l'app e accedere alle API di Servizi multimediali, è necessario specificare i valori di accesso corretti nel file app.config. 

1. Aprire Visual Studio.
2. Passare alla soluzione clonata.
3. In Esplora soluzioni espandere il progetto *EncodeAndStreamFiles*.
4. Impostare questo progetto come progetto di avvio.
5. Aprire il file app.config.
6. Sostituire i valori di appSettings con quelli ottenuti nel passaggio precedente.

 ```xml
 <add key="Region" value="value" />
 <add key="ResourceGroup" value="value" />
 <add key="AadEndpoint" value="value" />
 <add key="AccountName" value="value" />
 <add key="SubscriptionId" value="value" />
 <add key="ArmAadAudience" value="value" />
 <add key="AadTenantId" value="value" />
 <add key="AadSecret" value="value" />
 <add key="AadClientId" value="value" />
 <add key="ArmEndpoint" value="value" />
 ```    
 
7. Premere CTRL+MAIUSC+B per compilare la soluzione.
