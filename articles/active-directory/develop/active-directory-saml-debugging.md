---
title: Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory | Microsoft Docs
description: 'Informazioni su come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory  '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory

Durante il debug di un'integrazione di un'applicazione basata su SAML, è spesso utile usare uno strumento come [Fiddler](http://www.telerik.com/fiddler) per visualizzare la richiesta SAML e la risposta SAML contenente il token SAML rilasciato per un'applicazione. 

![Fiddler][1]

Spesso i problemi sono correlati a un errore di configurazione in Azure Active Directory o sul lato dell'applicazione. A seconda di dove viene visualizzato l'errore, è necessario esaminare la richiesta o la risposta SAML:

- Viene visualizzato un errore nella pagina di accesso dell'azienda [Collegamenti alla sezione]
- Viene visualizzato un errore nella pagina dell'applicazione dopo l'accesso [Collegamenti alla sezione]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>Viene visualizzato un errore nella pagina di accesso dell'azienda.

È possibile trovare informazioni sui passaggi per la risoluzione in base al codice di errore in [Problemi di accesso a un'applicazione della raccolta configurata per il Single Sign-On federato](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Se viene visualizzato un errore nella pagina di accesso dell'azienda, per il debug del problema sono necessari il messaggio di errore e la richiesta SAML.

### <a name="how-can-i-get-the-error--message"></a>Come è possibile ottenere il messaggio di errore?

Per ottenere il messaggio di errore, esaminare l'angolo inferiore destro della pagina. Viene visualizzato un errore che include:

- ID di correlazione
- Timestamp
- Messaggio

![Tipi di errore][2] 

 
L'ID di correlazione e il timestamp formano un identificatore univoco che è possibile usare per trovare i log back-end associati all'errore di accesso. Questi valori sono importanti quando si crea un caso di supporto con Microsoft, perché aiutano i tecnici a fornire una risoluzione più veloce per il problema.

Il messaggio indica la causa radice del problema di accesso. 


### <a name="how-can-i-review-the-saml-request"></a>Come è possibile esaminare la richiesta SAML?

La richiesta SAML inviata dall'applicazione ad Azure Active Directory è in genere l'ultima risposta HTTP 200 da [https://login.microsoftonline.com](https://login.microsoftonline.com).
 
Usando Fiddler, è possibile visualizzare la richiesta SAML selezionando la riga e quindi la scheda **Inspectors > WebForms** (Controlli > Web Form) nel riquadro a destra. Fare clic con il pulsante destro del mouse sul valore di **SAMLRequest** e quindi scegliere **Send to TextWizard** (Invia a TextWizard). Quindi selezionare **Da Base64** nel menu **Trasforma** per decodificare il token e visualizzarne il contenuto. 

È anche possibile copiare il valore di SAMLrequest e usare un altro decodificatore Base64.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

Dopo aver decodificato la richiesta SAML, esaminare quanto segue:

1. **Destination** nella richiesta SAML corrisponde all'URL del servizio Single Sign-On SAML ottenuto da Azure Active Directory.
 
2. **Issuer** nella richiesta SAML corrisponde all'**Identificatore** configurato per l'applicazione in Azure Active Directory. Azure AD usa il valore di Issuer per trovare un'applicazione nella directory.

3. **AssertionConsumerServiceURL** indica la posizione in cui l'applicazione si aspetta di ricevere il token SAML da Azure Active Directory. È possibile configurare questo valore in Azure Active Directory, ma non è obbligatorio se fa parte della richiesta SAML.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>Viene visualizzato un errore nella pagina dell'applicazione dopo l'accesso

In questo scenario l'applicazione non accetta la risposta di Azure AD. È importante verificare la risposta da Azure AD che contiene il token SAML con il fornitore dell'applicazione per capire cosa manca o è sbagliato. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>Come è possibile ottenere ed esaminare la risposta SAML?

La risposta di Azure AD che contiene il token SAML è in genere quella che si verifica dopo un reindirizzamento HTTP 302 da https://login.microsoftonline.com e viene inviata all'**URL di risposta** configurato dell'applicazione. 

È possibile visualizzare il token SAML selezionando la riga e quindi la scheda **Ispettori > WebForms** nel riquadro a destra. A questo punto fare clic con il pulsante destro sul valore **SAMLResponse** e selezionare **Invia a TextWizard**. Scegliere quindi **From Base64** (Da Base64) dal menu **Trasforma** per decodificare il token e visualizzarne il contenuto in un altro decodificatore Base64. 

È anche possibile copiare il valore di **SAMLrequest** e usare un altro decodificatore Base64.

Vedere le indicazioni per la risoluzione dei problemi in [Errore nella pagina dell'applicazione dopo l'accesso](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) per altre informazioni su ciò che può mancare o essere sbagliato nella risposta SAML.

Per informazioni su come esaminare la risposta SAML, vedere l'articolo [Protocollo SAML per Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](../active-directory-apps-index.md)
* [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Personalizzazione delle attestazioni rilasciate nel token SAML per le app preintegrate](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
