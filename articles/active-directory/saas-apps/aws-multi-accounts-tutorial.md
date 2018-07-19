---
title: 'Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS) per connettere più account | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure AD e più account di Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: dfbabdc7d5a02b21103ac709b8dbc89d69cde0e0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054264"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Esercitazione: Integrazione di Azure Active Directory con più account di Amazon Web Service (AWS)

Questa esercitazione descrive come integrare Azure Active Directory (Azure AD) con più account di Amazon Web Services (AWS).

L'integrazione di Amazon Web Service (AWS) con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Amazon Web Services (AWS).
- È possibile abilitare gli utenti per l'accesso automatico ad Amazon Web Services (AWS) (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Amazon Web Service (AWS), sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD Basic o Premium
- Più account di Amazon Web Services (AWS) abilitati per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Amazon Web Service (AWS) dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Aggiunta di Amazon Web Service (AWS) dalla raccolta
Per configurare l'integrazione di Amazon Web Service (AWS) in Azure AD, è necessario aggiungere Amazon Web Service (AWS) dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Amazon Web Service (AWS) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Amazon Web Services (AWS)** selezionare **Amazon Web Services (AWS)** nel riquadro dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Amazon Web Services (AWS) nell'elenco dei risultati](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Dopo avere aggiunto l'applicazione, andare alla pagina **Proprietà** e copiare l'**ID oggetto**.

    ![Amazon Web Services (AWS) nell'elenco dei risultati](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Amazon Web Services (AWS) usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Amazon Web Service (AWS) che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l’utente correlato in Amazon Web Service (AWS).

In Amazon Web Services (AWS) assegnare il valore del **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Amazon Web Services (AWS).

**Per configurare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), seguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Amazon Web Services (AWS)** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Nella sezione **URL e dominio Amazon Web Services (AWS)** l'utente non deve eseguire alcuna operazione perché l'applicazione è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. L'applicazione Amazon Web Services (AWS) si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configurare l'attributo Single Sign-On](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)    

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo  | Valore attributo | Spazio dei nomi |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Ruolo            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >È necessario configurare il provisioning dell'utente in Azure AD per recuperare tutti i ruoli dalla Console AWS. Vedere la procedura di provisioning seguente.

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta dell'accesso Single Sign-On](./media/aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Configurare l'attributo Single Sign-On](./media/aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Nella casella di testo **Spazio dei nomi**, digitare il valore dello spazio dei nomi indicato per la riga.
    
    d. Fare clic su **OK**.

6. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/aws-multi-accounts-tutorial/tutorial_general_400.png)

8. In un'altra finestra del browser accedere al sito aziendale di Amazon Web Service (AWS) come amministratore.

