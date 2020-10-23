---
title: Problemi di accesso alle app configurate Single Sign-On basate su SAML
description: Linee guida per gli errori specifici quando si esegue l'accesso a un'applicazione configurata per il Single Sign-On federato basato su SAML con Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperfq2
ms.openlocfilehash: ec39a6d106973808e26b7c06dce8b3054af490ff
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427379"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problemi di accesso alle app configurate Single Sign-On basate su SAML
Per risolvere i problemi di accesso indicati di seguito, è consigliabile eseguire una diagnosi migliore e automatizzare i passaggi di risoluzione:

- Installare l' [estensione My App Secure Browser](access-panel-extension-problem-installing.md) per semplificare la Azure Active Directory (Azure ad) per fornire una diagnosi e una risoluzione migliori quando si usa l'esperienza di test nel portale di Azure.
- Riprodurre l'errore usando l'esperienza di test nella pagina di configurazione dell'app nel portale di Azure. Altre informazioni sul [debug di applicazioni Single Sign-on basate su SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

Se si usa l' [esperienza di test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nella portale di Azure con l'estensione My App Secure browser, non è necessario seguire manualmente la procedura riportata di seguito per aprire la pagina di configurazione Single Sign-on basata su SAML.

Per aprire la pagina di configurazione Single Sign-On basata su SAML:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore**.
1.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
1.  Digitare **"Azure Active Directory"** nella casella di ricerca del filtro e selezionare l'elemento di **Azure Active Directory** .
1.  Selezionare **applicazioni aziendali** nel menu di spostamento a sinistra Azure Active Directory.
1.  Selezionare **tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.
    Se non viene visualizzata l'applicazione che si vuole visualizzare qui, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni**.
1.  Selezionare l'applicazione che si vuole configurare con l'accesso Single Sign-On.
1. Dopo il caricamento dell'applicazione, selezionare **Single Sign-on** nel menu di navigazione a sinistra dell'applicazione.
1. Selezionare SSO basato su SAML.

## <a name="application-not-found-in-directory"></a>Applicazione non trovata nella directory
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Possibile causa**

L' `Issuer` attributo inviato dall'applicazione a Azure ad nella richiesta SAML non corrisponde al valore dell'identificatore configurato per l'applicazione in Azure ad.

**Risoluzione**

Verificare che l' `Issuer` attributo nella richiesta SAML corrisponda al valore dell'identificatore configurato in Azure ad.

Nella pagina configurazione SSO basata su SAML, nella sezione **configurazione SAML di base** , verificare che il valore nella casella di testo identificatore corrisponda al valore per il valore dell'identificatore visualizzato nell'errore.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>L'indirizzo di risposta non corrisponde agli indirizzi di risposta configurati per l'applicazione
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Possibile causa**

Il `AssertionConsumerServiceURL` valore nella richiesta SAML non corrisponde al valore o al modello dell'URL di risposta configurato in Azure ad. Il `AssertionConsumerServiceURL` valore nella richiesta SAML è l'URL visualizzato nell'errore.

**Risoluzione**

Verificare che il `AssertionConsumerServiceURL` valore nella richiesta SAML corrisponda al valore dell'URL di risposta configurato in Azure ad. 

Verificare o aggiornare il valore nella casella di testo URL di risposta in modo che corrisponda al `AssertionConsumerServiceURL` valore nella richiesta SAML.   
    
Dopo aver aggiornato il valore dell'URL di risposta in Azure AD e aver individuato il valore inviato dall'applicazione nella richiesta SAML, dovrebbe essere possibile accedere all'applicazione.

## <a name="user-not-assigned-a-role"></a>All'utente non è stato assegnato un ruolo
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Possibile causa**

L'utente non ha ottenuto l'accesso all'applicazione in Azure AD.

**Risoluzione**

