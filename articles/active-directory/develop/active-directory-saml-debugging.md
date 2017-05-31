---
title: Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory | Microsoft Docs
description: 'Informazioni su come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory  '
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: asmalser
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: 245c2de65ca771cdc0e6371010246725c407ae11
ms.contentlocale: it-it
ms.lasthandoff: 01/18/2017


---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory
Durante il debug di un'integrazione di applicazioni basate su SAML, è spesso utile utilizzare uno strumento come [Fiddler](http://www.telerik.com/fiddler) per visualizzare la richiesta SAML, la risposta SAML e il token SAML reale emesso per l'applicazione. Esaminando il token SAML, è possibile garantire che tutti gli attributi richiesti, il nome utente nell’oggetto SAML e l'URI dell'autorità emittente vengano ricevuti come previsto.

![][1]

La risposta di Azure AD che contiene il token SAML è in genere quella che si verifica dopo un reindirizzamento HTTP 302 da https://login.windows.net e viene inviata all'**URL di risposta** configurato per l'applicazione. 

È possibile visualizzare il token SAML selezionando la riga e quindi la scheda **Ispettori > WebForms** nel riquadro a destra. A questo punto fare clic con il pulsante destro sul valore **SAMLResponse** e selezionare **Invia a TextWizard**. Quindi selezionare **Da Base64** nel menu **Trasforma** per decodificare il token e visualizzarne il contenuto.

**Nota**: per visualizzare il contenuto di questa richiesta HTTP, Fiddler potrebbe richiedere di configurare la decrittografia del traffico HTTPS; è necessario eseguire questa operazione.

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](../active-directory-apps-index.md)
* [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](../active-directory-saas-custom-apps.md)
* [Come personalizzare lle attestazioni rilasciate nel token SAML per le app preintegrate](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
