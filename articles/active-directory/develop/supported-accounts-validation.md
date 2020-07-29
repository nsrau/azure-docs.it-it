---
title: Differenze di convalida per tipi di account supportati-piattaforma di identità Microsoft | Azure
description: Informazioni sulle differenze di convalida di diverse proprietà per diversi tipi di account supportati quando si registra l'app con la piattaforma di identità Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: e794e277f6731c7b6e57a4710eea437f65be0340
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87336345"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Differenze di convalida in base ai tipi di conto supportati (signInAudience)

Quando si registra un'applicazione con la piattaforma di identità Microsoft per gli sviluppatori, viene richiesto di selezionare i tipi di account supportati dall'applicazione. Nell'oggetto e nel manifesto dell'applicazione questa proprietà è `signInAudience` .

Di seguito sono indicate alcune opzioni disponibili:

- *AzureADMyOrg*: solo gli account nella directory aziendale in cui è registrata l'app (tenant singolo)
- *AzureADMultipleOrgs*: account in qualsiasi directory organizzativa (multi-tenant)
- *AzureADandPersonalMicrosoftAccount*: account in qualsiasi directory organizzativa (multi-tenant) e account Microsoft personali (ad esempio, Skype, Xbox e Outlook.com)

Per le applicazioni registrate, è possibile trovare il valore per i tipi di account supportati nella sezione **autenticazione** di un'applicazione. È anche possibile trovarlo sotto la `signInAudience` proprietà nel **manifesto**.

Il valore selezionato per questa proprietà presenta implicazioni sulle altre proprietà dell'oggetto app. Di conseguenza, se si modifica questa proprietà, potrebbe essere necessario modificare prima le altre proprietà.

Per le differenze di convalida di diverse proprietà per diversi tipi di account supportati, vedere la tabella seguente.

| Proprietà | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` e `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| URI ID applicazione ( `identifierURIs` )  | Deve essere univoco nel tenant <br><br> sono supportati gli schemi urn:// <br><br> I caratteri jolly non sono supportati <br><br> Sono supportate le stringhe di query e i frammenti <br><br> Lunghezza massima di 255 caratteri <br><br> Nessun limite * sul numero di identifierURIs  | Deve essere globalmente univoco <br><br> sono supportati gli schemi urn:// <br><br> I caratteri jolly non sono supportati <br><br> Sono supportate le stringhe di query e i frammenti <br><br> Lunghezza massima di 255 caratteri <br><br> Nessun limite * sul numero di identifierURIs | Deve essere globalmente univoco <br><br> gli schemi urn://non sono supportati <br><br> I caratteri jolly, i frammenti e le stringhe di query non sono supportati <br><br> Lunghezza massima di 120 caratteri <br><br> Massimo 50 identifierURIs |
| Certificati ( `keyCredentials` ) | Chiave di firma simmetrica | Chiave di firma simmetrica | Crittografia e chiave di firma asimmetrica | 
| Segreti client ( `passwordCredentials` ) | Nessun limite * | Nessun limite * | Se liveSDK è abilitato: massimo 2 segreti client | 
| URI di reindirizzamento ( `replyURLs` ) | Per altre informazioni, vedere [restrizioni e limitazioni dell'URL di risposta/URI di reindirizzamento](reply-url.md) . | | | 
| Autorizzazioni API ( `requiredResourceAccess` ) | Nessun limite * | Nessun limite * | Massimo 50 di risorse per applicazione e 30 autorizzazioni per risorsa, ad esempio Microsoft Graph. Limite totale di 200 per applicazione (autorizzazioni x risorse). | 
| Ambiti definiti da questa API ( `oauth2Permissions` ) | Lunghezza massima del nome dell'ambito di 120 caratteri <br><br> Nessun limite * sul numero di ambiti definito | Lunghezza massima del nome dell'ambito di 120 caratteri <br><br> Nessun limite * sul numero di ambiti definito |  Lunghezza massima del nome dell'ambito di 40 caratteri <br><br> Massimo 100 ambiti definiti | 
| Applicazioni client autorizzate ( `preAuthorizedApplications` ) | Nessun limite * | Nessun limite * | Massimo totale di 500 <br><br> Massimo 100 di app client definite <br><br> Massimo 30 ambiti definiti per client | 
| appRoles | Supportato <br> Nessun limite * | Supportato <br> Nessun limite * | Non supportato | 
| URL di disconnessione | http://localhostconsentito <br><br> Lunghezza massima di 255 caratteri | http://localhostconsentito <br><br> Lunghezza massima di 255 caratteri | <br><br> https://localhostè consentito, http://localhost non riesce per MSA <br><br> Lunghezza massima di 255 caratteri <br><br> Lo schema HTTP non è consentito <br><br> I caratteri jolly non sono supportati | 

* Esiste un limite globale di circa 1000 elementi in tutte le proprietà della raccolta nell'oggetto app

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [registrazione dell'applicazione](app-objects-and-service-principals.md)
- Informazioni sul [Manifesto dell'applicazione](reference-app-manifest.md)
