---
title: 'Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Amazon Web Services.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 0fb9c8f428368271b548e3f174726fa01ea910c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS)

Questa esercitazione descrive come integrare Amazon Web Services (AWS) con Azure Active Directory (Azure AD).

L'integrazione di Amazon Web Service (AWS) con Azure AD offre i vantaggi seguenti:

- è possibile controllare in Azure AD chi può accedere ad Amazon Web Service (AWS)
- è possibile abilitare gli utenti per l'accesso automatico ad Amazon Web Service (AWS) (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Amazon Web Service (AWS), sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione Amazon Web Service (AWS) abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Amazon Web Service (AWS) dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Aggiunta di Amazon Web Service (AWS) dalla raccolta
Per configurare l'integrazione di Amazon Web Service (AWS) in Azure AD, è necessario aggiungere Amazon Web Service (AWS) dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Amazon Web Service (AWS) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Amazon Web Service (AWS)**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. Nel riquadro dei risultati selezionare **Amazon Web Service (AWS)** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Amazon Web Services (AWS) usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Amazon Web Service (AWS) che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l’utente correlato in Amazon Web Service (AWS).

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **NomeUtente** in Amazon Web Service (AWS).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test in Amazon Web Service](#creating-an-amazon-web-services-test-user)**: per avere una controparte di Britta Simon in Amazon Web Service (AWS) collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Amazon Web Services (AWS).

**Per configurare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), seguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Amazon Web Services (AWS)** del Portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. Nella sezione **URL e dominio Amazon Web Services (AWS)** l'utente non deve eseguire alcuna operazione perché l'applicazione è già preintegrata in Azure.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. L'applicazione Amazon Web Services (AWS) si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo  | Valore attributo | Spazio dei nomi |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://AWS.Amazon.com/SAML/Attributes |
    | Ruolo            | user.assignedroles |  https://AWS.Amazon.com/SAML/Attributes |
    
    >[!TIP]
    >È necessario configurare il provisioning dell'utente in Azure AD per recuperare tutti i ruoli dalla Console AWS. Consultare le seguente procedura di provisioning.

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga. Aggiungere il valore di Namespace come indicato in precedenza.
    
    d. Fare clic su **OK**.

7. Fare clic sul pulsante **Salva** per salvare le impostazioni in Azure.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. In un'altra finestra del Web browser accedere al sito aziendale di Amazon Web Service (AWS) come amministratore.

9. Fare clic su **Console Home**.
   
    ![Configura accesso Single Sign-On][11]

10. Fare clic su **IAM** dal servizio **Sicurezza, identità e conformità**.
   
    ![Configura accesso Single Sign-On][12]

11. Fare clic su **Provider di identità** e quindi fare clic su **Create Provider** (Crea provider).
   
    ![Configura accesso Single Sign-On][13]

12. Nella pagina **Configure Provider** seguire questa procedura:
   
    ![Configura accesso Single Sign-On][14]
 
    a. In **Tipo provider** selezionare **SAML**.

    b. Nella casella di testo **Provider Name** digitare un nome di provider, ad esempio *WAAD*.

    c. Per caricare il file di metadati scaricato, fare clic su **Choose file**.

    d. Fare clic su **Next Step**.

13. Nella pagina della finestra di dialogo **Verify Provider Information** (Verifica informazioni provider) fare clic su **Crea**. 
    
    ![Configura accesso Single Sign-On][15]

14. Fare clic su **Ruoli** e quindi fare clic su **Crea nuovo ruolo**. 
    
    ![Configura accesso Single Sign-On][16]

15. Nella finestra di dialogo **Set Role Name** seguire questa procedura: 
    
    ![Configura accesso Single Sign-On][17] 

    a. Nella casella d testo **Role Name** digitare un nome, ad esempio *TestUser*. 

    b. Fare clic su **Next Step**.

16. Nella finestra di dialogo **Select Role Type** seguire questa procedura: 
    
    ![Configura accesso Single Sign-On][18] 

    a. Selezionare **Role For Identity Provider Access**. 

    b. Nella sezione **Grant Web Single Sign-On (WebSSO) access to SAML providers** (Concedi accesso Web Single Sign-On (WebSSO) a provider SAML) fare clic su **Seleziona**.

17. Nella finestra di dialogo **Establish Trust** seguire questa procedura:  
    
    ![Configura accesso Single Sign-On][19] 

    a. Come provider SAML selezionare quello creato in precedenza, ad esempio *WAAD*
  
    b. Fare clic su **Next Step**.