Per assegnare uno o più utenti direttamente a un'applicazione, vedere [Guida introduttiva: assegnare utenti a un'app](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>Richiesta SAML non valida
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Possibile causa**

Azure AD non supporta la richiesta SAML inviata dall'applicazione per il Single Sign-On. Alcuni problemi comuni sono:
- Campi obbligatori mancanti nella richiesta SAML
- Metodo codificato della richiesta SAML

**Risoluzione**

1. Acquisire la richiesta SAML. Per informazioni su come acquisire la richiesta SAML, seguire l'esercitazione [come eseguire il debug di Single Sign-on basate su SAML per le applicazioni di Azure ad](../azuread-dev/howto-v1-debug-saml-sso-issues.md) .
1. Contattare il fornitore dell'applicazione e condividere i dati seguenti:
    - Richiesta SAML
    - [Requisiti del protocollo SAML per Single Sign-On di Azure](../develop/single-sign-on-saml-protocol.md)

Il fornitore dell'applicazione deve verificare che supportino l'implementazione SAML Azure AD per Single Sign-On.

## <a name="misconfigured-application"></a>Applicazione non configurata correttamente
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Possibile causa**

L' `Issuer` attributo inviato dall'applicazione a Azure ad nella richiesta SAML non corrisponde al valore dell'identificatore configurato per l'applicazione in Azure ad.

**Risoluzione**

Verificare che l' `Issuer` attributo nella richiesta SAML corrisponda al valore dell'identificatore configurato in Azure ad. 

Verificare che il valore nella casella di testo identificatore corrisponda al valore per il valore dell'identificatore visualizzato nell'errore.

## <a name="certificate-or-key-not-configured"></a>Chiave o certificato non configurato
`Error AADSTS50003: No signing key configured.`

**Possibile causa**

L'oggetto applicazione è danneggiato e Azure AD non riconosce il certificato configurato per l'applicazione.

**Risoluzione**

Per eliminare e creare un nuovo certificato, seguire questa procedura:
1. Nella schermata Configurazione di SSO basato su SAML selezionare **Crea nuovo certificato** nella sezione **certificato di firma SAML** .
1. Selezionare data di scadenza e quindi fare clic su **Salva**.
1. Selezionare **Rendi attivo il nuovo certificato** per sostituire il certificato attivo. Fare quindi clic su **Salva** nella parte superiore del riquadro e accettare di attivare il certificato di rollover.
1. Nella sezione **Certificato di firma SAML** selezionare **Rimuovi** per rimuovere il certificato **Inutilizzato**.

## <a name="saml-request-not-present-in-the-request"></a>Richiesta SAML non presente nella richiesta
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Possibile causa**

Azure AD non è riuscito a identificare la richiesta SAML entro i parametri dell'URL nella richiesta HTTP. Questo problema può verificarsi se l'applicazione non usa l'associazione di reindirizzamento HTTP quando Invia la richiesta SAML a Azure AD.

**Risoluzione**

L'applicazione deve inviare la richiesta SAML codificata nell'intestazione Location usando il binding di reindirizzamento HTTP. Per altre informazioni sull'implementazione di questo approccio, leggere la sezione sul binding Reindirizzamento HTTP nel [documento di specifiche del protocollo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD sta inviando il token a un endpoint errato
**Possibile causa**

Durante Single Sign-On, se la richiesta di accesso non contiene un URL di risposta esplicito (URL del servizio consumer di asserzione), Azure AD selezionerà uno degli URL di risposta configurati per l'applicazione. Anche se l'applicazione dispone di un URL di risposta esplicito configurato, l'utente potrebbe essere reindirizzato https://127.0.0.1:444 . 

Quando l'applicazione è stata aggiunta come un'app non inclusa nella raccolta, Azure Active Directory ha creato questo URL di risposta come valore predefinito. Questo comportamento è cambiato e Azure Active Directory non aggiunge più l'URL per impostazione predefinita. 

**Risoluzione**

Eliminare gli URL di risposta non utilizzati configurati per l'applicazione.

Nella pagina configurazione SSO basato su SAML, nella sezione **URL di risposta (URL del servizio consumer di asserzione)** , eliminare gli URL di risposta non utilizzati o predefiniti creati dal sistema. Ad esempio `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema di personalizzazione delle attestazioni SAML inviate a un'applicazione
Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [mapping delle attestazioni in Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="errors-related-to-misconfigured-apps"></a>Errori correlati a app non configurate correttamente
Verificare che le configurazioni nel portale corrispondano a quelle nell'app. In particolare, confrontare ID applicazione/client, URL di risposta, chiavi/segreti client e URI ID app.

Confrontare la risorsa a cui si richiede l'accesso nel codice con le autorizzazioni configurate nella scheda **Risorse necessarie** per assicurarsi di richiedere solo le risorse configurate.

## <a name="next-steps"></a>Passaggi successivi
- [Serie di guide di avvio rapido sulla gestione delle applicazioni](add-application-portal-assign-users.md)
- [Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
- [Requisiti del protocollo SAML per Single Sign-On di Azure](../develop/active-directory-single-sign-on-protocol-reference.md)
