---
title: Differenze di convalida per tipi di account supportati - Piattaforma di identità Microsoft Azure
description: Scopri le differenze di convalida di varie proprietà per diversi tipi di account supportati quando registri l'app con la piattaforma di identità Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128865"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Differenze di convalida per tipi di account supportati (signInAudience)

Quando si registra un'applicazione con la piattaforma di identità Microsoft per gli sviluppatori, viene chiesto di selezionare i tipi di account supportati dall'applicazione. Nell'oggetto applicazione e nel `signInAudience`manifesto questa proprietà è .

Di seguito sono indicate alcune opzioni disponibili:

- *AzureADMyOrg:* solo gli account nella directory dell'organizzazione in cui è registrata l'app (tenant singolo)AzureADMyOrg : Only accounts in the organizational directory where the app is registered (single-tenant)
- *AzureADMultipleOrgs:* account in qualsiasi directory organizzativa (multi-tenant)AzureADMultipleOrgs : Accounts in any organizational directory (multi-tenant)
- *AzureADandPersonalMicrosoftAccount:* account in qualsiasi directory dell'organizzazione (multi-tenant) e account Microsoft personali (ad esempio, Skype, Xbox e Outlook.com)

Per le applicazioni registrate, è possibile trovare il valore per Tipi di account supportati nella sezione **Autenticazione** di un'applicazione. È inoltre possibile trovarlo sotto la `signInAudience` proprietà nel **manifesto**.

Il valore selezionato per questa proprietà ha implicazioni su altre proprietà dell'oggetto app. Di conseguenza, se si modifica questa proprietà, potrebbe essere necessario modificare prima altre proprietà.

Vedere la tabella seguente per le differenze di convalida di varie proprietà per i diversi tipi di account supportati.

| Proprietà | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` e `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| URI ID`identifierURIs`applicazione ( )  | Deve essere univoco nel tenant <br><br> urn:// sono supportati schemi di urn:// <br><br> I caratteri jolly non sono supportati <br><br> Le stringhe di query e i frammenti sono supportati <br><br> Lunghezza massima di 255 caratteri <br><br> Nessun limite al numero di identificatoriURI  | Deve essere globalmente univoco <br><br> urn:// sono supportati schemi di urn:// <br><br> I caratteri jolly non sono supportati <br><br> Le stringhe di query e i frammenti sono supportati <br><br> Lunghezza massima di 255 caratteri <br><br> Nessun limite al numero di identificatoriURI | Deve essere globalmente univoco <br><br> gli schemi di urn:// non sono supportati <br><br> I caratteri jolly, i frammenti e le stringhe di query non sono supportati <br><br> Lunghezza massima di 120 caratteri <br><br> Massimo 50 identificatoriURI |
| Certificati`keyCredentials`( ) | Chiave di firma simmetrica | Chiave di firma simmetrica | Crittografia e chiave di firma asimmetrica | 
| Segreti client`passwordCredentials`( ) | Nessun limite | Nessun limite | Se liveSDK è abilitato: massimo 2 segreti client | 
| URI di`replyURLs`reindirizzamento ( ) | Per altre info, vedi [Restrizioni e limitazioni dell'URI di reindirizzamento/URL](reply-url.md) di risposta. | | | 
| Autorizzazioni API`requiredResourceAccess`( ) | Nessun limite | Nessun limite | Massimo 30 autorizzazioni per risorsa consentite (ad esempio Microsoft Graph) | 
| Ambiti definiti da questa`oauth2Permissions`API ( ) | Lunghezza massima del nome di ambito di 120 caratteri <br><br> Nessun limite al numero di ambiti definiti | Lunghezza massima del nome di ambito di 120 caratteri <br><br> Nessun limite al numero di ambiti definiti |  Lunghezza massima del nome di ambito di 40 caratteri <br><br> Massimo 100 ambiti definiti | 
| Applicazioni client`preautorizedApplications`autorizzate ( ) | Nessun limite | Nessun limite | Massimo totale di 500 <br><br> Massimo 100 app client definite <br><br> Massimo 30 ambiti definiti per client | 
| appRoles | Supportato <br> Nessun limite | Supportato <br> Nessun limite | Non supportate | 
| URL di chiusura sessione | http://localhostè consentito <br><br> Lunghezza massima di 255 caratteri | http://localhostè consentito <br><br> Lunghezza massima di 255 caratteri | <br><br> https://localhostè consentito, http://localhost non riesce per MSA <br><br> Lunghezza massima di 255 caratteri <br><br> Lo schema HTTP non è consentito <br><br> I caratteri jolly non sono supportati | 

È previsto un limite globale di circa 1000 elementi in tutte le proprietà della raccolta nell'oggetto app

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulla [registrazione dell'applicazione](app-objects-and-service-principals.md)
- Informazioni sul [manifesto dell'applicazione](reference-app-manifest.md)
