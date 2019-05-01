---
title: Configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali in Azure AD | Microsoft Docs
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
ms.date: 04/22/2019
ms.author: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb426eb15d20a4bbd628897ed1c3b29c37f32afd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723294"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Procedura: Configurare l'attestazione basata su ruolo rilasciata nel token SAML per applicazioni aziendali

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

    > [!Note]
    > Il ruolo di amministratore di App cloud e App non funzionerà in questo scenario perché sono necessarie le autorizzazioni di amministratore globale per le directory in lettura e scrittura.

    d. Accettare la richiesta di consenso. Si verrà riconnessi al sistema.

    e. Modificare la versione in **beta** e recuperare l'elenco delle entità servizio dal tenant usando la query seguente:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Se si usano più directory, seguire questo modello: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Finestra di dialogo Graph explorer, con la query per recuperare le entità servizio](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Poiché le API sono già in fase di aggiornamento, è possibile che i clienti riscontrino alcuni problemi nel servizio.

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

9. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome attributo | Valore attributo |
    | -------------- | ----------------|
    | Nome del ruolo  | user.assignedroles |

    >[!NOTE]
    >Se il valore di attestazione di ruolo è null, quindi Azure AD non le invia questo valore nel token e questo è l'impostazione predefinita in base alla progettazione.

    a. Fare clic su **Edit** icona per aprire **gli attributi utente e le attestazioni** finestra di dialogo.

      ![Pulsante Aggiungi attributo](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. Nel **gestire le attestazioni utente** finestra di dialogo, aggiungere l'attributo del token SAML, fare clic su **Aggiungi nuova attestazione**.

      ![Pulsante Aggiungi attributo](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Riquadro Aggiungi attributo](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Nella casella di testo **Nome** digitare il nome dell'attributo necessario. Questo esempio usa **Nome ruolo** come nome di attestazione.

    d. Lasciare vuota la casella **Spazio dei nomi**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Selezionare **Salva**.

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

1. Aprire [Graph Explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

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
