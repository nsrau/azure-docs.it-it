---
title: 'Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS) | Microsoft Docs'
description: Informazioni su come usare Amazon Web Services (AWS) con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 2cbd477776329ef84ef615b6c28c56cfee12fc99
ms.lasthandoff: 03/31/2017


---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS)
L’obiettivo di questa esercitazione è descrivere l’integrazione di Amazon Web Service (AWS) con Azure Active Directory (Azure AD).  

L'integrazione di Amazon Web Service (AWS) con Azure AD offre i vantaggi seguenti: 

* è possibile controllare in Azure AD chi può accedere ad Amazon Web Service (AWS) 
* È possibile abilitare gli utenti per l'accesso automatico ad Amazon Web Service (AWS) Single Sign-On (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Amazon Web Service (AWS), sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Amazon Web Service (AWS) abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.  

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Amazon Web Service (AWS) dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>Aggiungere Amazon Web Service (AWS) dalla raccolta
Per configurare l'integrazione di Amazon Web Service (AWS) in Azure AD, è necessario aggiungere Amazon Web Service (AWS) dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Amazon Web Service (AWS) dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1] 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore. 
   
    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina. 
   
    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**. 
   
    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Amazon Web Service (AWS)**.
   
    ![Applicazioni][5]

7. Nel riquadro dei risultati selezionare **Amazon Web Service (AWS)** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Applicazioni][6]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con Amazon Web Service (AWS) in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Amazon Web Service (AWS) che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l’utente correlato in Amazon Web Service (AWS).  

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **NomeUtente** in Amazon Web Service (AWS).

Per configurare e testare l'accesso SSO di Azure AD con Amazon Web Service (AWS), è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test in Amazon Web Service (AWS)](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in Amazon Web Service (AWS) collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso SSO di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Amazon Web Service (AWS).  

L'applicazione Amazon Web Service (AWS) prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi di token SAML**. 

La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On][27]

**Per configurare l'accesso Single Sign-On di Azure AD con Amazon Web Service (AWS), seguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Amazon Web Service (AWS)** del Portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][7]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Amazon Web Service (AWS)** selezionare **Azure AD Single Sign-On (Single Sign-On di Azure AD)** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On][8]

