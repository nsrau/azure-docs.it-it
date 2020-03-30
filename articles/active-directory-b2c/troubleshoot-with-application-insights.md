---
title: Risolvere i problemi relativi ai criteri personalizzati con Application InsightsTroubleshoot custom policies with Application Insights
titleSuffix: Azure AD B2C
description: Come configurare Application Insights per tracciare l'esecuzione dei criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186268"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Raccogliere i log di Azure Active Directory B2C con Application InsightsCollect Azure Active Directory B2C logs with Application Insights

Questo articolo illustra la procedura per la raccolta di log da Active Directory B2C (Azure AD B2C) in modo da poter diagnosticare i problemi con i criteri personalizzati. servizio che consente di diagnosticare le eccezioni e di visualizzare i problemi di prestazioni delle applicazioni. Azure AD B2C includes a feature for sending data to Application Insights.

I log attività dettagliati descritti di seguito devono essere abilitati **SOLO** durante lo sviluppo dei criteri personalizzati.

> [!WARNING]
> Non abilitare la modalità di sviluppo nell'ambiente di produzione. I log raccolgono tutte le attestazioni inviate da e verso i provider di identità. Lo sviluppatore si assume la responsabilità di tutti i dati personali raccolti nei log di Application Insights.You as the developer assume responsibility for any personal data collected in your Application Insights logs. Questi registri dettagliati vengono raccolti solo quando il criterio viene **inserito**in DEVELOPER MODE .

## <a name="set-up-application-insights"></a>Configurare Application Insights

Se non ne hai già uno, crea un'istanza di Application Insights nella sottoscrizione.

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Nel menu superiore selezionare il filtro **Directory e sottoscrizione** e quindi selezionare la directory che contiene la sottoscrizione di Azure (non la directory B2C di Azure AD).
1. Selezionare **Crea una risorsa** nel menu di navigazione a sinistra.
1. Cercare e selezionare **Application Insights**, quindi selezionare **Crea**.
1. Compilare il modulo, selezionare **Revisione e creazione**e quindi **Crea**.
1. Al termine della distribuzione, selezionare **Vai alla risorsa**.
1. In **Configura** nel menu Application Insights selezionare **Proprietà**.
1. Registrare la **chiave INSTRUMENTATION** da utilizzare in un passaggio successivo.

## <a name="configure-the-custom-policy"></a>Configurare il criterio personalizzato

1. Aprire il file della relying party (RP), ad esempio *SignUpOrSignin.xml*.
1. Aggiungere gli attributi seguenti all'elemento `<TrustFrameworkPolicy>`:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se non esiste già, aggiungere `<UserJourneyBehaviors>` un nodo `<RelyingParty>` figlio al nodo. Deve essere situato `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`immediatamente dopo .
1. Aggiungere il nodo seguente come figlio dell'elemento `<UserJourneyBehaviors>`. Assicurarsi di `{Your Application Insights Key}` sostituire con la chiave di **strumentazione** di Application Insights registrata in precedenza.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`indica a ApplicationInsights di accelerare i dati di telemetria tramite la pipeline di elaborazione. Buono per lo sviluppo, ma vincolato a volumi elevati.
    * `ClientEnabled="true"`invia lo script lato client di ApplicationInsights per tenere traccia della visualizzazione della pagina e degli errori sul lato client. È possibile visualizzarli nella tabella browserTimings nel portale di Application Insights.You can view these in the **browserTimings** table in the Application Insights portal. Impostando `ClientEnabled= "true"`, si aggiungono Application Insights allo script di pagina e si ottengono intervalli di caricamenti di pagine e chiamate AJAX, conteggi, dettagli delle eccezioni del browser e errori AJAX e conteggio di utenti e sessioni. Questo campo è **facoltativo** `false` ed è impostato su predefinito.
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

Si verifica un breve ritardo, in genere inferiore a cinque minuti, prima di poter visualizzare i nuovi log in Application Insights.There is a short delay, typically less than five minutes, before you can see new logs in Application Insights.

1. Aprire la risorsa di Application Insights creata nel [portale di Azure](https://portal.azure.com).
1. Nel menu **Panoramica,** seleziona **Analytics.**
1. Aprire una nuova scheda in Application Insights.

Di seguito è riportato un elenco di query che è possibile utilizzare per visualizzare i log:

| Query | Descrizione |
|---------------------|--------------------|
`traces` | Consente di visualizzare tutti i log generati da Azure AD B2C |
`traces | where timestamp > ago(1d)` | Consente di visualizzare tutti i log generati da Azure AD B2C nell'ultimo giorno

Le voci possono essere lunghe. Per visualizzarle meglio è possibile esportarle in formato CSV.

Per altre informazioni sull'esecuzione di query, vedere [Panoramica delle query di log in Monitoraggio di Azure.For](../azure-monitor/log-query/log-query-overview.md)more information about querying, see Overview of log queries in Azure Monitor .

## <a name="next-steps"></a>Passaggi successivi

La community ha sviluppato un visualizzatore di percorsi utente per supportare gli sviluppatori di identità. Legge l'istanza di Application Insights e restituisce una visualizzazione strutturata degli eventi di percorso utente. Ottenere il codice sorgente e distribuirlo nella soluzione.

Il lettore di percorsi utente non è supportato da Microsoft e viene reso disponibile rigorosamente così com'è.

È possibile trovare la versione del visualizzatore che legge gli eventi da Application Insights su GitHub, qui:

[Azure-Samples/active-directory-b2c-advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
