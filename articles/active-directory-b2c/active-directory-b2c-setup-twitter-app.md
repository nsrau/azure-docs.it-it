---
title: 'Azure Active Directory B2C: configurazione di Twitter | Documentazione Microsoft'
description: Fornire l'iscrizione e l'accesso agli utenti con account Twitter nelle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 579a6841-9329-45b8-a351-da4315a6634e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/06/2017
ms.author: parakhj
ms.openlocfilehash: dde17c89c49223c75842f328f455c6637aa1915b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account Twitter

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter
Per usare Twitter come provider di identità in Azure Active Directory (Azure AD) B2C, si deve creare un'applicazione Twitter e inserire i parametri corretti. Per eseguire questa operazione è necessario un account sviluppatore di Twitter. Se non si ha un account, è possibile crearlo sul sito [https://dev.twitter.com/](https://dev.twitter.com/).

1. Passare al [sito Web per sviluppatori di Twitter](https://dev.twitter.com/) e accedere con le proprie credenziali.
2. Fare clic su **My apps** (App personali) in **Tools & Support** (Strumenti e supporto) e quindi fare clic su **Create New App** (Crea nuova app). 
3. Nel modulo immettere i valori desiderati per **Nome**, **Descrizione** e **Sito Web**.
4. Per **URL callback** immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Accertarsi di sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com.
5. Selezionare la casella per accettare il **contratto per gli sviluppatori** e fare clic su **Create your Twitter application** (Crea applicazione Twitter).
6. Quando l'app viene creata, fare clic su **Keys and Access Tokens** (Chiavi e token di accesso).
7. Copiare il valore di **Chiave utente** e **Segreto consumer**. Sono necessari entrambi per configurare Twitter come provider di identità nel tenant.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurare Twitter come provider di identità nel tenant,
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. ad esempio, immettere "Twitter".
5. Fare clic su **Tipo di provider di identità**, selezionare **Twitter** e fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità** e immettere la **Chiave utente** di Twitter per **ID client** e il **Segreto consumer** di Twitter per **Segreto client**.
7. Fare clic su **OK** e su **Crea** per salvare la configurazione di Twitter.

