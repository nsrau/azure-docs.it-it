---
title: Problemi di accesso a un'applicazione non nella raccolta configurata per il Single Sign-On federato | Microsoft Docs
description: Linee guida per i problemi specifici che possono verificarsi durante l'accesso a un'applicazione configurata per il Single Sign-On federato basato su SAML con Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cfd4e478850783983caa3042cbbb2b096a576292
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemi di accesso a un'applicazione non nella raccolta configurata per il Single Sign-On federato

Per risolvere il problema, è necessario verificare la configurazione dell'applicazione in Azure AD come segue:

-   Assicurarsi di aver seguito tutti i passaggi di configurazione per l'applicazione nella raccolta di AD Azure.

-   Controllare che l'identificatore e l'URL di risposta configurati in AAD corrispondano ai valori previsti nell'applicazione

-   Verificare che siano stati assegnati utenti all'applicazione

## <a name="application-not-found-in-directory"></a>Applicazione non trovata nella directory

*Error AADSTS70001: Application with Identifier 'https://contoso.com' was not found in the directory* (Errore AADSTS70001: applicazione con identificatore 'https://contoso.com' non trovata nella directory).

**Causa possibile**

L'attributo Issuer inviato dall'applicazione ad Azure AD nella richiesta SAML non corrisponde al valore di identificatore configurato nell'applicazione di Azure AD.

**Risoluzione**

Assicurarsi che l'attributo Issuer nella richiesta SAML corrisponda al valore di identificatore configurato in Azure AD:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  <span id="_Hlk477190042" class="anchor"></span>Passare alla sezione **URL e dominio**. Verificare che il valore nella casella di testo dell'identificatore corrisponda al valore dell'identificatore visualizzato nel messaggio di errore.

Dopo aver aggiornato il valore dell'identificatore in Azure AD in modo che corrisponda al valore inviato dall'applicazione nella richiesta SAML, dovrebbe essere possibile accedere all'applicazione.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>L'indirizzo di risposta non corrisponde agli indirizzi di risposta configurati per l'applicazione. 

*Errore AADSTS50011: l'indirizzo di risposta 'https://contoso.com' non corrisponde agli indirizzi risposta configurati per l'applicazione* 

**Causa possibile** 

Il valore AssertionConsumerServiceURL nella richiesta SAML non corrisponde al valore o al modello dell'URL di risposta configurato in Azure AD. Il valore AssertionConsumerServiceURL nella richiesta SAML è l'URL visualizzato nel messaggio di errore. 

**Risoluzione** 

Assicurare che il valore AssertionConsumerServiceURL nella richiesta SAML corrisponda al valore dell'URL di risposta configurato in Azure AD. 
 
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**. 

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra. 

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**. 

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory. 

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni. 

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e      impostare l'opzione **Mostra** su **Tutte le applicazioni.**
  
6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  Passare alla sezione **URL e dominio**. Verificare o aggiornare il valore nella casella di testo URL di risposta in modo che corrisponda al valore AssertionConsumerServiceURL nella richiesta SAML.

  * Se non viene visualizzata la casella di testo URL di risposta, selezionare la casella **Mostra impostazioni URL avanzate**. 

Dopo aver aggiornato il valore dell'URL di risposta in Azure AD in modo che corrisponda al valore inviato dall'applicazione nella richiesta SAML, dovrebbe essere possibile accedere all'applicazione.

## <a name="user-not-assigned-a-role"></a>All'utente non è stato assegnato un ruolo

*Error AADSTS50105: The signed in user 'brian@contoso.com' is not assigned to a role for the application (Errore AADSTS50105: nessun ruolo assegnato all'utente che ha eseguito l'accesso "brian@contoso.com" per l'applicazione)*

**Causa possibile**

L'utente non ha ottenuto l'accesso all'applicazione in Azure AD.

**Risoluzione**

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7.  Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di spostamento di sinistra dell'applicazione.

8.  Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione**.

9.  Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo**  o l'**indirizzo di posta elettronica** dell'utente oggetto dell'assegnazione.

11. Passare il puntatore sull'**utente** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **selezionato**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati potranno avviare queste applicazioni usando i metodi illustrati nella sezione Descrizione della soluzione.

## <a name="not-a-valid-saml-request"></a>La richiesta non è un messaggio SAML valido

*Error AADSTS75005: The request is not a valid Saml2 protocol message.*(Errore AADSTS75005: la richiesta non è un messaggio del protocollo Saml2 valido).

**Causa possibile**

Azure AD non supporta la richiesta di SAML inviata dall'applicazione per il Single Sign-On. Alcuni problemi comuni sono:

-   Campi obbligatori mancanti nella richiesta SAML

-   Metodo codificato della richiesta SAML

**Risoluzione**

1.  Acquisire la richiesta SAML. Per informazioni su come acquisire la richiesta SAML, seguire l'esercitazione [Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

2.  Contattare il fornitore dell'applicazione e condividere:

    -   Richiesta SAML

    -   [Requisiti del protocollo SAML per Single Sign-On di Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Il fornitore dovrebbe confermare di supportare l'implementazione del protocollo SAML di Azure AD SAML per il Single Sign-on.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Nessuna risorsa nell'elenco requiredResourceAccess

*Error AADSTS65005: The client application has requested access to resource '00000002-0000-0000-c000-000000000000'. This request has failed because the client has not specified this resource in its requiredResourceAccess list. (Errore: l'applicazione client ha richiesto l'accesso alla risorsa '00000002-0000-0000-c000-000000000000'. La richiesta ha avuto esito negativo perché il client non ha specificato questa risorsa nell'elenco requiredResourceAccess)*.

**Causa possibile**

L'oggetto applicazione è danneggiato.

**Risoluzione**

Per risolvere il problema, rimuovere l'applicazione dalla directory e quindi aggiungerla e riconfigurarla seguendo questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Fare clic su **Elimina** in alto a sinistra del riquadro **Panoramica** dell'applicazione.

8.  Aggiornare Azure AD, aggiungere l'applicazione dalla raccolta di Azure AD e quindi configurarla di nuovo.

Dopo la riconfigurazione dell'applicazione, dovrebbe essere possibile accedere all'applicazione.

## <a name="certificate-or-key-not-configured"></a>Chiave o certificato non configurato

Error AADSTS50003: No signing key configured (Errore AADSTS50003: nessuna chiave di firma configurata).

**Causa possibile**

L'oggetto applicazione è danneggiato e Azure AD non riconosce il certificato configurato per l'applicazione.

**Risoluzione**

Per eliminare e creare un nuovo certificato, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

9.  Selezionare la data di scadenza. Fare quindi clic su **Salva**.

10. Selezionare l'opzione per **attivare il nuovo certificato** in modo da sostituire il certificato attivo. Fare quindi clic su **Salva** nella parte superiore del riquadro e accettare di attivare il certificato di rollover.

11. Nella sezione **Certificato di firma SAML** selezionare **Rimuovi** per rimuovere il certificato **Inutilizzato**.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema di personalizzazione delle attestazioni SAML inviate a un'applicazione

Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [Claims mapping in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) (Mapping di attestazioni in Azure Active Directory).

## <a name="next-steps"></a>Passaggi successivi
[Requisiti del protocollo SAML per Single Sign-On di Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
