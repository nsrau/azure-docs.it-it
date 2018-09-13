---
title: Configurare l'iscrizione e l'accesso con un account Amazon tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Amazon alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c64b32656db2d3b821833450b4e866b9e33e44cd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337346"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Amazon tramite Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Creare un'applicazione Amazon

Per usare un account Amazon come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account Amazon, è possibile crearlo sul sito [http://www.amazon.com/](http://www.amazon.com/).

1. Accedere al [Centro sviluppatori Amazon](https://login.amazon.com/) con le credenziali dell'account Amazon.
2. Se non è già stato fatto, fare clic su **Sign Up**(Iscrizione), seguire la procedura di registrazione per sviluppatori e accettare le condizioni.
3. Selezionare **Registra nuova applicazione**.
4. Immettere un **Name**, una **Descrizione** e un **URL dell'informativa sulla Privacy**, quindi fare clic su **Salva**.
5. Nella sezione **Impostazioni Web** copiare i valori dell'**ID client**. Selezionare **Mostra il segreto** per ottenere il segreto client e quindi copiarlo. Sono necessari entrambi per configurare un account Amazon come provider di identità nel tenant. **Client Segreto** è un'importante credenziale di sicurezza.
6. Nella sezione **Impostazioni Web**, selezionare **Modifica**, quindi immettere `https://{tenant}.b2clogin.com` in **Allowed JavaScript Origins** (Origini JavaScript consentite) e `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` in **Allowed Return URLs** (URL restituiti consentiti). Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c. 
7. Fare clic su **Save**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurare un account Amazon come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**. 

    ![Passare al tenant di Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Selezionare la directory](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Immettere un **Nome**. Ad esempio, immettere *Amazon*.
6. Fare clic su **Tipo di provider di identità**, selezionare **Amazon** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID Client registrato in precedenza in **ID Client** e immettere il segreto Client registrato come **Segreto client** dell'applicazione Amazon creata in precedenza.
8. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione di Amazon.

