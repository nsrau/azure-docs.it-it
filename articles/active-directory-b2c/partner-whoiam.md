---
title: Esercitazione per la configurazione di Azure Active Directory B2C con whoIam
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con whoIam per la verifica degli utenti
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817569"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di WhoIAM con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite istruzioni su come configurare il sistema di gestione delle identità [WhoIAM](https://www.whoiam.ai/brims/) personalizzato nel proprio ambiente e integrarlo con Azure ad B2C.

WhoIAM è un set di app e servizi che vengono distribuiti nell'ambiente in uso. Fornisce la verifica vocale, SMS e posta elettronica della base utente. È compatibile con la soluzione di gestione delle identità e degli accessi esistente ed è indipendente dalla piattaforma.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Un tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) collegato alla sottoscrizione di Azure.

- Un [account di valutazione](https://www.whoiam.ai/contact-us/) di WhoIAM

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di WhoIAM include i componenti seguenti:

- Tenant di Azure AD B2C: il server di autorizzazione, responsabile della verifica delle credenziali dell'utente in base ai criteri personalizzati definiti nel tenant. È noto anche come provider di identità.

- Un portale di amministrazione per la gestione dei client e le relative configurazioni

- Un servizio API che espone diverse funzionalità tramite gli endpoint  

- Azure Cosmos DB usato come back-end per il portale di amministrazione di trabocca e per il servizio API

Il diagramma dell'architettura seguente illustra l'implementazione di.

![screenshot per whoiam-Architecture-diagram](media/partner-whoiam/whoiam-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. L'utente avvia una richiesta di iscrizione o accesso a un'app che usa Azure AD B2C come provider di identità.
| 2. | Nell'ambito dell'autenticazione, l'utente richiede di verificare la proprietà della posta elettronica o del telefono o di usare la voce come fattore di verifica biometrico.  
| 3. | Azure AD B2C effettua una chiamata al servizio API di FALDe passando l'indirizzo di posta elettronica dell'utente, il numero di telefono e la registrazione vocale
| 4. | Il bordo usa configurazioni predefinite, ad esempio i modelli di posta elettronica e SMS completamente personalizzabili, per interagire con l'utente nella rispettiva lingua, coerente con l'aspetto dell'app.
| 5. | Una volta completata la verifica dell'identità di un utente, viene restituito un token a Azure AD B2C che indica il risultato della verifica. Azure AD B2C quindi concede all'utente l'accesso all'app o non riesce a eseguire il tentativo di autenticazione.  

## <a name="onboard-with-whoiam"></a>Onboarding con WhoIAM

1. Contattare [WhoIAM](https://www.whoiam.ai/contact-us/) e creare un account di falde.

2. Una volta creato un account, usare le linee guida di onboarding rese disponibili e configurare i servizi di Azure seguenti:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) : usato per l'archiviazione sicura delle password, ad esempio le password del servizio di posta elettronica.

    - [Servizio app Azure](https://azure.microsoft.com/services/app-service/) : usato per ospitare i servizi dell'API e del portale di amministrazione.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) : usato per autenticare gli utenti amministratori per il portale di amministrazione

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) : usato per archiviare e recuperare le impostazioni

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (facoltativo): usato per accedere sia all'API che al portale di amministrazione

3. Distribuire l'API di FALDe e il portale di amministrazione di trabocca nell'ambiente Azure.

4. Azure AD B2C esempi di criteri personalizzati sono disponibili nella documentazione sull'onboarding. Seguire il documento per configurare l'app e usare la piattaforma tesa per la verifica dell'identità utente.  

Per ulteriori informazioni su WhoIAM, vedere la documentazione del [prodotto](https://www.whoiam.ai/brims/)

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare **Framework esperienza di identità**.

2. Selezionare il **SignUpSignIn**creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione**: selezionare l'app registrata (l'esempio è JWT)

   b. **URL di risposta**: selezionare l' **URL di reindirizzamento**

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account

5. Il servizio trabocca viene chiamato durante il flusso, dopo la creazione dell'attributo utente. Se il flusso è incompleto, controllare che l'utente non sia salvato nella directory.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