9. Fare clic su **AWS** nella home page della console.
   
    ![Configurare la home page dell'accesso Single Sign-On][11]

10. Fare clic su **IAM** (Identity and Access Management). 
   
    ![Configurare l'identità dell'accesso Single Sign-On][12]

11. Fare clic su **Provider di identità** e quindi fare clic su **Create Provider** (Crea provider). 
   
    ![Configurare il provider dell'accesso Single Sign-On][13]

12. Nella pagina **Configure Provider** seguire questa procedura: 
   
    ![Configurare la finestra di dialogo dell'accesso Single Sign-On][14]
 
    a. In **Tipo provider** selezionare **SAML**.

    b. Nella casella di testo **Nome provider** digitare un nome di provider, ad esempio *WAAD*.

    c. Per caricare il **file di metadati** scaricato dal portale di Azure, fare clic su **Scegli file**.

    d. Fare clic su **Next Step**.

13. Nella pagina della finestra di dialogo **Verify Provider Information** (Verifica informazioni provider) fare clic su **Crea**. 
    
    ![Configurare la verifica dell'accesso Single Sign-On][15]

14. Fare clic su **Roles** (Ruoli) e quindi su **Create role** (Crea ruolo). 
    
    ![Configurare i ruoli dell'accesso Single Sign-On][16]

15. Nella pagina **Create role** (Crea ruolo) seguire questa procedura:  
    
    ![Configurare una relazione di trust dell'accesso Single Sign-On][19] 

    a. Selezionare **SAML 2.0 federation** (Federazione SAML 2.0) in **Select type of trusted entity** (Selezionare un tipo di entità attendibile).

    b. Nella sezione **Choose a SAML 2.0 Provider** (Scegliere un provider SAML 2.0) selezionare il **provider SAML** creato in precedenza, ad esempio *WAAD*.

    c. Selezionare **Allow programmatic and AWS Management Console access** (Consenti accesso a livello di codice e tramite AWS Management Console).
  
    d. Fare clic su **Next: Permissions** (Avanti: Autorizzazioni).

16. Nella finestra di dialogo **Attach Permissions Policies** (Collega i criteri di autorizzazione) fare clic su **Next: Review** (Avanti: Riepilogo).  
    
    ![Configurare criteri dell'accesso Single Sign-On][33]

17. Nella finestra di dialogo **Review** seguire questa procedura:   
    
    ![Riepilogo della configurazione dell'accesso Single Sign-On][34] 

    a. Nella casella di testo **Role name** (Nome ruolo) immettere il nome del ruolo.

    b. Nella casella di testo **Role description** (Descrizione ruolo) immettere la descrizione.

    a. Fare clic su **Crea ruolo**.

    b. Creare tutti i ruoli necessari in base alle esigenze ed eseguirne il mapping per il Provider di identità.

18. Disconnettersi dall'account AWS corrente e accedere con l'altro account in cui si vuole configurare Single Sign-On con Azure AD.

19. Eseguire i passaggi da 9 a 17 per creare più ruoli da configurare per questo account. Se gli account sono più di due, eseguire la stessa procedura per tutti gli account per creare i ruoli.

20. Dopo che tutti i ruoli sono stati creati negli account, vengono visualizzati nell'elenco **Roles** (Ruoli) per tali account.

    ![Configurazione dei ruoli](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. È necessario acquisire tutti gli ARN di ruolo e le entità attendibili per tutti i ruoli in tutti gli account, che servono per eseguire il mapping manuale con l'applicazione Azure AD. 

22. Fare clic sui ruoli per copiare i valori di **Role ARN** (ARN del ruolo) e **Trusted Entities** (Entità attendibili). Questi valori sono necessari per tutti i ruoli da creare in Azure AD.

    ![Configurazione dei ruoli](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. Eseguire il passaggio precedente per tutti i ruoli in tutti gli account e archiviarli tutti nel formato **Ruolo ARN,Entità attendibili** nel Blocco note. 

24. Aprire [Graph Explorer di Azure AD](https://developer.microsoft.com/graph/graph-explorer) in un'altra finestra.

    a. Accedere al sito di Graph explorer usando le credenziali di amministratore o coamministratore globale per il tenant.

    b. È necessario avere autorizzazioni sufficienti per creare i ruoli. Fare clic su **modify permissions** (autorizzazioni di modifica) per ottenere le autorizzazioni necessarie. 

    ![Finestra di dialogo di Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Selezionare le autorizzazioni seguenti dell'elenco (se non sono già disponibili) e fare clic su "Autorizzazioni di modifica" 

    ![Finestra di dialogo di Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Verrà chiesto di eseguire di nuovo l'accesso e di accettare il consenso. Dopo aver accettato il consenso, si è nuovamente connessi a Graph explorer.

    e. Modificare l'elenco a discesa della versione con **beta**. Per recuperare tutte le entità servizio dal tenant, usare la query seguente:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Se si usano più directory, è possibile usare il modello seguente, che include il dominio primario `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Finestra di dialogo di Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. Dall'elenco delle entità servizio recuperate ottenere quella da modificare. È anche possibile usare CTRL+F per cercare l'applicazione in tutte le entità servizio elencate. È possibile usare la query seguente con l'**ID oggetto** che è stato copiato dalla pagina delle proprietà di Azure AD per passare alla rispettiva entità servizio.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Finestra di dialogo di Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Estrarre la proprietà appRoles dall'oggetto entità servizio. 

    ![Finestra di dialogo di Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

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
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > È possibile aggiungere i nuovi ruoli solo dopo **msiam_access** per l'operazione patch. È anche possibile aggiungere tutti i ruoli desiderati in base alle esigenze dell'organizzazione. Azure AD invierà il **valore** di questi ruoli come valore attestazione nella risposta SAML.
    
    j. Tornare a Graph explorer e cambiare il metodo da **GET** a **PATCH**. Applicare la patch all'oggetto entità servizio per avere i ruoli desiderati aggiornando la proprietà appRoles in modo simile a quella visualizzata sopra nell'esempio. Fare clic su **Esegui query** per eseguire l'operazione patch. Un messaggio di operazione completata conferma la creazione del ruolo per l'applicazione Amazon Web Services.

    ![Finestra di dialogo di Graph Explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Dopo che all'entità servizio è stata applicata la patch con più ruoli, è possibile assegnare gli utenti o i gruppi ai rispettivi ruoli. Questa operazione può essere eseguita accedendo al portale e passando all'applicazione Amazon Web Services. Fare clic sulla scheda **Utenti e gruppi** nella parte superiore. 

26. È consigliabile creare nuovi gruppi per ogni ruolo AWS in modo che sia possibile assegnare un determinato ruolo in un determinato gruppo. Si noti che si tratta di un mapping uno-a-uno per un gruppo a un ruolo. È quindi possibile aggiungere i membri che appartengono a tale gruppo.

27. Dopo che sono stati creati i gruppi, selezionare il gruppo e assegnarlo all'applicazione. 

    ![Configurare l'aggiunta del Single Sign-On](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. Per assegnare il ruolo al gruppo, selezionare il ruolo e fare clic sul pulsante **Assegna** nella parte inferiore della pagina.

    ![Configurare l'aggiunta del Single Sign-On](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Si noti che è necessario aggiornare la sessione nel portale di Azure per visualizzare i nuovi ruoli.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Amazon Web Service (AWS) nel riquadro di accesso, si dovrebbe accedere alla pagina dell'applicazione Amazon Web Service (AWS) con l'opzione per selezionare il ruolo.

![Configurare l'aggiunta del Single Sign-On](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

È anche possibile verificare la risposta SAML per visualizzare i ruoli passati come attestazioni.

![Configurare l'aggiunta del Single Sign-On](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

