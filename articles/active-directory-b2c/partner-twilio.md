---
title: Twilio verificare l'app con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Informazioni su come integrare un'app di pagamento online di esempio in Azure AD B2C con l'API di verifica Twilio. Sono conformi ai requisiti delle transazioni PSD2 (Payment Services Directive 2) tramite collegamento dinamico e autenticazione avanzata del cliente.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 57dbec2b91d313c9c93c141c9f3ec839a299d47d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385485"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Integrazione dell'app Twilio Verify con Azure Active Directory B2C

In questa procedura dettagliata si apprenderà come integrare un'app di pagamento online di esempio in Azure Active Directory B2C (Azure AD B2C) con l'API Twilio Verify. Utilizzando Twilio Verify app, Azure AD B2C clienti possono essere conformi ai requisiti di transazione PSD2 (Payment Services Directive 2) tramite il collegamento dinamico e la forte autenticazione del cliente.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* [Un tenant Azure ad B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
* Un [account di valutazione](https://www.twilio.com/try-twilio) in twilio.

## <a name="scenario-description"></a>Descrizione dello scenario

I componenti seguenti costituiscono la soluzione Twilio:

- App Web demo .NET PSD2, che consente di eseguire l'accesso o l'iscrizione ed eseguire una transazione fittizia ad alto rischio.
- Azure AD B2C i criteri di accesso e iscrizione combinati.
- Criteri di Azure AD B2C integrati con Twilio Verify API using `id_token_hint` .
- App Web .NET che ospita un `.well-known` endpoint OpenIdConnect per consentire la convalida di un oggetto `id_token_hint` .


    ![flusso Twilio](media/partner-twilio/twilio-flow.png)

|      |      |
|------|------|
| 1     | L'utente avvia l'accesso o l'iscrizione all'app demo PSD2. L'utente viene autenticato tramite il Azure AD B2C i criteri di accesso e iscrizione combinati. All'applicazione viene restituito un token. Al momento dell'iscrizione, il numero di telefono dell'utente viene verificato con SMS/telefono e registrato nell'account Azure AD B2C.     |
| 2     | L'utente avvia una transazione a rischio elevato, ad esempio il trasferimento di $50,00. Il token di accesso corrente dell'utente viene valutato per PolicyId per determinare se l'utente ha già eseguito l'autenticazione tramite un criterio personalizzato Step-up.     |
| 3     | L'applicazione registra il valore di transazione e il beneficiario, $50,00 e John Doe, e genera un token firmato. Questo token è denominato `id_token_hint` e contiene l'attestazione `amount:$500, payee:john doe` . `id_token_hint`Viene inviato insieme alla richiesta al Azure ad B2C criteri personalizzati, integrato con Twilio.     |
| 4     | Azure AD B2C verifica la firma del id_token_hint controllando l'endpoint di OpenID Connect per le applicazioni `/.well-known` . Dopo la verifica, estrae le attestazioni da questo token, in particolare `amount` e `payee` . L'utente visualizzerà una pagina per verificare il numero di telefono cellulare tramite SMS.     |
| 5     | L'utente richiede di verificare il numero di telefono tramite SMS e Azure AD B2C esegue una richiesta dell'API REST per l'endpoint dell'API verify di Twilio. Invia inoltre la transazione `amount` e `payee` come parte del processo PSD2 per generare il codice di sola esecuzione (OTP). Twilio Invia un messaggio SMS al numero di telefono registrato dell'utente.     |
| 6     |  L'utente immette il OTP ricevuto nel messaggio SMS e lo invia al Azure AD B2C. Azure AD B2C esegue una richiesta API con questa OTP nell'API verify di Twilio per verificare che OTP sia corretto. Infine, viene emesso un token per l'applicazione con un nuovo PolicyId che indica che l'utente ha completato l'autenticazione.    |
|      |      |

## <a name="onboard-with-twilio"></a>Onboarding con Twilio

1. Ottenere un [account di valutazione](https://www.twilio.com/try-twilio) in twilio.

2. Acquistare un numero di telefono in Twilio come descritto in [questo articolo](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)

3. Passare a [verificare l'API](https://www.twilio.com/console/verify/services) nella console di Twilio e seguire le [istruzioni](https://www.twilio.com/docs/verify/verifying-transactions-psd2) per creare un servizio e abilitare l'opzione PSD2.  

## <a name="configure-the-psd2-demo-app"></a>Configurare l'app demo PSD2

1. Aprire la soluzione B2C-WebAPI-DotNet e sostituire i valori seguenti con i valori specifici del tenant nel web.config:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. L'app Web ospita anche il generatore di hint per token ID e l'endpoint dei metadati.
   - Creare il certificato di firma come descritto in questa [Descrizione di esempio](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate).
   - Aggiornare le righe seguenti in base al certificato nel web.config:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Caricare l'applicazione demo nel provider di hosting preferito. In [questa descrizione di esempio](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)viene fornita una guida per il servizio app Azure, incluse le istruzioni per il caricamento del certificato.

4. Aggiornare la registrazione dell'applicazione Azure AD B2C aggiungendo un URL di risposta equivalente all'URL in cui è ospitata l'applicazione.

5. Aprire i file dei criteri e sostituire tutte le istanze di  `contoso` con il nome del tenant.

6. Trovare il profilo tecnico dell'API REST di Twilio **personalizzato-SMS-registra**. Aggiornare  `ServiceURL`   con il AccountSid Twilio e il numero da al numero di telefono acquistato.

7. Trovare i profili tecnici dell'API REST di Twilio, **TwilioRestAPI-Verify-Step1**   e **TwilioRestAPI-Verify-2**e aggiornare  `ServiceURL`   con il Twilio AccountSid.

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

Aggiungere i file dei criteri a Azure AD B2C:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.

2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.

4. Passare a **Azure ad B2C**le chiavi dei criteri del Framework dell'esperienza di  >  **identità**  >  **Policy Keys**.

5. Aggiungere una nuova chiave con il nome **B2cRestTwilioClientId**. Selezionare **manuale**e specificare il valore di Twilio AccountSid.

6. Aggiungere una nuova chiave con il nome **B2cRestTwilioClientSecret**. Selezionare **manuale**e specificare il valore del token di autenticazione Twilio.

7. Caricare tutti i file di criteri nel tenant.

8. Personalizzare la stringa nella trasformazione delle attestazioni GenerateOTPMessageEnrol per il testo SMS di iscrizione.

## <a name="test-the-solution"></a>Testare la soluzione

* Passare all'applicazione e testare le azioni di accesso, iscrizione e invio di denaro.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- Vedere gli esempi di [codice di integrazione](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2) di GitHub per Twilio  

- [Criteri personalizzati in AAD B2C](custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in AAD B2C](custom-policy-get-started.md?tabs=applications)
