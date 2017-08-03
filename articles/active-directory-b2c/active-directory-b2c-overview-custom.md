---
title: 'Azure Active Directory B2C: attributi personalizzati | Microsoft Docs'
description: Un argomento sui criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1ff398a4-2079-4615-94f1-57de22c0aad6
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 0a0d91d622ed72ed22cfaaa0350b31ca653de483
ms.contentlocale: it-it
ms.lasthandoff: 06/01/2017

---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Definizione di criteri personalizzati

I criteri personalizzati sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory B2C. Mentre i **criteri predefiniti** sono predefiniti nel portale di Azure Active Directory B2C per le attività di identità più comuni, i criteri personalizzati possono essere modificati completamente dallo sviluppatore di identità per completare un numero quasi illimitato di attività. Continuare a leggere per determinare se i criteri personalizzati sono corretti per lo scenario di identità personale.

**La modifica dei criteri personalizzati non è adatta a tutti gli utenti.** La curva di apprendimento è complessa, il tempo di avvio è più lungo e le modifiche future ai criteri personalizzati richiedono competenze simili per poter essere mantenute. I criteri predefiniti devono essere considerati attentamente per lo scenario prima di usare i criteri personalizzati.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Confronto tra criteri predefiniti e personalizzati

| | Criteri predefiniti | Criteri personalizzati |
|-|-------------------|-----------------|
|Utenti di destinazione | Tutti gli sviluppatori di app con o senza competenze di identità | Professionisti di identità: integratori di sistemi, consulenti e team di identità interni. Hanno familiarità con i flussi OpenIDConnect e comprendono i provider di identità e l'autenticazione basata sulle attestazioni |
|Metodo di configurazione | Portale di Azure con un'interfaccia utente intuitiva | Modifica diretta dei file XML e caricamento nel portale di Azure |
|Personalizzazione dell'interfaccia utente | Personalizzazione completa dell'interfaccia utente, che include il supporto HTML, CSS e jscript, che richiede il dominio personalizzato<br><br>Supporto multilingue con stringhe personalizzate | Uguale |
| Personalizzazione degli attributi | Attributi standard e personalizzati | Uguale |
|Gestione delle sessioni e dei token | Token personalizzato e opzioni di sessione multiple | Uguale |
|Provider di identità| **Oggi**: provider social, predefinito, locale<br><br>**Futuro**: OIDC, SAML, OAuth basati su standard | **Oggi**: OIDC, SAML, OAuth basati su standard<br><br>**Futuro**: WsFed |
|Attività relative all'identità: esempi | Iscrizione o accesso ad account social e locali multipli<br><br>Reimpostazione delle password<br><br>Modifica del profilo<br><br>Scenari con autenticazione a più fattori<br><br>Personalizzare token e sessioni<br><br>Flussi di accesso ai token | Completare le stesse attività dei criteri predefiniti usando provider di identità personalizzati ambiti personalizzati<br><br>Effettuare il provisioning per l'utente in un altro sistema al momento della registrazione<br><br>Inviare un messaggio di posta elettronica di benvenuto con il proprio provider di servizi di posta elettronica<br><br>Usare un archivio utente esterno B2C<br><br>Convalidare le informazioni date dall'utente con un sistema attendibile tramite API |

## <a name="policy-files"></a>File dei criteri

Un criterio personalizzato è rappresentato come uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. Gli elementi XML definiscono: schema di attestazioni, trasformazioni di attestazioni, definizioni di contenuto, profili tecnico o provider di attestazioni e procedure di orchestrazione Userjourney, tra gli altri elementi.

Si consiglia di usare i tre tipi di file dei criteri:

- **Un file di BASE**, che contiene la maggior parte delle definizioni e per cui Azure offre un esempio completo.  Si consiglia di apportare un numero minimo di modifiche a questo file che contribuisce alla risoluzione dei problemi e alla manutenzione a lungo termine dei criteri
- **Un file di estensioni** che contiene le modifiche di configurazione univoche per il tenant
- **Un file Relying Party (RP)**, l'unico file incentrato sulle attività che viene richiamato direttamente dell'applicazione o dal servizio ed è noto anche come Relying Party.  Per altre informazioni leggere l'articolo sulle definizioni di file dei criteri.  Ogni attività univoca richiede un proprio Relying Party e, a seconda dei requisiti di personalizzazione, il numero potrebbe essere il "totale delle applicazioni per il numero totale di casi di uso".


I criteri predefiniti in Azure AD B2C seguono il modello a 3 file illustrato in precedenza, ma lo sviluppatore vede solo il file Relying Party (RP), mentre il portale apporta modifiche in background al file delle estensioni.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Concetti di base che è necessario conoscere quando si usano i criteri personalizzati

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure è dotato del servizio di identità del cliente e gestione dell'accesso, CIAM. Il servizio include:

1. Una directory utente sotto forma di Azure Active Directory con un scopo specifico accessibili tramite Microsoft Graph e che contenga i dati utente per gli account locali e gli account federati 
2. L'accesso al **framework per l'esperienza di identità** che orchestra la relazione di trust tra gli utenti e le entità e passa le attestazioni tra questi per completare un'attività di gestione all'identità o all'accesso 
3. Un servizio token di sicurezza (STS) che emette gli ID token, aggiorna i token e accede ai token, e alle asserzioni SAML equivalenti, e li convalida in modo da proteggere le risorse.

