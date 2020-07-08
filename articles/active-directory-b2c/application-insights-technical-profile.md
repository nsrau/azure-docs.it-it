---
title: Definire un profilo tecnico Application Insights in un criterio personalizzato
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico Application Insights in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201413"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definire un profilo tecnico Application Insights in un criterio personalizzato Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) supporta l'invio diretto di dati di evento a [Application Insights](../azure-monitor/app/app-insights-overview.md) usando la chiave di strumentazione fornita per Azure ad B2C.  Con un profilo tecnico Application Insights, è possibile ottenere registri eventi dettagliati e personalizzati per i percorsi utente per:

* Ottenere informazioni dettagliate sul comportamento degli utenti.
* Risolvere i problemi relativi ai criteri in ambiente di sviluppo o di produzione.
* Misurare le prestazioni.
* Creare notifiche da Application Insights.


## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **handler** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C per Application Insights:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Nell'esempio seguente viene illustrato il profilo tecnico Application Insights comune. Altri profili tecnici Application Insights includono AzureInsights-Common per sfruttare la configurazione.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare a Application Insights. È anche possibile eseguire il mapping del nome dell'attestazione a un nome che si preferisce visualizzare in Application Insights. Nell'esempio seguente viene illustrato come inviare telemetrie a Application Insights. Le proprietà di un evento vengono aggiunte tramite la sintassi `{property:NAME}` , dove name è la proprietà aggiunta all'evento. DefaultValue può essere un valore statico o un valore risolto da uno dei [resolver di attestazioni](claim-resolver-overview.md)supportati.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuovi prima di inviare a Application Insights.

## <a name="persist-claims"></a>Rendere persistenti le attestazioni

L'elemento PersistedClaims non viene utilizzato.

## <a name="output-claims"></a>Attestazioni di output

Gli elementi OutputClaims e OutputClaimsTransformations non vengono utilizzati.

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento CryptographicKeys non viene usato.


## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| InstrumentationKey| Sì | Chiave di [strumentazione](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights, che verrà usata per la registrazione degli eventi. | 
| DeveloperMode| No | Valore booleano che indica se la modalità sviluppatore è abilitata. Valori possibili: `true` o `false` (impostazione predefinita). Questi metadati controllano il modo in cui gli eventi vengono memorizzati nel buffer. In un ambiente di sviluppo con un volume di eventi minimo, l'abilitazione della modalità sviluppatore genera eventi immediatamente inviati a Application Insights.|  
|DisableTelemetry |No |Valore booleano che indica se la telemetria deve essere abilitata o meno. Valori possibili: `true` o `false` (impostazione predefinita).| 


## <a name="next-steps"></a>Passaggi successivi

- [Creare una risorsa di Application Insights](../azure-monitor/app/create-new-resource.md)
- Informazioni su come [tenere traccia del comportamento degli utenti in Azure Active Directory B2C usando Application Insights](analytics-with-application-insights.md)
