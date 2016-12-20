---
title: 'Esercitazione: Integrazione di Azure Active Directory con MOVEit Transfer | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e MOVEit Transfer.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3207124143dbc148206ea33d6030a986226c4d66


---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer"></a>Esercitazione: Integrazione di Azure Active Directory con MOVEit Transfer
Questa esercitazione descrive l'integrazione di MOVEit Transfer con Azure Active Directory (Azure AD).

L'integrazione di MOVEit Transfer con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a MOVEit Transfer
* È possibile abilitare gli utenti per l'accesso automatico a MOVEit Transfer (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con MOVEit Transfer, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di MOVEit Transfer abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di MOVEit Transfer dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-moveit-transfer-from-the-gallery"></a>Aggiunta di MOVEit Transfer dalla raccolta
Per configurare l'integrazione di MOVEit Transfer in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere MOVEit Transfer dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **MOVEit Transfer**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_01.png)
7. Nel riquadro dei risultati selezionare **MOVEit Transfer** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con MOVEit Transfer in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di MOVEit Transfer che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MOVEit Transfer.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in MOVEit Transfer.

Per configurare e testare l'accesso Single Sign-On di Azure AD con MOVEit Transfer, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di MOVEit Transfer](#creating-a-moveit-transfer-test-user)** : per avere una controparte di Britta Simon in MOVEit Transfer collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione MOVEit Transfer.

**Per configurare l'accesso Single Sign-On di Azure AD con MOVEit Transfer, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **MOVEit Transfer** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a MOVEit Transfer** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_03.png)
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_04.png)
   
    a. Nel casella di testo **URL di accesso** , digitare l'URL di accesso con il proprio dominio.
   
    b. Nella casella di testo **Identificatore** digitare l'URL dell'ID entità.
   
    c. Nel casella di testo **URL di risposta** , digitare un URL dell'interfaccia del servizio consumer di asserzione abilitato.
   
    d. Fare clic su **Avanti**
   
   > [!NOTE]
   > È necessario aggiornare questi valori con l'URL di accesso e l'ID effettivi. Per ottenere questi valori, fare riferimento ai dettagli descritti nel passaggio 8 oppure contattare [MOVEit Transfer](https://www.ipswitch.com/support/technical-support).
   > 
   > 
4. Nella pagina **Configura accesso Single Sign-On in MOVEit Transfer** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_05.png)
   
    a. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
   
    b. Fare clic su **Next**.
5. Accedere al tenant di MOVEit Transfer come amministratore.
6. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)
7. Fare clic sul collegamento **Single Signon** link in **Security Policies -> User Auth** (Criteri di sicurezza -> Autenticazione utente).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)
8. Fare clic sul collegamento con l'URL dei metadati per scaricare il documento di metadati.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
   
   * Verificare che **entityID** corrisponda all'**Identificatore** nel passaggio 3.
   * Verificare che l'URL del percorso di **AssertionConsumerService** corrisponda all'**URL DI RISPOSTA** nel passaggio 3.
     
     ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)
9. Fare clic sul pulsante **Add Identity Provider** (Aggiungi provider di identità) per aggiungere un nuovo provider di identità federato.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)
10. Fare clic su **Browse** (Sfoglia) per selezionare il file di metadati scaricato nel passaggio 4, quindi fare clic su **Add Identity Provider** (Aggiungi provider di identità) per caricare il file scaricato. 
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)
11. Selezionare "**Yes**" (Sì) per **Enabled** (Abilitato) nella pagina **Edit Federated Identity Provider Settings** (Modifica impostazioni provider di identità federato) e fare clic su **Save** (Salva).
    
     ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)
12. Nella pagina **Edit Federated Identity Provider User Settings** (Modifica impostazioni utente del provider di identità federato) eseguire le operazioni seguenti e fare clic su **Save** (Salva).
    
    a. Selezionare **SAML NameID** (ID nome SAML) per **Login name** (Nome di accesso).
    
    b. Selezionare **Other** (Altro) per **Full name** (Nome completo) e nella casella di testo **Attribute name** (Nome dell'attributo) immettere il valore: http://schemas.microsoft.com/identity/claims/displayname.
    
    c. Selezionare **Other** (Altro) per **Email** (Posta elettronica) e nella casella **Attribute name** (Nome dell'attributo) immettere il valore: http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress.
    
    d. Selezionare **Yes** (Sì) per **Auto-create account on signon** (Crea automaticamente account all'accesso).
    
    e. Fare clic sul pulsante **Salva** .
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
13. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]
14. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_06.png)
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_08.png)
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-moveit-transfer-test-user"></a>Creazione di un utente test di MOVEit Transfer
Questa sezione descrive come creare un utente chiamato Britta Simon in MOVEit Transfer. MOVEit Transfer supporta il provisioning just-in-time, che è stato abilitato.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a MOVEit Transfer verrà creato un nuovo utente, se questo non esiste già.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il team di supporto di MOVEit Transfer.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MOVEit Transfer.

![Assegna utente][200]

**Per assegnare Britta Simon a MOVEit Transfer, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco delle applicazioni selezionare **MOVEit Transfer**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_50.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro MOVEit Transfer nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione MOVEit Transfer.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


