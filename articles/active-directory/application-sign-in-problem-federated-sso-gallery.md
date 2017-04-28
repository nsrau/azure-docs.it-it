---
title: Problemi di accesso a un&quot;applicazione della raccolta configurata per il Single Sign-On federato | Microsoft Docs
description: Linee guida per errori durante l&quot;accesso a un&quot;applicazione configurata per il Single Sign-On federato basato su SAML con Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: b6d998575a3bf21ada200cfedeed61fe67e3b9d7
ms.lasthandoff: 04/17/2017


---

# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemi di accesso a un'applicazione della raccolta configurata per il Single Sign-On federato

Per risolvere il problema, è necessario verificare la configurazione dell'applicazione in Azure AD come segue:

-   Assicurarsi di aver seguito tutti i passaggi di configurazione per l'applicazione nella raccolta di AD Azure.

-   Controllare che l'identificatore e l'URL di risposta configurati in AAD corrispondano ai valori previsti nell'applicazione

-   Verificare che siano stati assegnati utenti all'applicazione

## <a name="application-not-found-in-directory"></a>Applicazione non trovata nella directory

*Error: Application with Identifier ‘https://contoso.com’ was not found in the directory (Errore: applicazione con identificatore "https://contoso.com" non trovata nella directory)*.

**Causa possibile**

L'attributo Issuer inviato dall'applicazione ad Azure AD nella richiesta SAML non corrisponde al valore di identificatore configurato nell'applicazione di Azure AD.

**Risoluzione**

Assicurarsi che l'attributo Issuer nella richiesta SAML corrisponda al valore di identificatore configurato in Azure AD:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory"** nella casella di ricerca del filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare un accesso Single Sign-On

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Passare alla sezione **URL e dominio**. Verificare che il valore nella casella di testo dell'identificatore corrisponda al valore dell'identificatore visualizzato nel messaggio di errore.

Dopo aver aggiornato il valore dell'identificatore in Azure AD in modo che corrisponda al valore inviato dall'applicazione nella richiesta SAML, dovrebbe essere possibile accedere all'applicazione.

## <a name="user-not-assigned-a-role"></a>All'utente non è stato assegnato un ruolo

*Error: The signed in user 'brian@contoso.com' is not assigned to a role for the application (Errore: nessun ruolo assegnato all'utente che ha eseguito l'accesso "brian@contoso.com" per l'applicazione)*

**Causa possibile**

L'utente non ha ottenuto l'accesso all'applicazione in Azure AD.

**Risoluzione**

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory"** nella casella di ricerca del filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione che si desidera assegnare a un utente dall'elenco.

7.  Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il pannello **Aggiungi assegnazione**.

9.  Fare clic sul selettore **Utenti e gruppi** nel pannello **Aggiungi assegnazione**.

10. Digitare il **nome completo**  o l'**indirizzo di posta elettronica** dell'utente a cui si vuole eseguire l'assegnazione nella casella di ricerca **Cerca per nome o indirizzo di posta**.

11. Passare il puntatore sull'**utente** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si desidera **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **Selezionato**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel pannello **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati potranno avviare queste applicazioni usando i metodi illustrati nella sezione Descrizione della soluzione.

## <a name="not-a-valid-saml-request"></a>La richiesta non è un messaggio SAML valido

*Error: The request is not a valid Saml2 protocol message. (Errore: la richiesta non è un messaggio del protocollo Saml2 valido.)*

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

*Error: The client application has requested access to resource '00000002-0000-0000-c000-000000000000'. This request has failed because the client has not specified this resource in its requiredResourceAccess list. (Errore: l'applicazione client ha richiesto l'accesso alla risorsa '00000002-0000-0000-c000-000000000000'. La richiesta ha avuto esito negativo perché il client non ha specificato questa risorsa nell'elenco requiredResourceAccess)*.

**Causa possibile**

L'oggetto applicazione è danneggiato.

**Risoluzione**

Per risolvere il problema, rimuovere l'applicazione dalla directory e quindi aggiungerla e riconfigurarla seguendo questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory"** nella casella di ricerca del filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare un accesso Single Sign-On

7.  Fare clic su **Elimina** in alto a sinistra del pannello **Panoramica** dell'applicazione.

8.  Aggiornare Azure AD, aggiungere l'applicazione dalla raccolta di Azure AD e quindi configurarla.

<span id="_Hlk477190176" class="anchor"></span>Dopo la riconfigurazione dell'applicazione, dovrebbe essere possibile accedere all'applicazione.

## <a name="certificate-or-key-not-configured"></a>Chiave o certificato non configurato

*Error: No signing key configured. (Errore: nessuna chiave di firma configurata.)*

**Causa possibile**

L'oggetto applicazione è danneggiato e Azure AD non riconosce il certificato configurato per l'applicazione.

**Risoluzione**

Per eliminare e creare un nuovo certificato, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory"** nella casella di ricerca del filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

 * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare un accesso Single Sign-On

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

9.  Selezionare la data di scadenza. Fare quindi clic su **Salva**.

10. Selezionare l'opzione per **attivare il nuovo certificato** in modo da sostituire il certificato attivo. Fare quindi clic su **Salva** nella parte superiore del pannello e accettare di attivare il certificato di rollover.

11. Nella sezione **Certificato di firma SAML** selezionare **Rimuovi** per rimuovere il certificato **Inutilizzato**.

## <a name="next-steps"></a>Passaggi successivi
[Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

