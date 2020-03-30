---
title: Definire un profilo tecnico di Application Insights in un criterio personalizzatoDefine an Application Insights technical profile in a custom policy
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico di Application Insights in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108575"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definire un profilo tecnico di Application Insights in un criterio personalizzato di Azure AD B2CDefine an Application Insights technical profile in an Azure AD B2C custom policy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) supporta l'invio di dati di evento direttamente ad [Application Insights](../azure-monitor/app/app-insights-overview.md) usando la chiave di strumentazione fornita ad Azure AD B2C.  Con un profilo tecnico di Application Insights, è possibile ottenere log eventi dettagliati e personalizzati per i percorsi utente per:With an Application Insights technical profile, you can get detailed and customized event logs for your user journeys to:

* Ottenere informazioni dettagliate sul comportamento degli utenti.
* Risolvere i problemi relativi ai criteri in ambiente di sviluppo o di produzione.
* Misurare le prestazioni.
* Creare notifiche da Application Insights.


## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **del gestore** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C per Application Insights:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Nell'esempio seguente viene illustrato il profilo tecnico comune di Application Insights.The following example shows the common Application Insights technical profile. Altri profili tecnici di Application Insights includono AzureInsights-Common per sfruttare la configurazione.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Attestazioni di input

L'elemento InputClaims contiene un elenco di attestazioni da inviare a Application Insights.The **InputClaims** element contains a list of claims to send to Application Insights. You can also map the name of your claim to a name you prefer to appear in Application Insights. Nell'esempio seguente viene illustrato come inviare telemetrie ad Application Insights.The following example shows how to send telemetries to Application Insights. Le proprietà di un evento `{property:NAME}`vengono aggiunte tramite la sintassi , dove NAME è property aggiunta all'evento. DefaultValue può essere un valore statico o un valore risolto da uno dei resolver di [attestazioni](claim-resolver-overview.md)supportati.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi InputClaimsTransformation usati per modificare le attestazioni di input o generarne di nuove prima dell'invio ad Application Insights.The InputClaimsTransformations element may contain a collection of **InputClaimsTransformation** elements that are used to modify the input claims or generate new one before sending to Application Insights.

## <a name="persist-claims"></a>Rendere persistenti le attestazioni

L'elemento PersistedClaims non viene utilizzato.

## <a name="output-claims"></a>Attestazioni di output

Gli elementi OutputClaims e OutputClaimsTransformations non vengono utilizzati.

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento CryptographicKeys non viene usato.


## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| InstrumentationKey| Sì | Chiave di [strumentazione](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)di Application Insights , che verrà utilizzata per la registrazione degli eventi. | 
| DeveloperMode| No | Valore booleano che indica se è abilitata la modalità sviluppatore. Valori `true` possibili: `false` o (predefinito). Questi metadati controllano il modo in cui gli eventi vengono memorizzati nel buffer. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to Application Insights.|  
|DisableTelemetry |No |Valore booleano che indica se la telemetria deve essere abilitata o meno. Valori `true` possibili: `false` o (predefinito).| 


## <a name="next-steps"></a>Passaggi successivi

- [Creare una risorsa di Application InsightsCreate an Application Insights resource](../azure-monitor/app/create-new-resource.md)
- Informazioni su come [tenere traccia del comportamento degli utenti in Azure Active Directory B2C usando Application Insights](analytics-with-application-insights.md)
