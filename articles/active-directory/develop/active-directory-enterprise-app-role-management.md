---
title: Configurazione dell'attestazione basata su ruolo rilasciata nel token SAML per le applicazioni aziendali in Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare l'attestazione basata su ruolo rilasciata nel token SAML per le applicazioni aziendali in Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 88a9f5988d1fe3f4de4fe10da23a5f713e3f3370
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Configurazione dell'attestazione basata su ruolo rilasciata nel token SAML per le applicazioni aziendali in Azure Active Directory

Questa funzionalità consente agli utenti di personalizzare il tipo di attestazione per l'attestazione basata su ruoli nel token di risposta ricevuto al momento dell'autorizzazione di un'app tramite Azure AD.

## <a name="prerequisites"></a>prerequisiti
- Una sottoscrizione di Azure AD con la directory installata
- Una sottoscrizione abilitata per l'accesso Single Sign-On
- È necessario configurare l'accesso SSO con l'applicazione

## <a name="when-to-use-this-feature"></a>Quando usare questa funzionalità

Se l'applicazione prevede che vengano passati ruoli personalizzati nella risposta SAML, è necessario usare questa funzionalità. Questa funzionalità consente di creare tutti i ruoli che è necessario passare nuovamente da Azure AD all'applicazione.

## <a name="steps-to-use-this-feature"></a>Passaggi per l'uso di questa funzionalità

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare il nome dell'applicazione, selezionare l'applicazione nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Applicazione nell'elenco dei risultati](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Dopo avere aggiunto l'applicazione, andare alla pagina **Proprietà** e copiare l'**ID oggetto**.

    ![Pagina Proprietà](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Aprire [Graph Explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

    a. Accedere al sito di Graph Explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

    b. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Se si usano più directory, è consigliabile seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. Dall'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione in tutte le entità servizio elencate. Cercare l'**ID oggetto** che è stato copiato dalla pagina Proprietà e usare la query seguente per ottenere la rispettiva entità servizio.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Estrarre la proprietà appRoles dall'oggetto entità servizio.

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. È ora necessario generare nuovi ruoli per l'applicazione. È possibile scaricare Azure AD Role Generator da [qui](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Aprire Azure AD Role Generator e seguire questa procedura:

    ![Azure AD Role Generator](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Immettere i valori richiesti per **Role Name** (Nome del ruolo), **Role Description** (Descrizione del ruolo) e **Role Value** (Valore del ruolo). Fare clic su **Add** (Aggiungi) per aggiungere il ruolo.
    
    Dopo avere aggiunto tutti i ruoli richiesti, fare clic su **Generate** (Genera).
    
    Copiare il contenuto facendo clic su **Copy Content** (Copia contenuto).

    > [!NOTE] 
    > Assicurarsi di disporre del ruolo utente **msiam_access** e che l'ID sia corrispondente nel ruolo generato.

    g. Tornare a Graph Explorer. Modificare il metodo da **GET** a **PATCH**. Applicare la patch all'oggetto entità servizio per avere gli oggetti appRoles desiderati aggiornando la proprietà appRoles con i valori copiati. Fare clic su **Esegui query**.

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > Di seguito è riportato un esempio di oggetto appRoles. 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. Dopo che all'entità servizio è stata applicata la patch con più ruoli, è possibile assegnare gli utenti ai rispettivi ruoli. Questa operazione può essere eseguita accedendo al portale e passando alla rispettiva app. Fare quindi clic sulla scheda **Utenti e gruppi** nella parte superiore. Questo processo elencherà tutti gli utenti o i gruppi.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. Per assegnare un ruolo a un utente, è sufficiente selezionare l'utente o il gruppo specifico e fare clic sul pulsante **Assegna** nella parte inferiore della pagina.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. Facendo clic su questo pulsante, viene visualizzato un popup per la selezione di un ruolo tra i diversi ruoli definiti per la rispettiva entità servizio.

    c. Scegliere il ruolo richiesto e fare clic su Invia.

8. Dopo l'assegnazione dei ruoli agli utenti, è necessario aggiornare la tabella **Attributi** per definire il mapping personalizzato dell'attestazione basata su **ruolo**.

9. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | -------------- | ----------------|    
    | Nome ruolo      | user.assignedrole |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Configurare l'attributo Single Sign-On](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.

10. Per testare l'applicazione in modalità Single Sign-On avviata da IDP, accedere al Pannello di accesso (https://myapps.microsoft.com) e fare clic sul riquadro dell'applicazione. Nel token SAML dovrebbero essere visualizzati tutti i ruoli assegnati per l'utente con il nome dell'attestazione specificato.

## <a name="update-existing-role"></a>Aggiornare un ruolo esistente

1. Per aggiornare un ruolo esistente, seguire questa procedura:

    a. Aprire [Graph Explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

    b. Accedere al sito di Graph Explorer usando le credenziali di amministratore o coamministratore globale per il tenant.
    
    c. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Se si usano più directory, è consigliabile seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Dall'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione in tutte le entità servizio elencate. Cercare l'**ID oggetto** che è stato copiato dalla pagina Proprietà e usare la query seguente per ottenere la rispettiva entità servizio.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Estrarre la proprietà appRoles dall'oggetto entità servizio.
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. Per aggiornare il ruolo esistente, seguire questa procedura:

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Modificare il metodo da **GET** a **PATCH**.

    * Copiare i ruoli esistenti dall'applicazione e incollarli nel **corpo della richiesta**.
    
    * Aggiornare il valore del ruolo sostituendo la **descrizione del ruolo**, il **valore del ruolo** e il **nome visualizzato del ruolo** in base alle esigenze dell'organizzazione.
    
    * Dopo avere aggiornato tutti i ruoli richiesti, fare clic su **Esegui query**.
        
## <a name="delete-existing-role"></a>Eliminare un ruolo esistente

1. Per eliminare un ruolo esistente, seguire questa procedura:

    a. Aprire [Graph Explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

    b. Accedere al sito di Graph Explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

    c. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Se si usano più directory, è consigliabile seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Dall'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione in tutte le entità servizio elencate. Cercare l'**ID oggetto** che è stato copiato dalla pagina Proprietà e usare la query seguente per ottenere la rispettiva entità servizio.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Estrarre la proprietà appRoles dall'oggetto entità servizio.
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. Per eliminare il ruolo esistente, seguire questa procedura:

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    Modificare il metodo da **GET** a **PATCH**.

    Copiare i ruoli esistenti dall'applicazione e incollarli nel **corpo della richiesta**.
    
    Impostare il valore **IsEnabled** su **false** per il ruolo da eliminare.

    Fare clic su **Esegui query**.
    
    > [!NOTE] 
    > Assicurarsi di disporre del ruolo utente **msiam_access** e che l'ID sia corrispondente nel ruolo generato.
    
    g. Dopo avere eseguito il processo precedente, mantenere il metodo **PATCH** e incollare il contenuto del ruolo rimanente nel **corpo della richiesta**, quindi fare clic su **Esegui query**.
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. Dopo l'esecuzione della query il ruolo verrà eliminato.
    
    > [!NOTE]
    > Il ruolo deve essere disabilitato prima di poter essere rimosso. 

## <a name="next-steps"></a>Passaggi successivi

Fare riferimento alla [documentazione dell'app](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list) per i passaggi aggiuntivi.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
