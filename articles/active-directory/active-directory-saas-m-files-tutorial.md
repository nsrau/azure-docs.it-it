---
title: 'Esercitazione: Integrazione di Azure Active Directory con M-Files | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e M-Files.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bb78abd45760c17e94f35be0a6c78ab5d91253d3


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Esercitazione: Integrazione di Azure Active Directory con M-Files
Questa esercitazione descrive come integrare M-Files con Azure Active Directory (Azure AD).

L'integrazione di M-Files con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a M-Files
* È possibile abilitare gli utenti per l'accesso automatico a M-Files (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con M-Files, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di **M-Files** abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di M-Files dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-m-files-from-the-gallery"></a>Aggiunta di M-Files dalla raccolta
Per configurare l'integrazione di M-Files in Azure AD, è necessario aggiungere M-Files dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere M-Files dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **M-Files**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_01.png)
7. Nel riquadro dei risultati selezionare **M-Files** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con M-Files usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente controparte di M-Files che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in M-Files.
La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** in M-Files. Per configurare e testare l'accesso Single Sign-On di Azure AD con M-Files, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di M-Files](#creating-a-m-file-test-user)**: per avere una controparte di Britta Simon in M-Files collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione M-Files.

**Per configurare l'accesso Single Sign-On di Azure AD con M-Files, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **M-Files** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][7] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a M-Files** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_06.png)
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_07.png)

    a. Nella casella di testo URL di accesso digitare l'URL usando il modello seguente: `https://<tenant-name>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`.

    b. Fare clic su **Avanti**.


1. Nella pagina **Configura accesso Single Sign-On in M-Files** fare clic su **Scarica metadati** e salvare il file sul computer.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_09.png)
2. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il team di supporto di M-Files all'indirizzo<mailto:support@m-files.com> e fornire i metadati scaricati.
   
   > [!NOTE]
   > Se si desidera configurare l'accesso Single Sign-On per l'applicazione desktop M-Files, attenersi alla procedura seguente. Non sono necessarie operazioni aggiuntive se si desidera configurare l'accesso Single Sign-On per la versione Web di M-Files. 
   > 
   > 
3. Per configurare l'applicazione desktop M-Files in modo da abilitare l'accesso Single Sign-On con Azure AD attenersi alla procedura seguente. Per scaricare M-Files, passare alla pagina di [download di M-Files](https://www.m-files.com/en/download-latest-version).
4. Aprire la finestra **M-Files Desktop Settings** (Impostazioni M-Files Desktop). Fare quindi clic su **Aggiungi**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)
5. Nella finestra **Document Vault Connection Properties** (Proprietà connessione insieme di credenziali del documento) eseguire le seguenti operazioni:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)
   
    Nella sezione **Server** digitare i valori seguenti:
   
    a. Per **Nome** digitare `<tenant-name>.cloudvault.m-files.com`.
   
    b. Per **Numero porta** digitare **4466**.
   
    c. Per **Protocollo** selezionare **HTTPS**.
   
    d. Nel campo **Autenticazione** selezionare l'opzione relativa **Specific Windows user** (Utente specifico di Windows). Verrà visualizzata una pagina di accesso. Inserire le credenziali di Azure AD.
   
    e. Per **Vault on Server** (Insieme di credenziali nel server) selezionare l'insieme di credenziali corrispondente nel server.
   
    f. Fare clic su **OK**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-m-files-test-user"></a>Creazione di un utente di test per M-Files
In questa sezione viene creato un utente chiamato Britta Simon in M-Files. Se non si conosce la procedura per creare un utente in file M, contattare il supporto tecnico di M-File all'indirizzo <mailto:support@m-files.com>.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a M-Files.

![Assegna utente][200]

**Per assegnare Britta Simon a M-Files, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **M-Files**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_12.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro M-Files nel pannello di accesso, si accederà automaticamente all'applicazione M-Files.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-m-files-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


