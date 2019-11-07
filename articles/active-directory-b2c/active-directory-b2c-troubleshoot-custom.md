---
title: Risolvere i problemi relativi ai criteri personalizzati con Application Insights-Azure Active Directory B2C
description: Come configurare Application Insights per tracciare l'esecuzione dei criteri personalizzati.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b8bf26791ca6489c12e4f9538d56ae0f0f66cc8c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602032"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Raccogliere i log di Azure Active Directory B2C con Application Insights

Questo articolo illustra i passaggi per la raccolta di log da Active Directory B2C (Azure AD B2C) in modo da poter diagnosticare i problemi relativi ai criteri personalizzati. servizio che consente di diagnosticare le eccezioni e di visualizzare i problemi di prestazioni delle applicazioni. Azure AD B2C include una funzionalità per l'invio di dati a Application Insights.

I log attività dettagliati descritti in questa sezione devono essere abilitati **solo** durante lo sviluppo dei criteri personalizzati.

> [!WARNING]
> Non abilitare la modalità di sviluppo nell'ambiente di produzione. I log raccolgono tutte le attestazioni inviate da e verso i provider di identità. Gli sviluppatori si assumono la responsabilità di tutti i dati personali raccolti nei log del Application Insights. Questi log dettagliati vengono raccolti solo quando i criteri vengono inseriti in **modalità sviluppatore**.

## <a name="set-up-application-insights"></a>Configurare Application Insights

Se non ne è già presente uno, creare un'istanza di Application Insights nella sottoscrizione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene la sottoscrizione di Azure (non la directory di Azure ad B2C).
1. Selezionare **Crea una risorsa** nel menu di spostamento a sinistra.
1. Cercare e selezionare **Application Insights**, quindi selezionare **Crea**.
1. Completare il modulo, selezionare **Verifica + crea**, quindi selezionare **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.
1. In **Configura** in Application Insights menu selezionare **Proprietà**.
1. Registrare la **chiave di strumentazione** per l'uso in un passaggio successivo.

## <a name="configure-the-custom-policy"></a>Configurare i criteri personalizzati

1. Aprire il file relying party (RP), ad esempio *SignUpOrSignin. XML*.
1. Aggiungere gli attributi seguenti all'elemento `<TrustFrameworkPolicy>`:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se non esiste già, aggiungere un `<UserJourneyBehaviors>` nodo figlio al nodo `<RelyingParty>`. Deve trovarsi immediatamente dopo `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`.
1. Aggiungere il nodo seguente come figlio dell'elemento `<UserJourneyBehaviors>`. Assicurarsi di sostituire `{Your Application Insights Key}` con la chiave di **strumentazione** Application Insights registrata in precedenza.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` indica a ApplicationInsights di accelerare i dati di telemetria attraverso la pipeline di elaborazione. Ideale per lo sviluppo, ma vincolato a volumi elevati.
    * `ClientEnabled="true"` invia lo script lato client di ApplicationInsights per il rilevamento degli errori sul lato client e sulla visualizzazione pagina. È possibile visualizzarli nella tabella **browserTimings** nel portale di Application Insights. Impostando `ClientEnabled= "true"`, si aggiungono Application Insights allo script di pagina e si ottengono gli intervalli di caricamenti di pagina e chiamate AJAX, conteggi, dettagli delle eccezioni del browser e degli errori AJAX, nonché i conteggi degli utenti e delle sessioni. Questo campo è **facoltativo**e è impostato su `false` per impostazione predefinita.
    * `ServerEnabled="true"` invia l'elemento JSON UserJourneyRecorder esistente come evento personalizzato ad Application Insights.

    Ad esempio:

    ```XML
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Caricare i criteri.

## <a name="see-the-logs-in-application-insights"></a>Visualizza i log in Application Insights

Si verifica un breve ritardo, in genere meno di cinque minuti, prima che sia possibile visualizzare nuovi log in Application Insights.

1. Aprire la risorsa di Application Insights creata nel [portale di Azure](https://portal.azure.com).
1. Nel menu **Panoramica** selezionare **Analytics**.
1. Aprire una nuova scheda in Application Insights.

Di seguito è riportato un elenco di query che è possibile usare per visualizzare i log:

| Query | Descrizione |
|---------------------|--------------------|
`traces` | Consente di visualizzare tutti i log generati da Azure AD B2C |
`traces | where timestamp > ago(1d)` | Consente di visualizzare tutti i log generati da Azure AD B2C nell'ultimo giorno

Le voci possono essere lunghe. Per visualizzarle meglio è possibile esportarle in formato CSV.

Per altre informazioni sull'esecuzione di query, vedere [Panoramica delle query di log in monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Passaggi successivi

La community ha sviluppato un visualizzatore di percorsi utente per supportare gli sviluppatori di identità. Legge l'istanza di Application Insights e restituisce una visualizzazione strutturata degli eventi di percorso utente. Ottenere il codice sorgente e distribuirlo nella soluzione.

Il lettore di percorsi utente non è supportato da Microsoft e viene reso disponibile esclusivamente così com'è.

È possibile trovare la versione del visualizzatore che legge gli eventi da Application Insights su GitHub, qui:

[Azure-Samples/Active-Directory-B2C-Advanced-Policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
