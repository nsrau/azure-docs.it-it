---
title: Azure Active Directory B2C - Criteri personalizzati di risoluzione dei problemi | Microsoft Docs
description: Questo argomento descrive come risolvere i problemi con criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 2fa67038f2a214c1569fc65fd9f1beba394cb790
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017

---

# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: raccolta di log

Questo articolo illustra i passaggi per la raccolta di log da Azure AD B2C in modo che sia possibile diagnosticare con criteri personalizzati.

## <a name="use-application-insights"></a>Usare Application Insights

Azure Active Directory B2C supporta una funzionalità per l'invio di dati ad Application Insights,  servizio che consente di diagnosticare le eccezioni e di visualizzare i problemi di prestazioni delle applicazioni.

### <a name="setup-application-insights"></a>Configurazione di Application Insights

1. Accedere al [portale di Azure](https://portal.azure.com). Verificarsi di trovarsi nel tenant con la propria sottoscrizione di Azure (non il tenant di Azure AD B2C in uso).
1. Fare clic su **+ Nuovo** nel menu di navigazione a sinistra.
1. Cercare e selezionare **Application Insights** e quindi fare clic su **Crea**.
1. Completare il modulo e fare clic su **Crea**. Selezionare **Generale** come valore per **Tipo di applicazione**.
1. Dopo aver creato la risorsa, aprire la risorsa di Application Insights.
1. Fare clic su **Proprietà** nel menu a sinistra.
1. Copiare il valore di **Chiave di strumentazione** e salvarlo per la sezione successiva.

### <a name="set-up-the-custom-policy"></a>Configurare i criteri personalizzati

1. Aprire il file RP, ad esempio SignUpOrSignin.xml.
1. Aggiungere gli attributi seguenti all'elemento `<TrustFrameworkPolicy>`:

  ```XML
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Se non esiste già, aggiungere un nodo figlio `<UserJourneyBehaviors>` al nodo `<RelyingParty>`.
2. Aggiungere il nodo seguente come figlio dell'elemento `<UserJourneyBehaviors>`. Verificare di sostituire `{Your Application Insights Key}` con il valore di **Chiave di strumentazione** ottenuto nella sezione precedente.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` indica ad Application Insights di accelerare la telemetria nella pipeline di elaborazione, valida per lo sviluppo, ma vincolata a volumi elevati.
  * `ClientEnabled="true"` invia lo script di Application Insights lato client per tenere traccia della visualizzazione della pagina e degli errori del client (non necessari).
  * `ServerEnabled="true"` invia l'elemento JSON UserJourneyRecorder esistente come evento personalizzato ad Application Insights.
  Il codice XML finale avrà un aspetto simile al seguente:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Caricare i criteri.

### <a name="see-the-logs"></a>Visualizzare i log

>[!NOTE]
> I log di Application Insights possono essere visualizzati dopo un breve ritardo (meno di 5 minuti).

1. Aprire la risorsa di Application Insights creata nel [portale di Azure](https://portal.azure.com).
1. Nel menu **Panoramica** fare clic su **Analytics**.
1. Aprire una nuova scheda in Application Insights.
1. Di seguito viene indicato un elenco di query che è possibile usare per visualizzare i log

| Query | Descrizione |
|---------------------|--------------------|
traces | Consente di visualizzare tutti i log generati da Azure AD B2C |
traces \| where timestamp > ago(1d) | Consente di visualizzare tutti i log generati da Azure AD B2C nell'ultimo giorno

Per altre informazioni sullo strumento Analytics, vedere [qui](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).





