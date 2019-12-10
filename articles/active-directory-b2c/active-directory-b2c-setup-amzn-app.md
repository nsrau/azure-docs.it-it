---
title: Configurare l'iscrizione e l'accesso con un account Amazon
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Amazon alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e43c880fb2e38a235c38e19854da9e2b6e2a10c0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947719"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Amazon tramite Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Creare un'applicazione Amazon

Per usare un account Amazon come [provider di identità](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory B2C (Azure ad B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si ha già un account Amazon, è possibile iscriversi all' [https://www.amazon.com/](https://www.amazon.com/).

1. Accedere al [Centro sviluppatori Amazon](https://login.amazon.com/) con le credenziali dell'account Amazon.
1. Se non è già stato fatto, fare clic su **Sign Up**(Iscrizione), seguire la procedura di registrazione per sviluppatori e accettare le condizioni.
1. Selezionare **Registra nuova applicazione**.
1. Immettere un **Name**, una **Descrizione** e un **URL dell'informativa sulla Privacy**, quindi fare clic su **Salva**. L'Informativa sulla privacy è una pagina gestita dall'utente che fornisce informazioni sulla privacy per gli utenti.
1. Nella sezione **Impostazioni Web** copiare i valori dell'**ID client**. Selezionare **Mostra il segreto** per ottenere il segreto client e quindi copiarlo. Sono necessari entrambi per configurare un account Amazon come provider di identità nel tenant. **Client Segreto** è un'importante credenziale di sicurezza.
1. Nella sezione **Impostazioni Web**, selezionare **Modifica**, quindi immettere `https://your-tenant-name.b2clogin.com` in **Allowed JavaScript Origins** (Origini JavaScript consentite) e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in **Allowed Return URLs** (URL restituiti consentiti). Sostituire `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C.
1. Fare clic su **Salva**

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurare un account Amazon come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**, quindi **Amazon**.
1. Immettere un **Nome**. Ad esempio, *Amazon*.
1. Per **ID client**, immettere l'ID client dell'applicazione Amazon creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.
