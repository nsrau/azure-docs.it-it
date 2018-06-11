---
title: Configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali in Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali in Azure Active Directory
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
ms.date: 05/30/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5aa716f91a3155e81ef8dc7c436b4a9a5811238b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34723253"
---
# <a name="configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali in Azure Active Directory

Usando Azure Active Directory (Azure AD), è possibile personalizzare il tipo di attestazione per l'attestazione basata su ruolo nel token di risposta ricevuto dopo aver autorizzato un'app.

## <a name="prerequisites"></a>Prerequisiti
- Una sottoscrizione di Azure AD con la directory installata.
- Sottoscrizione con Single Sign-On (SSO) abilitato. È necessario configurare SSO con l'applicazione.

## <a name="when-to-use-this-feature"></a>Quando usare questa funzionalità

Se l'applicazione prevede che vengano passati ruoli personalizzati in una risposta SAML, è necessario usare questa funzionalità. È possibile creare tutti i ruoli necessari che devono essere passati nuovamente da Azure AD all'applicazione.

## <a name="create-roles-for-an-application"></a>Creare ruoli per un'applicazione

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Icona Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali**. Selezionare quindi **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare il nome dell'applicazione e quindi selezionare l'applicazione nel pannello dei risultati. Selezionare quindi il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Applicazione nell'elenco dei risultati](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Dopo avere aggiunto l'applicazione, passare alla pagina **Proprietà** e copiare l'ID oggetto.

    ![Pagina Proprietà](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Aprire [Graph explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra e completare questi passaggi:

    a. Accedere al sito Graph explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

    b. Sono necessarie autorizzazioni sufficienti per creare i ruoli. Selezionare **Autorizzazioni di modifica** per ottenere le autorizzazioni. 

      ![Pulsante Autorizzazioni di modifica](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selezionare le autorizzazioni seguenti nell'elenco (se non sono già disponibili) e quindi **Autorizzazioni di modifica**. 

      ![Elenco delle autorizzazioni e pulsante Autorizzazioni di modifica](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Accettare la richiesta di consenso. Si verrà riconnessi al sistema.

    e. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
      Se si usano più directory, seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
      ![Finestra di dialogo Graph explorer, con la query per recuperare le entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Nell'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione tra tutte le entità servizio elencate. Cercare l'ID oggetto copiato dalla pagina **Proprietà** e usare la query seguente per ottenere l'entità servizio:
    
      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Query per ottenere l'entità servizio che è necessario modificare](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Estrarre la proprietà **appRoles** dall'oggetto entità servizio. 

      ![Dettagli della proprietà appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Se si usa l'app personalizzata e non l'app Azure Marketplace, verranno visualizzati due ruoli predefiniti: user e msiam_access. Per l'app Marketplace, msiam_access è l'unico ruolo predefinito. Non è necessario apportare modifiche ai ruoli predefiniti.

    h. Generare nuovi ruoli per l'applicazione. 

      Il codice JSON seguente è un esempio dell'oggetto **appRoles**. Creare un oggetto simile per aggiungere i ruoli desiderati per l'applicazione. 

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
      > È possibile aggiungere nuovi ruoli solo dopo msiam_access per l'operazione patch. Inoltre, è possibile aggiungere tutti i ruoli necessari per l'organizzazione. Azure AD invierà il valore di questi ruoli come valore dell'attestazione nella risposta SAML. Per generare i valori GUID per l'ID dei nuovi ruoli, usare strumenti Web come [questo](https://www.guidgenerator.com/)
    
    i. Tornare a Graph explorer e modificare il metodo da **GET** a **PATCH**. Applicare la patch all'oggetto entità servizio in modo da avere i ruoli desiderati aggiornando la proprietà **appRoles** perché sia simile a quella mostrata nell'esempio precedente. Selezionare **Esegui query** per eseguire l'operazione patch. Un messaggio di operazione completata conferma la creazione del ruolo.

      ![Operazione patch con messaggio di operazione completata](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Dopo che all'entità servizio è stata applicata la patch con più ruoli, è possibile assegnare gli utenti ai rispettivi ruoli. Per assegnare gli utenti, è possibile accedere al portale e passare all'applicazione. Selezionare la scheda **Utenti e gruppi**. La scheda elenca tutti gli utenti e i gruppi già assegnati all'app. È possibile aggiungere nuovi utenti nei nuovi ruoli. È anche possibile selezionare un utente esistente e fare clic su **Modifica** per modificare il ruolo.

    ![Scheda Utenti e gruppi](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Per assegnare il ruolo a un utente, selezionare il nuovo ruolo e fare clic sul pulsante **Assegna** nella parte inferiore della pagina.

    ![Riquadro Modifica assegnazione e riquadro Selezionare un ruolo](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > È necessario aggiornare la sessione nel portale di Azure per visualizzare i nuovi ruoli.

8. Aggiornare la tabella **Attributi** per definire un mapping personalizzato dell'attestazione basata su ruolo.

9. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come mostrato nell'immagine e completare i passaggi seguenti.
    
    | Nome attributo | Valore attributo |
    | -------------- | ----------------|    
    | Nome del ruolo      | user.assignedrole |

    a. Selezionare **Aggiungi attributo** per aprire il riquadro **Aggiungi attributo**.

      ![Pulsante Aggiungi attributo](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Riquadro Aggiungi attributo](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo necessario. Questo esempio usa **Nome ruolo** come nome di attestazione.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Selezionare **OK**.

10. Per testare l'applicazione in modalità Single Sign-On avviata da un provider di identità, accedere a [Riquadro di accesso ](https://myapps.microsoft.com) e selezionare il riquadro dell'applicazione. Nel token SAML verranno visualizzati tutti i ruoli assegnati per l'utente con il nome di attestazione specificato.

## <a name="update-an-existing-role"></a>Aggiornare un ruolo esistente

Per aggiornare un ruolo esistente, completare questi passaggi:

1. Aprire [Graph explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer).

2. Accedere al sito Graph explorer usando le credenziali di amministratore o coamministratore globale per il tenant.
    
3. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Se si usano più directory, seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Finestra di dialogo Graph explorer, con la query per recuperare le entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Nell'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione tra tutte le entità servizio elencate. Cercare l'ID oggetto copiato dalla pagina **Proprietà** e usare la query seguente per ottenere l'entità servizio:
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Query per ottenere l'entità servizio che è necessario modificare](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Estrarre la proprietà **appRoles** dall'oggetto entità servizio.
    
    ![Dettagli della proprietà appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Per aggiornare un ruolo esistente, completare i passaggi seguenti.

    ![Corpo della richiesta per "PATCH," con "description" e "displayname" evidenziati](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    a. Modificare il metodo da **GET** a **PATCH**.

    b. Copiare i ruoli esistenti e incollarli in **Corpo della richiesta**.

    c. Aggiornare il valore di un ruolo aggiornando la descrizione del ruolo, il valore del ruolo o il nome visualizzato del ruolo nel modo necessario.

    d. Dopo avere aggiornato tutti i ruoli necessari, fare clic su **Esegui query**.
        
## <a name="delete-an-existing-role"></a>Eliminare un ruolo esistente

Per eliminare un ruolo esistente, completare questi passaggi:

1. Aprire [Graph explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

2. Accedere al sito Graph explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

3. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Se si usano più directory, seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Finestra di dialogo Graph explorer, con la query per recuperare l'elenco delle entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Nell'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione tra tutte le entità servizio elencate. Cercare l'ID oggetto copiato dalla pagina **Proprietà** e usare la query seguente per ottenere l'entità servizio:
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Query per ottenere l'entità servizio che è necessario modificare](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Estrarre la proprietà **appRoles** dall'oggetto entità servizio.
    
    ![Dettagli della proprietà appRoles dall'oggetto entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Per eliminare il ruolo esistente, completare i passaggi seguenti.

    ![Corpo della richiesta per "PATCH," con IsEnabled impostato su false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Modificare il metodo da **GET** a **PATCH**.

    b. Copiare i ruoli esistenti dall'applicazione e incollarli in **Corpo della richiesta**.
        
    c. Impostare il valore di **IsEnabled** su **false** per il ruolo che si vuole eliminare.

    d. Selezionare **Esegui query**.
    
    > [!NOTE] 
    > Assicurarsi di avere il ruolo msiam_access e che l'ID corrisponda nel ruolo generato.
    
7. Dopo che il ruolo è stato disabilitato, eliminare il blocco del ruolo dalla sezione **appRoles**. Mantenere il metodo come **PATCH** e selezionare **Esegui query**.
    
8. Al termine dell'esecuzione della query, il ruolo viene eliminato.
    
    > [!NOTE]
    > Il ruolo deve essere disabilitato prima che possa essere rimosso. 

## <a name="next-steps"></a>Passaggi successivi

Per i passaggi aggiuntivi, vedere la [documentazione dell'app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png