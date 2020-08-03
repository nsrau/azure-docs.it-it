---
title: itsme OpenID Connect con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con itsme OIDC usando i criteri di flusso utente client_secret. itsme è un'app di ID digitale. Consente di accedere in modo sicuro senza lettori di schede, password, autenticazione a due fattori e codici PIN multipli.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba7875caa6a1db7638bfeafcfea1efa7b2462152
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489516"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Configurare itsme OpenID Connect (OIDC) con Azure Active Directory B2C

L'app itsme Digital ID consente di accedere in modo sicuro senza lettori di schede, password, autenticazione a due fattori o codici PIN multipli. L'app itsme offre un'autenticazione avanzata del cliente con un'identità verificata. Questo articolo illustra come integrare Azure AD B2C autenticazione con itsme OpenID Connect (OIDC) usando un criterio di flusso utente segreto client.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* [Un tenant Azure ad B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
* ID client, noto anche come codice partner, fornito da itsme.
* Il codice del servizio fornito da itsme.
* Il segreto client per l'account itsme.

## <a name="scenario-description"></a>Descrizione dello scenario

![diagramma dell'architettura di itsme](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Passaggio | Descrizione |
|------|------|
|1     | Nel sito Web o nell'applicazione includere il pulsante **Accedi con itsme** adattando il flusso di Azure ad B2C utente. Il flusso di interazione viene avviato quando l'utente fa clic su questo pulsante.  |
|2     | Azure AD B2C avvia il flusso di OpenID Connect inviando una richiesta di autorizzazione all'API Secret client itsme. È disponibile un endpoint di configurazione well-known/OpenID che contiene informazioni sugli endpoint.  |
|3     | L'ambiente itsme reindirizza l'utente alla pagina di identificazione itsme, consentendo all'utente di immettere il numero di telefono.  |
|4     | L'ambiente itsme riceve il numero di telefono dall'utente e ne convalida la correttezza.  |
|5     | Se il numero di telefono appartiene a un utente itsme attivo, viene creata un'azione per l'app itsme.  |
|6     | L'utente apre l'app itsme, controlla la richiesta e conferma l'azione.  |
|7     |  L'app informa l'ambiente itsme che l'azione è stata confermata. |
|8     |  L'ambiente itsme restituisce il codice di autorizzazione OAuth per Azure AD B2C. |
|9     |  Usando il codice di autorizzazione, il Azure AD B2C esegue una richiesta di token. |
| 10 | L'ambiente itsme controlla la richiesta del token e, se ancora valido, restituisce il token di accesso OAuth e il token ID contenente le informazioni sull'utente richieste. |
| 11 | Infine, l'utente viene reindirizzato all'URL di reindirizzamento come utente autenticato.  |
|   |   |

## <a name="onboard-with-itsme"></a>Onboarding con itsme

1. Per creare un account con itsme, visitare itsme in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

2. Attivare l'account itsme inviando un messaggio di posta elettronica a onboarding@itsme.be . Si riceverà un **codice partner** e un **codice del servizio** che saranno necessari per la configurazione di B2C.

3. Dopo l'attivazione dell'account partner itsme, si riceverà un messaggio di posta elettronica con un collegamento unico al **segreto client**.

4. Per completare la configurazione, seguire le istruzioni riportate in [itsme](https://business.itsme.be/en) .

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Configurare un nuovo provider di identità in Azure AD B2C

> [!NOTE]
> In assenza di un tenant, [creare un tenant di Azure AD B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.

2. In **servizi di Azure**selezionare **Azure ad B2C** (oppure selezionare **altri servizi** e usare la casella di ricerca **tutti i servizi** per cercare *Azure ad B2C*).

3. Selezionare **Provider di identità** e quindi selezionare **Nuovo provider di OpenID Connect**.

4. Compilare il modulo con le informazioni seguenti:

   |Proprietà | Valore |
   |------------ |------- |
   | Nome | itsme |
   | URL dei metadati | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>dove `<environment>` è `e2e` (ambiente di test) o `prd` (produzione)  |
   | ClientID     | **ID client**, noto anche come **codice partner**  |
   | Segreto client | Il **client_secret** |
   | Ambito  | servizio OpenID: indirizzo di posta elettronica del profilo YOURSERVICECODE [Phone] [indirizzo]  |
   |Tipo di risposta | codice |
   |Modalità di risposta | query |
   |Hint di dominio | *È possibile lasciare vuoto questo* |
   |UserID | sub |
   |Nome visualizzato | name |
   |Nome specificato | given_name |
   |Surname | family_name |
   |E-mail | email|

5. Selezionare **Salva**.

### <a name="configure-a-user-flow"></a>Configurare un flusso utente

1. Nel tenant di Azure AD B2C, in **criteri**, selezionare **flussi utente**.

2. Selezionare **nuovo flusso utente**.

3. Selezionare **iscrizione e accesso**, selezionare una versione e quindi fare clic su **Crea**.

4. Immettere un **Nome**.

5. Nella sezione **Identity Providers** selezionare **itsme**.

6. Selezionare **Crea**.

7. Aprire il flusso utente appena creato selezionando il nome del flusso utente.

8. Selezionare **Proprietà** e modificare i valori seguenti:

   * Modificare la **durata del token di accesso & ID (minuti)** a **5**.
   * Modificare la **durata della finestra temporale scorrevole del token di aggiornamento** **senza scadenza**.

### <a name="register-an-application"></a>Registrare un'applicazione

1. Nel tenant B2C in **Gestisci**selezionare **registrazioni app**  >  **nuova registrazione**.

2. Specificare un **nome** per l'applicazione e immettere l' **URI di reindirizzamento**. A scopo di test, immettere `https://jwt.ms` .

3. Assicurarsi che la funzionalità autenticazione a più fattori sia **disabilitata**.

4. Selezionare **Registra**.

   a. A scopo di test, selezionare **autenticazione**e in **concessione implicita**Selezionare le caselle di controllo **token di accesso** e **token ID** .  

   b. Selezionare **Salva**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Nel tenant B2C, in **criteri** selezionare **flussi utente**.

2. Selezionare il flusso utente creato in precedenza.

3. Selezionare **Esegui il flusso utente**.

   a. **Applicazione**: *selezionare l'app registrata*

   b. **URL di risposta**: *selezionare l'URL di reindirizzamento*

4. Viene visualizzata la pagina di **Identificazione** itsme.  

5. Immettere il numero di telefono cellulare e selezionare **Invia**.

6. Confermare l'azione nell'app itsme.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

* [Criteri personalizzati in AAD B2C](custom-policy-overview.md)

* [Introduzione ai criteri personalizzati in Azure AD B2C](custom-policy-get-started.md?tabs=applications)