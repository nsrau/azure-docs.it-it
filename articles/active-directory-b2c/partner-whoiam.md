---
title: Esercitazione per la configurazione di Azure Active Directory B2C con WhoIAM
titleSuffix: Azure AD B2C
description: In questa esercitazione si apprenderà come integrare Azure AD B2C autenticazione con WhoIAM per la verifica dell'utente.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 718ccbaa57ffe9f4ebaf4e8df448b602ba8cc3fa
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293151"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di WhoIAM con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite istruzioni su come configurare il sistema di gestione delle identità [WhoIAM](https://www.whoiam.ai/brims/) con personalizzazione del sistema nel proprio ambiente e integrarlo con Active Directory B2C (Azure ad B2C).

È un set di app e servizi distribuiti nell'ambiente in uso. Fornisce la verifica vocale, SMS e posta elettronica della base utente. È compatibile con la soluzione di gestione delle identità e degli accessi esistente ed è indipendente dalla piattaforma.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Un tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) collegato alla sottoscrizione di Azure.

- Un [account di valutazione](https://www.whoiam.ai/contact-us/)di WhoIAM.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di WhoIAM include i componenti seguenti:

- Tenant Azure AD B2C. Si tratta del server di autorizzazione che verifica le credenziali dell'utente in base ai criteri personalizzati definiti. È noto anche come provider di identità.

- Un portale di amministrazione per la gestione dei client e le relative configurazioni.

- Un servizio API che espone diverse funzionalità tramite gli endpoint.  

- Azure Cosmos DB, che funge da back-end sia per il portale di amministrazione di FALDe che per il servizio API.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Diagramma dell'architettura dell'integrazione di Azure AD B2C con WhoIAM.](media/partner-whoiam/whoiam-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina per avviare la richiesta di iscrizione o accesso a un'app che usa Azure AD B2C come provider di identità.
| 2. | Nell'ambito dell'autenticazione, l'utente richiede di verificare la proprietà della posta elettronica o del telefono o di usare la voce come fattore di verifica biometrico.  
| 3. | Azure AD B2C effettua una chiamata al servizio API di FALDe e passa l'indirizzo di posta elettronica dell'utente, il numero di telefono e la registrazione vocale.
| 4. | Il termine USA configurazioni predefinite, ad esempio i modelli di posta elettronica e SMS completamente personalizzabili, per interagire con l'utente nella rispettiva lingua in modo coerente con lo stile dell'app.
| 5. | Una volta completata la verifica dell'identità di un utente, viene restituito un token per Azure AD B2C per indicare il risultato della verifica. Azure AD B2C quindi concede all'utente l'accesso all'app o non riesce a eseguire il tentativo di autenticazione.  

## <a name="sign-up-with-whoiam"></a>Iscriversi a WhoIAM

1. Contattare [WhoIAM](https://www.whoiam.ai/contact-us/) e creare un account di falde.

2. Usare le linee guida per l'iscrizione rese disponibili e configurare i servizi di Azure seguenti:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): usato per l'archiviazione sicura delle password, ad esempio le password del servizio di posta elettronica.

    - [Servizio app Azure](https://azure.microsoft.com/services/app-service/): usato per ospitare i servizi dell'API e del portale di amministrazione.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): usato per autenticare gli utenti amministrativi per il portale di amministrazione.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): usato per archiviare e recuperare le impostazioni.

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications) (facoltativo): usato per accedere sia all'API che al portale di amministrazione.

3. Distribuire l'API di FALDe e il portale di amministrazione di trabocca nell'ambiente Azure.

4. Azure AD B2C esempi di criteri personalizzati sono disponibili nella documentazione di iscrizione a tesa. Seguire la documentazione per configurare l'app e usare la piattaforma tesa per la verifica dell'identità utente.  

Per ulteriori informazioni su WhoIAM, vedere la [documentazione del prodotto](https://www.whoiam.ai/brims/).

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant Azure AD B2C. In **Criteri** selezionare **Identity Experience Framework**.

2. Selezionare il **SignUpSignIn**creato in precedenza.

3. Selezionare **Esegui flusso utente** e quindi:

   a. Per **applicazione**, selezionare l'app registrata (l'esempio è JWT).

   b. In **URL di risposta**selezionare l' **URL di reindirizzamento**.

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account.

5. Il servizio trabocca viene chiamato durante il flusso, dopo la creazione dell'attributo utente. Se il flusso è incompleto, controllare che l'utente non sia salvato nella directory.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
