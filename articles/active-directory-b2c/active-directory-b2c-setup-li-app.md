---
title: Configurare l'iscrizione e l'accesso con un account LinkedIn-Azure Active Directory B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account LinkedIn alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ddc42bc9315d50b8b8cdd40264de561d0edf5c7d
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622376"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account LinkedIn tramite Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Creare un'applicazione su LinkedIn

Per usare un account LinkedIn come [provider di identità](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si dispone già di un account LinkedIn, è possibile iscriversi [https://www.linkedin.com/](https://www.linkedin.com/)all'indirizzo.

1. Accedere al [sito Web di sviluppatori LinkedIn](https://www.developer.linkedin.com/) con le credenziali dell'account LinkedIn.
1. Selezionare **My Apps** (App personali) e quindi **Crea applicazione**.
1. Immettere **Nome società**, **Nome applicazione**, **Descrizione applicazione**, **Logo applicazione**, **Uso applicazione** , **URL del sito Web**, **Indirizzo di posta elettronica aziendale** e **Telefono (uff.)** .
1. Accettare il documento **LinkedIn API Terms of Use** (Condizioni d'uso dell'API LinkedIn) e fare clic su **Submit** (Invia).
1. Copiare i valori **ID client** e **Segreto client**. Si trovano nella sezione **Authentication Keys** (Chiavi di autenticazione) Sono necessari entrambi per configurare LinkedIn come provider di identità nel tenant. **Client Segreto** è un'importante credenziale di sicurezza.
1. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` negli **URL di reindirizzamento autorizzati**. Sostituire `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C. Fare clic su **Aggiungi** e quindi su **Aggiorna**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurare un account LinkedIn come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**e quindi fare clic su **LinkedIn**.
1. Immettere un **Nome**. Ad esempio, *LinkedIn*.
1. Per **ID client**, immettere l'ID client dell'applicazione LinkedIn creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.

## <a name="migration-from-v10-to-v20"></a>Migrazione dalla versione 1.0 alla versione 2.0

LinkedIn ha [aggiornato di recente le API dalla versione 1.0 alla versione 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Come parte della migrazione, Azure AD B2C è in grado di ottenere solo il nome completo dell'utente LinkedIn durante l'iscrizione. Se un indirizzo di posta elettronica è uno degli attributi raccolti durante l'iscrizione, l'utente deve immettere manualmente l'indirizzo di posta elettronica e convalidarlo.