18. Nella finestra di dialogo **Verify Role Trust** (Verifica attendibilità ruolo) fare clic su **Passaggio successivo**.
    
    ![Configura accesso Single Sign-On][32]

19. Nella finestra di dialogo **Attach Policy** (Allega criteri) fare clic su **Passaggio successivo**.
    
    ![Configura accesso Single Sign-On][33]

20. Nella finestra di dialogo **Review** seguire questa procedura:
    
    ![Configura accesso Single Sign-On][34]
 
    a. Fare clic su **Crea ruolo**.

    b. Creare tutti i ruoli necessari in base alle esigenze ed eseguirne il mapping per il Provider di identità.

21. Configurare ora il provisioning dell'utente per recuperare tutti i ruoli da AWS

    a. Nella Console AWS eseguire l'accesso con il proprio account radice

    b. Nell'angolo superiore destro fare clic sul nome e quindi fare clic sull'opzione **Le mie credenziali di sicurezza**. Verrà aperta una schermata come messaggio di avviso. Fare clic sul pulsante **Credenziali di sicurezza** per passare alla schermata.
        
       ![Configura accesso Single Sign-On][36]

       ![Configura accesso Single Sign-On][37]

    c. Nella sezione Access Keys (Chiavi di accesso) fare clic sul pulsante **Create New Access Key** (Crea nuova chiave di accesso). Verrà generata l'ID della chiave di accesso e un valore di token.
    
       ![Configura accesso Single Sign-On][38]

    d. Copiare entrambi questi valori e scaricarli, in modo da non perderli.

    e. Nella pagina di integrazione dell'applicazione Amazon Web Services (AWS) del Portale di Azure fare clic su **Provisioning**.
        
       ![Configura accesso Single Sign-On][35]

    f. Impostare la Modalità di provisioning su **Automatico**
        
       ![Configura accesso Single Sign-On][39]

    g. Ora in **clientsecret** e in **segreto token** incollare i valori corrispondenti, che sono stati copiati dalla Console AWS.
    
    h. È possibile fare clic sul pulsante **Test connessione** per testare la connettività. Quando questa viene eseguita correttamente è possibile avviare il connettore di provisioning.
       
       ![Configura accesso Single Sign-On][40]

    i. Ora abilitare lo stato di Provisioning su **Attiva**. Ciò consente di avviare il recupero dei ruoli dall'applicazione.

       ![Configura accesso Single Sign-On][41]

    > [!NOTE]
    > Il servizio di provisioning di Azure AD viene eseguito ogni volta dopo un certo lasso di tempo per sincronizzare i ruoli da AWS. Vengono visualizzati tutti i ruoli di AWS associati al Provider di identità in Azure AD ed è possibile usarli durante l'assegnazione dell'applicazione a utenti o gruppi.

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-amazon-web-services-test-user"></a>Creazione di un utente test in Amazon Web Service

Per permettere agli utenti di Azure AD di accedere ad Amazon Web Services (AWS), è necessario che eseguano il provisioning in Amazon Web Services (AWS). Nel caso di Amazon Web Services (AWS), il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Amazon Web Service (AWS)** come amministratore.

2. Fare clic sull'icona **Console Home** . 
   
    ![Configura accesso Single Sign-On][11]

3. Fare clic su Identity and Access Management. 
   
    ![Configura accesso Single Sign-On][28]

4. In Dashboard fare clic su **Utenti** e quindi fare clic su **Crea nuovi utenti**. 
   
    ![Configura accesso Single Sign-On][29]

5. Nella finestra di dialogo Create User seguire questa procedura: 
   
    ![Configura accesso Single Sign-On][30]   
    
    a. Nelle caselle di testo **Inserire Nomi Utenti** digitare il nome utente (userprincipalname) di Britta Simon in Azure AD.

    b. Fare clic su **Crea**.
        
### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Amazon Web Services (AWS).

![Assegna utente][200] 

**Per assegnare Britta Simon ad Amazon Web Services (AWS), seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Amazon Web Service (AWS)**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Nella scheda **Seleziona ruolo** selezionare il ruolo appropriato per l'utente. Tutti i ruoli vengono visualizzati con il nome del ruolo e il nome del provider di identità. In questo modo è possibile identificare facilmente i ruoli da AWS.

8. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Amazon Web Service (AWS) nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione Amazon Web Service (AWS). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
