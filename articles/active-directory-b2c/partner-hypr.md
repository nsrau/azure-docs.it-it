---
title: Esercitazione per la configurazione di Azure Active Directory B2C con HYPR
titleSuffix: Azure AD B2C
description: Esercitazione per la configurazione di Azure Active Directory B2C con HYPR per l'autenticazione dei clienti affidabile senza password
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953764"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di HYPR con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite istruzioni su come configurare Azure AD B2C con [HYPR](https://get.hypr.com). Con Azure AD B2C come provider di identità, è possibile integrare HYPR con le applicazioni dei clienti per fornire agli utenti un'autenticazione senza password vera e propria. HYPR sostituisce le password con crittografia a chiave pubblica, eliminando la frode, il phishing e il riutilizzo delle credenziali.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Tenant Azure ad B2C](./tutorial-create-tenant.md). Il tenant è collegato alla sottoscrizione di Azure.

- Un tenant cloud HYPR, ottenere un [account di valutazione](https://get.hypr.com/free-trial)gratuito.

- Un dispositivo mobile dell'utente registrato usando le API REST di HYPR o HYPR Device Manager nel tenant di HYPR. Ad esempio, è possibile usare [HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) per completare questa attività.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di HYRP include i componenti seguenti:

- Azure AD B2C: il server di autorizzazione, responsabile della verifica delle credenziali dell'utente, noto anche come provider di identità

- Applicazioni Web e per dispositivi mobili: app Web e per dispositivi mobili che si sceglie di proteggere con HYPR e Azure AD B2C. HYPR fornisce un potente SDK per dispositivi mobili anche un'app per dispositivi mobili che è possibile usare nelle piattaforme iOS e Android per autenticare l'autenticazione senza password.

- App per dispositivi mobili HYPR: l'app per dispositivi mobili HYPR può essere usata per eseguire questo esempio se preferisco non usare gli SDK per dispositivi mobili nelle tue applicazioni per dispositivi mobili.

- API REST HYPR: è possibile usare le API HYPR per la registrazione e l'autenticazione del dispositivo utente. Queste API sono disponibili [qui](https://apidocs.hypr.com).

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Screenshot per HYPR-Architecture-diagram](media/partner-hypr/hypr-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. Gli utenti selezionano l'accesso/iscrizione e immettono il nome utente nella pagina.
| 2. | L'applicazione invia gli attributi utente a Azure AD B2C per l'identificazione della verifica.
| 3. | Azure AD B2C raccoglie gli attributi utente e invia gli attributi a HYPR per autenticare l'utente tramite l'app per dispositivi mobili HYPR.
| 4. | HYPR invia una notifica push al dispositivo mobile utente registrato per l'autenticazione certificata Fast Identity online (FIDO). Può trattarsi di un dito utente, biometrico o di un pin decentralizzato.  
| 5. | Dopo che l'utente ha riconosciuto la notifica push, all'utente viene concesso o negato l'accesso all'applicazione del cliente in base ai risultati della verifica.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configurare i criteri di Azure AD B2C

1. Passare al [criterio Azure ad B2C HYPR](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) nella cartella criteri.

2. Segui questo [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) per scaricare lo [Starter Pack le LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configurare i criteri per il tenant Azure AD B2C.

>[!NOTE]
>Aggiornare i criteri specificati per la correlazione con il tenant specifico.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare **Framework esperienza di identità**.

2. Selezionare il **SignUpSignIn** creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione**: selezionare l'app registrata (l'esempio è JWT)

   b. **URL di risposta**: selezionare l' **URL di reindirizzamento**

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account

5. HYPR verrà chiamato durante il flusso, dopo la creazione dell'attributo utente. Se il flusso è incompleto, controllare che l'utente non sia salvato nella directory.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](./custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)