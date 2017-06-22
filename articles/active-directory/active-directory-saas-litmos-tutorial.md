---
title: Esercitazione Integrazione di Azure Active Directory con Litmos | Documentazione Microsoft
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Litmos.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2c967d759806b47862179ecf9e9a89801134192a
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Esercitazione Integrazione di Azure Active Directory con Litmos
Questa esercitazione descrive l'integrazione di Litmos con Azure Active Directory (Azure AD).  

L'integrazione di Litmos con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a Litmos 
* È possibile abilitare gli utenti per l'accesso Single Sign-On (SSO) automatico a Litmos con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: Azure Active Directory 

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Litmos, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Litmos abilitata per l'accesso Single Sign-On

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.  

Lo scenario descritto in questa esercitazione è costituito da tre blocchi principali:

1. Aggiunta di Litmos dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-litmos-from-the-gallery"></a>Aggiungere Litmos dalla raccolta
Per configurare l'integrazione di Litmos in Azure AD, è necessario aggiungere Litmos dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Litmos dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Litmos**.
   
    ![Applicazioni][5]
7. Nel riquadro dei risultati selezionare **Litmos** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Applicazioni][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Litmos in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Litmos che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Litmos.  

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in Litmos.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Litmos, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Litmos](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in Litmos collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure AD classico e configurare l'accesso Single Sign-On nell'applicazione Litmos.  

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  

Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

La procedura di configurazione prevede la personalizzazione degli **Attributi token SAML** per l'applicazione Litmos.  

![Accesso Single Sign-On di Azure AD][17] 

**Per configurare l'accesso Single Sign-On di Azure AD con Litmos, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Litmos** del portale di Azure AD classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **How would you like users to sign on to Litmos** (Stabilire come si desidera che gli utenti accedano a Litmos) selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][7] 
3. Accedere al sito dell'azienda Litmos (ad esempio *https://azureapptest.litmos.com/account/Login*) come amministratore.
   
    ![Single Sign-On di Microsoft Azure AD][21] 
4. Nella barra di spostamento sul lato sinistro fare clic su **Accounts**(Account).
   
    ![Accesso Single Sign-On di Azure AD][22] 
5. Fare clic sulla scheda **Integrations** (Integrazioni).
   
    ![Single Sign-On di Microsoft Azure AD][23] 
6. Nella scheda **Integrations** (Integrazioni) scorrere verso il basso fino a **3rd Party Integrations** (Integrazioni di terze parti) e quindi fare clic sulla scheda **SAML 2.0** (SAML 2.0).
   
    ![Single Sign-On di Microsoft Azure AD][24] 
7. Copiare il valore in **The SAML endpoint for litmos is:**(L'endpoint SAML per litmos è:).
   
    ![Accesso Single Sign-On di Azure AD][26] 
8. Nella pagina della finestra di dialogo **Configurare le impostazioni dell'app** del portale di Azure classico seguire questa procedura:
   
    ![Single Sign-On di Microsoft Azure AD][8] 
   
    1. Nella casella di testo **Identificatore** digitare l'URL utilizzato dagli utenti per accedere all'applicazione Litmos, ad esempio *https://azureapptest.litmos.com/account/Login*.
   
    2. Nella casella di testo **URL di risposta** incollare il valore copiato dall'applicazione Litmos nel passaggio precedente.
   
    3. Fare clic su **Avanti**.
9. Nella pagina **Configure single sign-on at Litmos** (Configura accesso Single Sign-On in Litmos) seguire questa procedura:
   
    ![Single Sign-On di Microsoft Azure AD][2] 
   
    * Fare clic su Download certificato e quindi salvare il file nel computer.
10. Nell'applicazione **Litmos** seguire questa procedura:
    
     ![Single Sign-On di Microsoft Azure AD][25] 
    
     1. Fare clic su **Enable SAML**.
    
     2. Creare un file con **codifica Base 64** dal certificato scaricato.  
    >[!TIP]
    >Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
     >

     3. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **SAML X.509 Certificate** .
    
     4. Fare clic su **Salva modifiche**.
11. Nel portale di Azure AD classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. 
    
     ![Single Sign-On di Microsoft Azure AD][10]
12. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
     ![Single Sign-On di Microsoft Azure AD][11]
13. Nel menu in alto fare clic su **Attributi** to open the **SAML Token Attributi** . 
    
    ![Configura accesso Single Sign-On][12]
14. Nella finestra di dialogo **Aggiungi attributo utente** seguire questa procedura: 
    
    ![Configura accesso Single Sign-On][14]
    
    | Nome attributo | Valore attributo |
    | --- | --- |
    | Email |user.mail |
    | FirstName |user.givenname |
    | LastName |user.surname |
    
    Per ogni riga di dati nella tabella precedente, seguire questa procedura:
    
 1. Fare clic su **Aggiungi attributo utente**.    
   ![Configura accesso Single Sign-On][15]
 2. Nella casella di testo **Nome attributo** digitare il **nome dell'attributo** indicato per quella riga.
 3. Selezionare il **Valore attributo** indicato per la riga.
 4. Fare clic su **Completa** per chiudere la finestra di dialogo **Aggiungi attributo utente**.


1. Fare clic su **Applica modifiche**. 
   
   ![Configura accesso Single Sign-On][16]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.  

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  
   
    1. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
   
    2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura: 
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
   
   1. Nella casella di testo **Nome** digitare **Britta**.  
   
   2. Nella casella di testo **Cognome** digitare **Simon**.
   
   3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   4. Nell'elenco **Ruolo** selezionare **Utente**.
   5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
   
    1. Prendere nota del valore visualizzato in **Nuova password**.
   
    2. Fare clic su **Completa**.   

### <a name="create-a-litmos-test-user"></a>Creare un utente di test di Litmos
Questa sezione descrive come creare un utente chiamato Britta Simon in Litmos.  
L'applicazione Litmos supporta il provisioning JIT (just-in-time). Questo significa che, se necessario, un account utente viene creato automaticamente durante il tentativo di accedere all'applicazione tramite il pannello di accesso.

**Per creare un utente denominato Britta Simon in Litmos, seguire questa procedura:**

1. Accedere al sito dell'azienda Litmos (ad esempio *https://azureapptest.litmos.com/account/Login*) come amministratore.
   
    ![Single Sign-On di Microsoft Azure AD][21] 
2. Nella barra di spostamento sul lato sinistro fare clic su **Accounts**(Account).
   
    ![Accesso Single Sign-On di Azure AD][22] 
3. Fare clic sulla scheda **Integrations** (Integrazioni).
   
    ![Single Sign-On di Microsoft Azure AD][23] 
4. Nella scheda **Integrations** (Integrazioni) scorrere verso il basso fino a **3rd Party Integrations** (Integrazioni di terze parti) e quindi fare clic sulla scheda **SAML 2.0** (SAML 2.0).
   
    ![Single Sign-On di Microsoft Azure AD][24] 
5. Selezionare **Autogenerate Users**(Genera utenti in automatico).
   
    ![Single Sign-On di Microsoft Azure AD][27] 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Litmos.

![Assegna utente][200] 

**Per assegnare Britta Simon a Litmos, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Litmos**.
   
    ![Assegna utente][202] 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro Litmos nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Litmos.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png