Azure Active Directory B2C interagisce i provider di identità, gli utenti, con altri sistemi e le directory dell'utente locale in sequenza per eseguire un'attività di identità, ad esempio l'accesso a un utente, la registrazione di un nuovo utente, la reimpostazione della password. La piattaforma sottostante, che stabilisce relazioni di trust tra più parti ed esegue questa procedura viene chiamata Framework di esperienza di identità e un criterio, che ha chiamato un percorso utente o un criterio di attendibilità framework, definisce in modo esplicito gli attori, le azioni, i protocolli e la sequenza di passaggi da completare.

### <a name="identity-experience-framework"></a>Framework dell'esperienza di gestione delle identità

Una piattaforma Azure completamente configurabile, basato sui criteri e su cloud che orchestra le relazioni di trust tra le entità, in modo più ampio i provider di attestazioni, in formati di protocollo standard, ad esempio OpenIDConnect, OAuth, SAML, standard WSFed e alcuni formati non standard, ad esempio, scambi di attestazioni basati su API REST da sistema a sistema. I2E crea esperienze intuitive, generiche che supportano HTML, CSS e jscript.  Oggi, il Framework di esperienza di identità è disponibile solo nel contesto del servizio Azure AD B2C e dà priorità alle attività correlate al CIAM.

### <a name="built-in-policies"></a>Criteri predefiniti

I file di configurazione predefiniti che indirizzano il comportamento di Azure AD B2C affinché esegua le attività di identità più usate, ovvero registrazione dell'utente, accesso, reimpostazione della password, e interagisca con parti attendibili la cui relazione è predefinita anche in Azure AD B2C, ad esempio nei provider di identità di Facebook, in LinkedIn, in Microsoft Account, negli account di Google.  In futuro, i criteri predefiniti possono anche garantire la personalizzazione dei provider di identità che sono in genere nell'area aziendale, ad esempio Azure Active Directory Premium, Active Directory o ADFS, Salesforce ID Provider e così via.


### <a name="custom-policies"></a>Criteri personalizzati

I file di configurazione definiscono il comportamento del Framework di esperienza di identità nel tenant di Azure AD B2C. Un criterio personalizzato è accessibile come uno o più file XML, vedere le definizioni di file di criteri, che vengono eseguiti dal Framework di esperienza di identità quando viene richiamati da una relying party, ad esempio da un'applicazione. I criteri personalizzati possono essere modificati direttamente da uno sviluppatore di identità per il completamento di un numero quasi illimitato di attività. Gli sviluppatori che configurano i criteri personalizzati devono definire le relazioni attendibili dettagliatamente per includere gli endpoint dei metadati, le definizioni di scambio per le attestazioni esatte, e configurare i segreti, le chiavi e i certificati necessari per ogni provider di identità.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Definizione di file di criteri per i framework di attendibilità per il Framework di esperienza di identità

### <a name="policy-files"></a>File dei criteri

Un criterio personalizzato è rappresentato come uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. Gli elementi XML definiscono: schema di attestazioni, trasformazioni di attestazioni, definizioni di contenuto, profili tecnico o provider di attestazioni e procedure di orchestrazione Userjourney, tra gli altri elementi.  Si consiglia di usare i tre tipi di file dei criteri:

- **Un file di BASE**, che contiene la maggior parte delle definizioni e per cui Azure offre un esempio completo.  Si consiglia di apportare un numero minimo di modifiche a questo file che contribuisce alla risoluzione dei problemi e alla manutenzione a lungo termine dei criteri
- **Un file di estensioni** che contiene le modifiche di configurazione univoche per il tenant
- **Un file Relying Party (RP)**, l'unico file incentrato sulle attività che viene richiamato direttamente dell'applicazione o dal servizio ed è noto anche come Relying Party.  Per altre informazioni leggere l'articolo sulle definizioni di file dei criteri.  Ogni attività univoca richiede un proprio Relying Party e, a seconda dei requisiti di personalizzazione, il numero potrebbe essere il "totale delle applicazioni per il numero totale di casi di uso".

![Tipi di file di criteri](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Tipo di file di criteri | Nome del file di esempio | Uso consigliato | Eredito da |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Include lo schema di attestazioni core, le trasformazioni delle attestazioni, i provider delle attestazioni e i percorsi utente è configurati da Microsoft<br><br>Apportare modifiche minime al file | Nessuno |
| Estensione (RXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Consolidare le modifiche al file di BASE qui<br><br>Provider di attestazioni modificati<br><br>Percorsi utente modificati<br><br>Definizioni dello schema personalizzato | File di BASE |
| Relying Party (RP) | | | File di estensioni |

### <a name="inheritance-model"></a>Modello di ereditarietà

Quando un'applicazione chiama il file dei criteri di relying party, il Framework di esperienza di identità in B2C aggiungerà tutti gli elementi dal file di criteri di BASE, quindi da quello delle estensioni e infine dal relying party per assemblare i criteri correnti in vigore.  Gli elementi dello stesso tipo e nome nel file relying party eseguiranno l'override nel file ESTENSIONI, ed ESTENSIONI esegue l'override nel file di BASE.

I **criteri predefiniti** in Azure AD B2C seguono il modello a 3 file illustrato in precedenza, ma lo sviluppatore vede solo il file Relying Party (RP), mentre il portale apporta modifiche in background al file delle estensioni.  Tutti i componenti di Azure AD B2C condividono un file di criteri di BASE che è controllaro del team di Azure B2C e viene aggiornato di frequente.

