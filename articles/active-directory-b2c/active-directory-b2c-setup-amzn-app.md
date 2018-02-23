---
title: 'Azure Active Directory B2C: configurazione di Amazon | Documentazione Microsoft'
description: "Fornire la registrazione e l’accesso agli utenti con account Amazon nelle applicazioni protette da Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mtillman
editor: bryanla
ms.assetid: 77c099bb-a005-4d75-87f9-f61e3de48725
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: df69b075717e6b4311e336f5bd2d385c5f0e1f52
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account Amazon
## <a name="create-an-amazon-application"></a>Creare un'applicazione Amazon
Per usare Amazon come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione Amazon e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account Amazon. Se non si ha un account, è possibile crearlo nel sito [http://www.amazon.com/](http://www.amazon.com/).

1. Visitare il [Centro sviluppatori Amazon](https://login.amazon.com/) e accedere con le credenziali dell'account Amazon.
2. Se non è già stato fatto, fare clic su **Sign Up**(Iscrizione), seguire la procedura di registrazione per sviluppatori e accettare le condizioni.
3. Fare clic su **Registra nuova applicazione**.
   
    ![Registrazione di una nuova applicazione nel sito Web di Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Fornire informazioni sull'applicazione, come **Name** (Nome), **Description** (Descrizione) e **Privacy Notice URL** (URL informativa sulla privacy) e fare clic su **Save** (Salva).
   
    ![Inserimento delle informazioni sull'applicazione per la registrazione di una nuova applicazione in Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. Nella sezione **Web Settings** (Impostazioni Web) copiare i valori di **Client ID** (ID Client) e **Client Secret** (Segreto client). Sarà necessario fare clic sul pulsante **Show Secret** (Mostra segreto) per visualizzarlo. Sono necessari entrambi per configurare Amazon come provider di identità nel tenant. Fare clic su **Modifica** nella parte inferiore della sezione. **Client Segreto** è un'importante credenziale di sicurezza.
   
    ![Inserimento dell'ID Client e del Segreto client per la nuova applicazione in Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Immettere `https://login.microsoftonline.com` nel campo **Allowed JavaScript Origins** (Origini JavaScript consentite) e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **Allowed Return URLs** (URL restituiti consentiti). Sostituire **{tenant}** con il nome del tenant, ad esempio contoso.onmicrosoft.com. Fare clic su **Save**. Il valore **{tenant}** distingue tra maiuscole e minuscole.
   
    ![Inserimento delle origini JavaScript e degli URL restituiti per la nuova applicazione in Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configurare Amazon come provider di identità nel tenant
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "Amzn".
5. Fare clic su **Identity provider type** (Tipo di provider di identità), selezionare **Amazon** e fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità** e immettere l'ID client e il segreto client dell'applicazione Amazon creata in precedenza.
7. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione di Amazon.

