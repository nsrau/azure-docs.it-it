---
title: Differenze di convalida in base ai tipi di account supportati (SignInAudience)-piattaforma di identità Microsoft/Azure Active Directory
description: Informazioni sulle differenze di convalida di diverse proprietà per diversi tipi di account supportati quando si registra l'app con la piattaforma di identità Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bea6e3ea93e8b630970a8d86fc246eddaf3f56c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392605"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Differenze di convalida in base ai tipi di conto supportati (signInAudience)

Quando si registra un'applicazione con la piattaforma di identità Microsoft per gli sviluppatori, viene richiesto di selezionare i tipi di account supportati dall'applicazione. Nell'oggetto e nel manifesto dell'applicazione questa proprietà è `signInAudience`.

Di seguito sono indicate alcune opzioni disponibili:

- *AzureADMyOrg*: solo gli account nella directory aziendale in cui è registrata l'app (tenant singolo)
- *AzureADMultipleOrgs*: account in qualsiasi directory organizzativa (multi-tenant)
- *AzureADandPersonalMicrosoftAccount*: account in qualsiasi directory organizzativa (multi-tenant) e account Microsoft personali (ad esempio, Skype, Xbox e Outlook.com)

Per le applicazioni registrate, è possibile trovare il valore per i tipi di account supportati nella sezione **autenticazione** di un'applicazione. È anche possibile trovarlo sotto la proprietà `signInAudience` nel **manifesto**.

Il valore selezionato per questa proprietà presenta implicazioni sulle altre proprietà dell'oggetto app. Di conseguenza, se si modifica questa proprietà, potrebbe essere necessario modificare prima le altre proprietà.

Per le differenze di convalida di diverse proprietà per diversi tipi di account supportati, vedere la tabella seguente.

| Proprietà | `AzureADMyOrg` | `AzureADMultipleOrgs`  | `AzureADandPersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| URI ID applicazione (`identifierURIs`)  | Deve essere univoco nel tenant <br><br> sono supportati gli schemi urn:// <br><br> I caratteri jolly non sono supportati <br><br> Sono supportate le stringhe di query e i frammenti <br><br> Lunghezza massima di 255 caratteri <br><br> Nessun limite * sul numero di identifierURIs  | Deve essere globalmente univoco <br><br> sono supportati gli schemi urn:// <br><br> I caratteri jolly non sono supportati <br><br> Sono supportate le stringhe di query e i frammenti <br><br> Lunghezza massima di 255 caratteri <br><br> Nessun limite * sul numero di identifierURIs | Deve essere globalmente univoco <br><br> gli schemi urn://non sono supportati <br><br> I caratteri jolly, i frammenti e le stringhe di query non sono supportati <br><br> Lunghezza massima di 120 caratteri <br><br> Massimo 50 identifierURIs |
| Certificati (`keyCredentials`) | Chiave di firma simmetrica | Chiave di firma simmetrica | Crittografia e chiave di firma asimmetrica | 
| Segreti client (`passwordCredentials`) | Nessun limite * | Nessun limite * | Se liveSDK è abilitato: massimo 2 segreti client | 
| URI di reindirizzamento (`replyURLs`) | Per altre informazioni, vedere [restrizioni e limitazioni dell'URL di risposta/URI di reindirizzamento](reply-url.md) . | | | 
| Autorizzazioni per le API (`requiredResourceAccess`) | Nessun limite * | Nessun limite * | Sono consentite al massimo 30 autorizzazioni per risorsa (ad esempio Microsoft Graph) | 
| Ambiti definiti da questa API (`oauth2Permissions`) | Lunghezza massima del nome dell'ambito di 120 caratteri <br><br> Nessun limite * sul numero di ambiti definito | Lunghezza massima del nome dell'ambito di 120 caratteri <br><br> Nessun limite * sul numero di ambiti definito |  Lunghezza massima del nome dell'ambito di 40 caratteri <br><br> Massimo 100 ambiti definiti | 
| Applicazioni client autorizzate (`preautorizedApplications`) | Nessun limite * | Nessun limite * | Massimo totale di 500 <br><br> Massimo 100 di app client definite <br><br> Massimo 30 ambiti definiti per client | 
| appRoles | Supportato <br> Nessun limite * | Supportato <br> Nessun limite * | Supporto non disponibile | 
| URL di chiusura sessione | http://localhost è consentito <br><br> Lunghezza massima di 255 caratteri | http://localhost è consentito <br><br> Lunghezza massima di 255 caratteri | <br><br> https://localhost è consentito, http://localhost ha esito negativo per MSA <br><br> Lunghezza massima di 255 caratteri <br><br> Lo schema HTTP non è consentito <br><br> I caratteri jolly non sono supportati | 

\* Esiste un limite globale di circa 1000 elementi in tutte le proprietà della raccolta nell'oggetto app

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [registrazione dell'applicazione](app-objects-and-service-principals.md)
- Informazioni sul [manifesto dell'applicazione](reference-app-manifest.md)
