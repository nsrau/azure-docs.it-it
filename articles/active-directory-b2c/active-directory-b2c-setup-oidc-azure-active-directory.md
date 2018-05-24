---
title: 'Azure Active Directory B2C: aggiungere un provider di Azure AD usando criteri predefiniti | Microsoft Docs'
description: Informazioni su come aggiungere un provider di identità Open ID Connect (Azure AD)
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 7dac9545-d5f1-4136-a04d-1c5740aea499
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/27/2018
ms.author: parja
ms.openlocfilehash: 52a752df9cf199acf39596f49e7368bce27a8158
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2018
ms.locfileid: "32309016"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: accedere usando account di Azure AD tramite un criterio predefinito

>[!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica. Non usare la funzione negli ambienti di produzione.

Questo articolo illustra come abilitare l'accesso per gli utenti da una specifica organizzazione Azure Active Directory (Azure AD) tramite l'uso di criteri predefiniti.

## <a name="create-an-azure-ad-app"></a>Creare un'app di Azure AD

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD.

>[!NOTE]
> Nelle istruzioni seguenti vengono usati "contoso.com" per il tenant di Azure AD dell'organizzazione e "fabrikamb2c.onmicrosoft.com" come tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra in alto selezionare il proprio account. Nell'elenco **Directory** scegliere il tenant di Azure AD dell'organizzazione in cui si registrerà l'applicazione (contoso.com).
1. Fare clic su **Tutti i servizi** nel riquadro a sinistra e cercare "Registrazioni per l'app".
1. Selezionare **Registrazione nuova applicazione**.
1. Immettere un nome per l'applicazione, ad esempio `Azure AD B2C App`.
1. Selezionare **App Web/API** per il tipo di applicazione.
1. Per **URL accesso** immettere l'URL seguente, dove `yourtenant` viene sostituito dal nome del tenant di Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Il valore di "yourtenant" deve essere in formato minuscolo in **URL accesso**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Salvare l'ID applicazione che verrà usato nella sezione successiva come ID client.
1. Nel pannello **Impostazioni** selezionare **Chiavi**.
1. Immettere una **descrizione chiave** sotto la sezione **Password** e impostare la **durata** su "Nessuna scadenza". 
1. Fare clic su **Salva** e annotare il **valore** chiave risultante, che verrà usato nella sezione successiva come segreto client.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Configurare Azure AD come provider di identità nel tenant

1. Nella barra in alto selezionare il proprio account. Dall'elenco **Directory** scegliere il tenant Active Directory B2C (fabrikamb2c.onmicrosoft.com).
1. [Passare al menu delle impostazioni Active Directory B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
1. Nel menu delle impostazioni Active Directory B2C fare clic su **Provider di identità**.
1. Fare clic su **+Aggiungi** nella parte superiore del pannello.
1. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "Contoso Azure AD".
1. Fare clic su **Tipo di provider di identità**, selezionare **Open ID Connect** e fare clic su **OK**.
1. Fare clic su **Configura questo provider di identità**
1. Per **URL metadati** immettere l'URL seguente, dove `yourtenant` viene sostituito dal nome del tenant di Azure AD, ad esempi o`contoso.com`:

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Per l'**ID client** e il **segreto client** immettere l'ID applicazione e la chiave dalla sezione precedente.
1. Mantenere il valore predefinito per **Ambito**, che deve essere impostato su `openid`.
1. Mantenere il valore predefinito per **Tipo di risposta**, che deve essere impostato su `code`.
1. Mantenere il valore predefinito per **Modalità di risposta**, che deve essere impostato su `form_post`.
1. Facoltativamente immettere un valore per **Dominio**, ad esempio `ContosoAD`. Si tratta del valore da usare quando si fa riferimento a questo provider di identità specificando *domain_hint* nella richiesta. 
1. Fare clic su **OK**.
1. Fare clic su **Esegui mapping delle attestazioni di questo provider di identità**.
1. Per **ID utente** immettere `oid`.
1. Per **nome visualizzato** immettere `name`.
1. Per **Nome** immettere `given_name`.
1. Per **Cognome** immettere `family_name`.
1. Per **Indirizzo di posta elettronica** immettere `unique_name`.
1. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere il provider di identità di Azure AD appena creato a un criterio predefinito e fornire commenti e suggerimenti all'indirizzo [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
