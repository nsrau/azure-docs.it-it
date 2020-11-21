---
title: Registrare un'app per le risorse nell'API Azure AD-Azure per FHIR
description: Registrare un'app di risorsa (o API) in Azure Active Directory, in modo che le applicazioni client possano richiedere l'accesso alla risorsa durante l'autenticazione.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024483"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrare un'applicazione della risorsa in Azure Active Directory

Questo articolo illustra come registrare un'applicazione di risorse (o API) in Azure Active Directory. Un'applicazione della risorsa è una rappresentazione Azure Active Directory dell'API del server FHIR e le applicazioni client possono richiedere l'accesso alla risorsa durante l'autenticazione. L'applicazione della risorsa è nota anche come *destinatari* nel gergo OAuth.

## <a name="azure-api-for-fhir"></a>API di Azure per FHIR

Se si usa l'API di Azure per FHIR, viene creata automaticamente un'applicazione della risorsa quando si distribuisce il servizio. Fino a quando si usa l'API di Azure per FHIR nello stesso tenant di Azure Active Directory in cui si distribuisce l'applicazione, è possibile ignorare questa guida dettagliata e distribuire invece l'API di Azure per FHIR per iniziare.

Se si usa un tenant di Azure Active Directory diverso (non associato alla sottoscrizione), è possibile importare l'applicazione della risorsa API di Azure per FHIR nel tenant con PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

oppure usare l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Server FHIR per Azure

Se si usa il server FHIR open source per Azure, seguire i passaggi nel [repository GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) per registrare un'applicazione della risorsa. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come registrare un'applicazione della risorsa in Azure Active Directory. Successivamente, registrare l'applicazione client riservata.
 
>[!div class="nextstepaction"]
>[Registrare un'applicazione client riservata](register-confidential-azure-ad-client-app.md)