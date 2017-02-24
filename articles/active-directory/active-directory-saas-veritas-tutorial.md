---
title: 'Esercitazione: Integrazione di Azure Active Directory con Veritas Enterprise Vault.cloud SSO | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Veritas Enterprise Vault.cloud SSO.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a8f9ae76cd914224b3715857b6fd37750f13cd7b
ms.openlocfilehash: 2d6dccdcb7eb6c08d707c2182f9af8fe4824e6f5


---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Esercitazione: Integrazione di Azure Active Directory con Veritas Enterprise Vault.cloud SSO

Questa esercitazione descrive come integrare Veritas Enterprise Vault.cloud SSO con Azure Active Directory (Azure AD).

L'integrazione di Veritas Enterprise Vault.cloud SSO con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Veritas Enterprise Vault.cloud SSO
- È possibile abilitare gli utenti per l'accesso automatico a Veritas Enterprise Vault.cloud SSO (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Veritas Enterprise Vault.cloud SSO, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Veritas Enterprise Vault.cloud SSO abilitata per l'accesso Single Sign-On.


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Veritas Enterprise Vault.cloud SSO dalla galleria
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Aggiunta di Veritas Enterprise Vault.cloud SSO dalla galleria
Per configurare l'integrazione di Veritas Enterprise Vault.cloud SSO in Azure AD, è necessario aggiungere Veritas Enterprise Vault.cloud SSO dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Veritas Enterprise Vault.cloud SSO, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.

    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Veritas Enterprise Vault.cloud SSO**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_01.png)
7. Nel pannello dei risultati selezionare **Veritas Enterprise Vault.cloud SSO** e quindi fare clic su **Completa** per aggiungere l'applicazione.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Veritas Enterprise Vault.cloud SSO usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Veritas Enterprise Vault.cloud SSO che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Veritas Enterprise Vault.cloud SSO.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente) in Veritas Enterprise Vault.cloud SSO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Veritas Enterprise Vault.cloud SSO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Veritas Enterprise Vault.cloud SSO](#creating-a-Veritas Enterprise Vault.cloud SSO-test-user)**: per avere una controparte di Britta Simon in Veritas Enterprise Vault.cloud SSO collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Veritas Enterprise Vault.cloud SSO.


**Per configurare l'accesso Single Sign-On di Azure AD con Veritas Enterprise Vault.cloud SSO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Veritas Enterprise Vault.cloud SSO** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
     
    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Veritas Enterprise Vault.cloud SSO** selezionare **Accesso Single Sign-On di Azure AD**, quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_03.png) 

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_04.png) 

    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Veritas Enterprise Vault.cloud SSO adottando il modello seguente: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`
    
     
4. Nella pagina **Configura accesso Single Sign-On in Veritas Enterprise Vault.cloud SSO** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_05.png)

    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.

    b. Fare clic su **Next**.


5. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il team di supporto di Veritas Enterprise Vault.cloud SSO sul <a href="https://www.veritas.com/content/support/en_US/62696.html ">sito Web di supporto tecnico</a> e fornire gli elementi seguenti:

    • Il file dei **metadati**

    • **URL SSO SAML**

6. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
 
    ![Single Sign-On di Microsoft Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.


![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:  ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_05.png) 

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_06.png) 

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/create_aaduser_08.png) 

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.   



### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Creazione di un utente test di Veritas Enterprise Vault.cloud SSO

In questa sezione viene creato un utente chiamato Britta Simon in Veritas Enterprise Vault.cloud SSO. Rivolgersi al team di supporto di Veritas Enterprise Vault.cloud SSO sul <a href="https://www.veritas.com/content/support/en_US/62696.html ">sito Web di supporto tecnico</a> per aggiungere gli utenti nella piattaforma Veritas Enterprise Vault.cloud SSO.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Veritas Enterprise Vault.cloud SSO.

![Assegna utente][200] 

**Per assegnare Britta Simon a Veritas Enterprise Vault.cloud SSO, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Veritas Enterprise Vault.cloud SSO**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_veritas-enterprise-vault-cloud-sso_50.png) 

3. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.

    ![Assegna utente][205]


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Veritas Enterprise Vault.cloud SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Veritas Enterprise Vault.cloud SSO.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-veritas-enterprise-vault-cloud-sso-tutorial/tutorial_general_205.png




<!--HONumber=Nov16_HO5-->


