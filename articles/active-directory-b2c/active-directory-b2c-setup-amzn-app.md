---
title: Configurare l'iscrizione e l'accesso con un account Amazon tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Amazon alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 207a33931d7be88355241ea8c282dc7c39c9971c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169961"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Amazon tramite Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Creare un'applicazione Amazon

Per usare un account Amazon come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account Amazon, è possibile crearlo sul sito [https://www.amazon.com/](https://www.amazon.com/).

1. Accedere al [Centro sviluppatori Amazon](https://login.amazon.com/) con le credenziali dell'account Amazon.
2. Se non è già stato fatto, fare clic su **Sign Up**(Iscrizione), seguire la procedura di registrazione per sviluppatori e accettare le condizioni.
3. Selezionare **Registra nuova applicazione**.
4. Immettere un **Name**, una **Descrizione** e un **URL dell'informativa sulla Privacy**, quindi fare clic su **Salva**. L'Informativa sulla privacy è una pagina gestita dall'utente che fornisce informazioni sulla privacy per gli utenti.
5. Nella sezione **Impostazioni Web** copiare i valori dell'**ID client**. Selezionare **Mostra il segreto** per ottenere il segreto client e quindi copiarlo. Sono necessari entrambi per configurare un account Amazon come provider di identità nel tenant. **Client Segreto** è un'importante credenziale di sicurezza.
6. Nella sezione **Impostazioni Web**, selezionare **Modifica**, quindi immettere `https://your-tenant-name.b2clogin.com` in **Allowed JavaScript Origins** (Origini JavaScript consentite) e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in **Allowed Return URLs** (URL restituiti consentiti). Sostituire `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C.
7. Fare clic su **Save**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurare un account Amazon come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Immettere un **Nome**. Ad esempio, immettere *Amazon*.
6. Fare clic su **Tipo di provider di identità**, selezionare **Amazon** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID Client registrato in precedenza in **ID Client** e immettere il segreto Client registrato come **Segreto client** dell'applicazione Amazon creata in precedenza.
8. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione di Amazon.

