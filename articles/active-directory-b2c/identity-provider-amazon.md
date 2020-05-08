---
title: Configurare l'iscrizione e l'accesso con un account Amazon
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Amazon alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3149d45f50c53209e3be6be6688c9c2ce8fb0555
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900370"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Amazon tramite Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Creare un'app in Amazon Developer Console

Per usare un account Amazon come provider di identità federato in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione in [Amazon Developer Services and Technologies](https://developer.amazon.com). Se non si ha già un account Amazon, è possibile iscriversi all' [https://www.amazon.com/](https://www.amazon.com/)indirizzo.

> [!NOTE]  
> Usare gli URL seguenti nel **passaggio 8** seguente, sostituendo `your-tenant-name` con il nome del tenant. Quando si immette il nome del tenant, usare tutte le lettere minuscole, anche se il tenant è definito con lettere maiuscole in Azure AD B2C.
> - Per le **origini consentite**, immettere`https://your-tenant-name.b2clogin.com` 
> - Per gli **URL restituiti consentiti**, immettere`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurare un account Amazon come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure ad B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**, quindi **Amazon**.
1. Immettere un **nome**. Ad esempio, *Amazon*.
1. Per **ID client**, immettere l'ID client dell'applicazione Amazon creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.
