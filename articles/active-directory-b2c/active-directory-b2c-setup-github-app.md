---
title: Configurazione del provider di identità GitHub - Azure AD B2C | Microsoft Docs
description: Fornire la registrazione e l’accesso ai clienti con account GitHub nelle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: 17ec4ebd8406f56c431666340ca8834c5ccf9670
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account GitHub

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

In questo articolo viene illustrato come abilitare l'accesso agli utenti con account GitHub.

## <a name="create-a-github-oauth-application"></a>Creare un'applicazione OAuth per GitHub

Per usare GitHub come provider di identità in Azure AD B2C, è necessario creare un'applicazione OAuth per GitHub e inserire i parametri corretti.

1. Accedere a GitHub e andare alle impostazioni di [GitHub Developer](https://github.com/settings/developers).
1. Fare clic su **New OAuth App** (Nuova app OAuth)
1. Immettere **Application name** (Nome applicazione) e **Homepage URL** (URL della home page) personale.
1. Per **URL di callback autorizzazione**, immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Sostituire **{tenant}** con il nome del tenant di Azure AD B2C, ad esempio contosob2c.onmicrosoft.com.

    >[!NOTE]
    >Il valore di "tenant" deve essere in formato minuscolo in **URL di accesso**.

1. Fare clic su **Register application**.
1. Salvare i valori **ID client** e **Segreto client**. Verranno usati nella sezione successiva.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Configurare GitHub come provider di identità nel tenant di Azure AD B2C

1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
1. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
1. Fare clic su **+Aggiungi** nella parte superiore del pannello.
1. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "GitHub".
1. Fare clic su **Tipo di provider di identità**, selezionare **GitHub** e fare clic su **OK**.
1. Fare clic su **Configura questo provider di identità** e immettere l'ID Client e il segreto client dell'applicazione OAuth di GitHub copiata in precedenza.
1. Fare clic su **OK** e su **Crea** per salvare la configurazione di GitHub.

## <a name="next-steps"></a>Passaggi successivi

Creare o modificare un [criterio predefinito](active-directory-b2c-reference-policies.md) e aggiungere GitHub come provider di identità.