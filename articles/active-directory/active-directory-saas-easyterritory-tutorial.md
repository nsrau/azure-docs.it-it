---
title: 'Esercitazione: integrazione di Azure Active Directory con EasyTerritory | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e EasyTerritory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d29b362d-e986-4f67-8ff2-e158e49353aa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 64131f85cceb463bdd91cec40c2f272ece773691
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-easyterritory"></a>Esercitazione: integrazione di Azure Active Directory con EasyTerritory

Questa esercitazione descrive come integrare EasyTerritory con Azure Active Directory (Azure AD).

L'integrazione di EasyTerritory con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a EasyTerritory
- È possibile abilitare gli utenti per l'accesso automatico a EasyTerritory (SSO) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con EasyTerritory, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di EasyTerritory abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
>
>

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile ottenere una versione di valutazione di [un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene testato l'accesso SSO di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di EasyTerritory dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="adding-easyterritory-from-the-gallery"></a>Aggiunta di EasyTerritory dalla raccolta
Per configurare l'integrazione di EasyTerritory in Azure AD, è necessario aggiungere EasyTerritory dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere EasyTerritory dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **EasyTerritory**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_001.png)

5. Nel pannello dei risultati selezionare **EasyTerritory** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con EasyTerritory con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di EasyTerritory che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in EasyTerritory.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in EasyTerritory.

Per configurare e testare l'accesso SSO di Azure AD con EasyTerritory, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di EasyTerritory](#creating-an-easyterritory-test-user)** per avere una controparte di Britta Simon in EasyTerritory collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso SSO nell'applicazione EasyTerritory.

**Per configurare l'accesso SSO di Azure AD con EasyTerritory, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **EasyTerritory** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso SSO.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_01.png)

3. Nella sezione **URL e dominio EasyTerritory**, se si vuole configurare l'applicazione in **modalità avviata da IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_02.png)
   1. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://apps.easyterritory.com/<tenant id>/DEV/`
   2. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://apps.easyterritory.com/<tenant id>/DEV/AuthServices/Acs`
    
4. Per configurare l'applicazione in **modalità avviata da SP**, nella sezione **URL e dominio EasyTerritory** seguire questa procedura:
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_03.png)
  1. Fare clic sull'opzione **Mostra impostazioni URL avanzate**.
  2. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.easyterritory.com/`

    >[!NOTE] 
    >Questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di EasyTerritory](mailto:sales@easyterritory.com).

5. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_04.png)     

6. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_300.png)

7. Nella sezione **Certificato di firma SAML** selezionare **Make new certificate active** (Rendi attivo il nuovo certificato) e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_05.png)

8. Nella finestra popup **Rollover certificate** (Certificato di rollover) fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_400.png)

9. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_06.png) 

10. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il [team di supporto di EasyTerritory](mailto:sales@easyterritory.com) e fornire i **metadati** scaricati. 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_04.png) 
 1. Nella casella di testo **Nome** digitare **BrittaSimon**.
 2. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.
 3. Selezionare **Mostra password** e prendere nota del valore della **Password**.
 4. Fare clic su **Crea**. 

### <a name="create-an-easyterritory-test-user"></a>Creare un utente test di EasyTerritory

In questa sezione viene creato un utente chiamato Britta Simon in EasyTerritory. Collaborare con il [team di supporto di EasyTerritory](mailto:sales@easyterritory.com) per aggiungere gli utenti alla piattaforma EasyTerritory.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso SSO di Azure, concedendole l'accesso a EasyTerritory.

![Assegna utente][200] 

**Per assegnare Britta Simon a EasyTerritory, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **EasyTerritory**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro EasyTerritory nel pannello di accesso, si accederà automaticamente all'applicazione EasyTerritory.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_203.png
