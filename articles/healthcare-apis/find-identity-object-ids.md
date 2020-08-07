---
title: Trovare ID oggetto Identity per l'autenticazione-API di Azure per FHIR
description: Questo articolo illustra come individuare gli ID oggetto identità necessari per configurare l'autenticazione per l'API di Azure per FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 5b42d61d59a3c816c3b664297470cfbf91f17439
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851767"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Trovare gli ID oggetto Identity per la configurazione dell'autenticazione

In questo articolo si apprenderà come trovare gli ID oggetto Identity necessari durante la configurazione dell'API di Azure per FHIR per [usare un tenant Active Directory esterno o secondario](configure-local-rbac.md) per il piano dati.

## <a name="find-user-object-id"></a>Trova ID oggetto utente

Se si dispone di un utente con nome utente `myuser@consoso.com` , è possibile individuare gli utenti `ObjectId` usando il comando PowerShell seguente:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

in alternativa è possibile usare l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az ad user show --id myuser@consoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>Trova ID oggetto entità servizio

Si supponga di aver registrato un' [app client del servizio](register-service-azure-ad-client-app.md) e di voler consentire a questo client del servizio di accedere all'API di Azure per FHIR, è possibile trovare l'ID oggetto per l'entità servizio client con il comando PowerShell seguente:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

dove `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` è l'ID dell'applicazione client del servizio. In alternativa, è possibile utilizzare il `DisplayName` del client del servizio:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Se si usa l'interfaccia della riga di comando di Azure, è possibile usare:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>Trovare un ID oggetto gruppo di sicurezza

Se si vuole trovare l'ID oggetto di un gruppo di sicurezza, è possibile usare il comando di PowerShell seguente:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Dove `mygroup` è il nome del gruppo a cui si è interessati.

Se si usa l'interfaccia della riga di comando di Azure, è possibile usare:

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come trovare gli ID oggetto Identity necessari per configurare l'API di Azure per FHIR per l'uso di un tenant Azure Active Directory esterno o secondario. Leggere quindi le informazioni su come usare gli ID oggetto per configurare le impostazioni RBAC locali:
 
>[!div class="nextstepaction"]
>[Configurare le impostazioni RBAC locali](configure-local-rbac.md)