3. Nella pagina **Configurare le impostazioni dell'app** fare clic su **Avanti**. 
   
    ![Configurare le impostazioni dell'app][9]

4. Nella pagina **Configura accesso Single Sign-On in Amazon Web Service (AWS)** fare clic su **Download metadata (Scarica metadati)** e quindi salvare il file di metadati localmente sul computer.
   
    ![Configura accesso Single Sign-On][10]

5. In un'altra finestra del Web browser accedere al sito aziendale di Amazon Web Service (AWS) come amministratore.

6. Fare clic su **Console Home**.
   
    ![Configura accesso Single Sign-On][11]

7. Fare clic su **Identity and Access Management**. 
   
    ![Configura accesso Single Sign-On][12]

8. Fare clic su **Provider di identità** e quindi fare clic su **Create Provider** (Crea provider). 
   
    ![Configura accesso Single Sign-On][13]

9. Nella pagina **Configure Provider** seguire questa procedura: 
   
    ![Configura accesso Single Sign-On][14]   
  1. In **Tipo provider** selezionare **SAML**.
  2. Nella casella di testo **Provider Name** digitare un nome di provider, ad esempio *WAAD*.
  3. Per caricare il file di metadati scaricato, fare clic su **Choose file**.
  4. Fare clic su **Next Step**.

10. Nella pagina della finestra di dialogo **Verify Provider Information** (Verifica informazioni provider) fare clic su **Crea**. 
    
    ![Configura accesso Single Sign-On][15]

11. Fare clic su **Ruoli** e quindi fare clic su **Crea nuovo ruolo**. 
    
    ![Configura accesso Single Sign-On][16]

12. Nella finestra di dialogo **Set Role Name** seguire questa procedura: 
    
    ![Configura accesso Single Sign-On][17] 
  1. Nella casella d testo **Role Name** digitare un nome, ad esempio *TestUser*. 
  2. Fare clic su **Next Step**.

13. Nella finestra di dialogo **Select Role Type** seguire questa procedura: 
    
    ![Configura accesso Single Sign-On][18] 
  1. Selezionare **Role For Identity Provider Access**. 
  2. Nella sezione **Grant Web Single Sign-On (WebSSO) access to SAML providers** (Concedi accesso Web Single Sign-On (WebSSO) a provider SAML) fare clic su **Seleziona**.

14. Nella finestra di dialogo **Establish Trust** seguire questa procedura:  
    
    ![Configura accesso Single Sign-On][19] 
  1. Come provider SAML selezionare quello creato in precedenza, ad esempio *WAAD*.   
  2. Fare clic su **Next Step**.

15. Nella finestra di dialogo **Verify Role Trust** (Verifica attendibilità ruolo) fare clic su **Passaggio successivo**. 
    
    ![Configura accesso Single Sign-On][32]

16. Nella finestra di dialogo **Attach Policy** (Allega criteri) fare clic su **Passaggio successivo**.  
    
    ![Configura accesso Single Sign-On][33]

17. Nella finestra di dialogo **Review** seguire questa procedura:   
    
    ![Configura accesso Single Sign-On][34] 
  1. Copia il valore **Ruolo informazioni** .  
  2. Copia il valore **Entità Trusted** . 
  3. Fare clic su **Crea ruolo**. 

18. Nel portale di Azure classico selezionare la conferma della configurazione dell’accesso Single Sign-On e quindi fare clic su **Avanti**.
    
    ![Cos'è Azure AD Connect][20]

19. Nella pagina **Conferma Single Sign-On** fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Cos'è Azure AD Connect][22]

20. Nel menu in alto fare clic su **Attributi** to open the **SAML Token Attributi** . 
    
    ![Configura accesso Single Sign-On][21]

21. Fare clic su **Aggiungi attributo utente**. 
    
    ![Configura accesso Single Sign-On][23]

22. Nella finestra di dialogo Aggiungi attributo utente seguire questa procedura. 
    
    ![Configura accesso Single Sign-On][24]  
  1. Nella casella di testo **Nome attributo** digitare **https://aws.amazon.com/SAML/Attributes/Role**.  
  2. Nella casella di testo **Valore attributo** digitare **[valore ARN del ruolo],[valore ARN dell'entità attendibile]**.
    
     >[!TIP]
     >Questi sono i valori copiati dalla finestra di dialogo di revisione quando è stato creato il ruolo. 
     > 
     
  3. Fare clic su **Completa** per chiudere la finestra di dialogo **Aggiungi attributo utente**.

23. Fare clic su **Aggiungi attributo utente**. 
    
    ![Configura accesso Single Sign-On][23]

24. Nella finestra di dialogo Aggiungi attributo utente seguire questa procedura e fare clic su **Applica modifiche**. 
    
    ![Configura accesso Single Sign-On][25]
 1. Nella casella di testo **Nome attributo** digitare **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.
 2. Nella casella di testo **Valore attributo** digitare **user.userprincipalname** o selezionarlo nell'elenco a discesa.

     ![Configura accesso Single Sign-On][35]
 3. Fare clic su **Completa** per chiudere la finestra di dialogo **Aggiungi attributo utente**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_05.png) 
 1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
 2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
 3. Fare clic su Avanti.

6. Nella pagina **Profilo utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_06.png)  
 1. Nella casella di testo **Nome** digitare **Britta**.   
 2. Nella casella di testo **Cognome** digitare **Simon**. 
 3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**. 
 4. Nell'elenco **Ruolo** selezionare **Utente**. 
 5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_08.png)  
 1. Prendere nota del valore visualizzato in **Nuova password**.  
 2. Fare clic su **Complete**.   

### <a name="create-a-amazon-web-services-aws-test-user"></a>Creare un utente test in Amazon Web Service (AWS)
Questa sezione descrive come creare un utente chiamato Britta Simon in Amazon Web Service (AWS).

**Per creare un utente chiamato Britta Simon in Amazon Web Service (AWS), seguire questa procedura:**

1. Accedere al sito aziendale di **Amazon Web Service (AWS)** come amministratore.

2. Fare clic sull'icona **Console Home** . 
   
    ![Configura accesso Single Sign-On][11]

3. Fare clic su Identity and Access Management. 
   
    ![Configura accesso Single Sign-On][28]

4. In Dashboard, fare clic su Users e quindi fare clic su Create New Users. 
   
    ![Configura accesso Single Sign-On][29]

5. Nella finestra di dialogo Create User seguire questa procedura: 
   
    ![Configura accesso Single Sign-On][30]   
 1. Nelle caselle di testo **Inserire Nomi Utenti** digitare il nome utente (userprincipalname) di Britta Simon in Azure AD. 
 2. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso SSO di Azure concedendole l'accesso ad Amazon Web Service (AWS).

![Assegna utente][31]

**Per assegnare Britta Simon ad Amazon Web Services (AWS), seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][26]

2. Nell'elenco delle applicazioni selezionare **Amazon Web Service (AWS)**.
   
    ![Assegna utente][27]

3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][25]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][29]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro Amazon Web Service (AWS) nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione Amazon Web Service (AWS).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service-tutorial/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service-tutorial/capture24.png
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
[26]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/user_attributes_01.png























