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
ms.openlocfilehash: 94b451f66d286426f6dd2cc556e8c6785c3f743e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
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

    ![Pagina Proprietà](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.PNG)

6. Aprire [Graph Explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

    a. Accedere al sito di Graph Explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

    b. È necessario avere autorizzazioni sufficienti per creare i ruoli. Fare clic su **modify permissions** (autorizzazioni di modifica) per ottenere le autorizzazioni necessarie. 

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selezionare le autorizzazioni seguenti dell'elenco (se non sono già disponibili) e fare clic su "Autorizzazioni di modifica" 

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Verrà chiesto di eseguire di nuovo l'accesso e di accettare il consenso. Dopo aver accettato il consenso, si è nuovamente connessi al sistema.

    e. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Se si usano più directory, è consigliabile seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Dall'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione in tutte le entità servizio elencate. Cercare l'**ID oggetto** che è stato copiato dalla pagina Proprietà e usare la query seguente per ottenere la rispettiva entità servizio.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Estrarre la proprietà appRoles dall'oggetto entità servizio. 

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > Se si usa l'app personalizzata (non quella della raccolta), si noteranno i due ruoli predefiniti: User e msiam_access. Se si usa l'app della raccolta, msiam_access è l'unico ruolo predefinito. Non è necessario apportare modifiche ai ruoli predefiniti.

    h. È ora necessario generare nuovi ruoli per l'applicazione. 

    i. Di seguito è riportato un codice JSON di esempio di oggetto appRoles. Creare un oggetto simile per aggiungere i ruoli desiderati per l'applicazione. 

    ```
    {
       "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Administrators Only",
            "displayName": "Admin",
            "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "Administrator"
        }
    ]
    }
    ```
    > [!Note]
    > È possibile aggiungere i nuovi ruoli solo dopo **msiam_access** per l'operazione patch. È anche possibile aggiungere tutti i ruoli desiderati in base alle esigenze dell'organizzazione. Azure AD invierà il **valore** di questi ruoli come valore attestazione nella risposta SAML.
    
    j. Tornare a Graph explorer e cambiare il metodo da **GET** a **PATCH**. Applicare la patch all'oggetto entità servizio per avere i ruoli desiderati aggiornando la proprietà appRoles in modo simile a quella visualizzata sopra nell'esempio. Fare clic su **Esegui query** per eseguire l'operazione patch. Un messaggio di operazione completata conferma la creazione del ruolo.

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Dopo che all'entità servizio è stata applicata la patch con più ruoli, è possibile assegnare gli utenti ai rispettivi ruoli. Questa operazione può essere eseguita accedendo al portale e passando alla rispettiva app. Fare clic sulla scheda **Utenti e gruppi** nella parte superiore. Verranno elencati tutti gli utenti e i gruppi che sono già assegnati all'applicazione. È possibile aggiungere nuovi utenti nel nuovo ruolo, ma anche selezionare l'utente esistente e fare clic su **Modifica** per cambiare il ruolo.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     Per assegnare il ruolo a un utente, selezionare il nuovo ruolo e fare clic sul pulsante **Assegna** nella parte inferiore della pagina.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Si noti che è necessario aggiornare la sessione nel portale di Azure per visualizzare i nuovi ruoli.

8. Dopo l'assegnazione dei ruoli agli utenti, è necessario aggiornare la tabella **Attributi** per definire il mapping personalizzato dell'attestazione basata su **ruolo**.

9. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | -------------- | ----------------|    
    | Nome ruolo      | user.assignedrole |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Configurare l'attributo Single Sign-On](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo necessario. In questo esempio è stato usato **Nome ruolo** come nome di attestazione.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.

10. Per testare l'applicazione in modalità Single Sign-On avviata da IDP, accedere al Pannello di accesso (https://myapps.microsoft.com) e fare clic sul riquadro dell'applicazione. Nel token SAML dovrebbero essere visualizzati tutti i ruoli assegnati per l'utente con il nome dell'attestazione specificato.

## <a name="update-existing-role"></a>Aggiornare un ruolo esistente

Per aggiornare un ruolo esistente, seguire questa procedura:

1. Aprire [Graph explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer).

2. Accedere al sito di Graph Explorer usando le credenziali di amministratore o coamministratore globale per il tenant.
    
3. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Se si usano più directory, è consigliabile seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Dall'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione in tutte le entità servizio elencate. Cercare l'**ID oggetto** che è stato copiato dalla pagina Proprietà e usare la query seguente per ottenere la rispettiva entità servizio.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Estrarre la proprietà appRoles dall'oggetto entità servizio.
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Per aggiornare il ruolo esistente, seguire questa procedura:

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Modificare il metodo da **GET** a **PATCH**.

    * Copiare i ruoli esistenti e incollarli nel **corpo della richiesta**.

    * Aggiornare il valore del ruolo aggiornando la **descrizione del ruolo**, il **valore del ruolo** o il **nome visualizzato del ruolo**, se necessario.

    * Dopo avere aggiornato tutti i ruoli richiesti, fare clic su **Esegui query**.
        
## <a name="delete-existing-role"></a>Eliminare un ruolo esistente

Per eliminare un ruolo esistente, seguire questa procedura:

1. Aprire [Graph Explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

2. Accedere al sito di Graph Explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

3. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Se si usano più directory, è consigliabile seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Dall'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione in tutte le entità servizio elencate. Cercare l'**ID oggetto** che è stato copiato dalla pagina Proprietà e usare la query seguente per ottenere la rispettiva entità servizio.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Estrarre la proprietà appRoles dall'oggetto entità servizio.
    
    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Per eliminare il ruolo esistente, seguire questa procedura:

    ![Finestra di dialogo di Graph Explorer](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * Modificare il metodo da **GET** a **PATCH**.

    * Copiare i ruoli esistenti dall'applicazione e incollarli nel **corpo della richiesta**.
        
    * Impostare il valore **IsEnabled** su **false** per il ruolo da eliminare.

    * Fare clic su **Esegui query**.
    
    > [!NOTE] 
    > Assicurarsi di disporre del ruolo utente **msiam_access** e che l'ID sia corrispondente nel ruolo generato.
    
7. Dopo che il ruolo è stato disabilitato, eliminare il blocco del ruolo dalla sezione appRoles, mantenere il metodo **PATCH** e fare clic su **Esegui query**.
    
8. Dopo l'esecuzione della query il ruolo verrà eliminato.
    
    > [!NOTE]
    > Il ruolo deve essere disabilitato prima di poter essere rimosso. 

## <a name="next-steps"></a>Passaggi successivi

Fare riferimento alla [documentazione dell'app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) per i passaggi aggiuntivi.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
