---
title: Configurare l'iscrizione e l'accesso con un account LinkedIn
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account LinkedIn alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188101"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account LinkedIn tramite Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Creare un'applicazione su LinkedIn

Per usare un account LinkedIn come provider di [identità](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione nel tenant che lo rappresenta. Se non si dispone già di un account LinkedIn, è possibile registrarsi all'indirizzo [https://www.linkedin.com/](https://www.linkedin.com/).

1. Accedere al [sito Web di sviluppatori LinkedIn](https://www.developer.linkedin.com/) con le credenziali dell'account LinkedIn.
1. Selezionare **My Apps** (App personali) e quindi **Crea applicazione**.
1. Immettere **Nome società**, **Nome applicazione**, **Descrizione applicazione**, **Logo applicazione**, **Uso applicazione **, **URL del sito Web**, **Indirizzo di posta elettronica aziendale** e **Telefono (uff.)**.
1. Accettare il documento **LinkedIn API Terms of Use** (Condizioni d'uso dell'API LinkedIn) e fare clic su **Submit** (Invia).
1. Copiare i valori di **ID client** e **Segreto client**. È possibile trovarli in Chiavi di **autenticazione**. Sono necessari entrambi per configurare LinkedIn come provider di identità nel tenant. **Client Secret** è un'importante credenziale di sicurezza.
1. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` negli **URL di reindirizzamento autorizzati**. Sostituire `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C. Fare clic su **Aggiungi** e quindi su **Aggiorna**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurare un account LinkedIn come provider di identità

1. Accedere al [portale](https://portal.azure.com/) di Azure come amministratore globale del tenant B2C di Azure AD.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **Provider di identità**, quindi **LinkedIn**.
1. Immettere un **Nome**. Ad esempio, *LinkedIn*.
1. Per **ID client**immettere l'ID client dell'applicazione LinkedIn creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.

## <a name="migration-from-v10-to-v20"></a>Migrazione da v1.0 a v2.0

LinkedIn [ha recentemente aggiornato le API da v1.0 a v2.0.](https://engineering.linkedin.com/blog/2018/12/developer-program-updates) Come parte della migrazione, Azure AD B2C è in grado di ottenere il nome completo dell'utente LinkedIn solo durante l'iscrizione. Se un indirizzo di posta elettronica è uno degli attributi raccolti durante l'iscrizione, l'utente deve immettere manualmente l'indirizzo di posta elettronica e convalidarlo.
