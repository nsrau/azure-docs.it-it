---
title: 'Esercitazione: Integrazione di Azure Active Directory con FirmPlay - Employee Advocacy for Recruiting | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FirmPlay - Employee Advocacy for Recruiting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: e1381f2273ee961a4b72dede8cf8e017814ba909
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Esercitazione: Integrazione di Azure Active Directory con FirmPlay - Employee Advocacy for Recruiting

Questa esercitazione descrive come integrare FirmPlay - Employee Advocacy for Recruiting con Azure Active Directory (Azure AD).

L'integrazione di FirmPlay - Employee Advocacy for Recruiting con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a FirmPlay - Employee Advocacy for Recruiting
- È possibile abilitare gli utenti per l'accesso automatico a FirmPlay - Employee Advocacy for Recruiting (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con FirmPlay - Employee Advocacy for Recruiting, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di FirmPlay - Employee Advocacy for Recruiting abilitata per l'accesso Single-Sign On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di FirmPlay - Employee Advocacy for Recruiting dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Aggiunta di FirmPlay - Employee Advocacy for Recruiting dalla raccolta
Per configurare l'integrazione di FirmPlay - Employee Advocacy for Recruiting in Azure AD, è necessario aggiungere FirmPlay - Employee Advocacy for Recruiting dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere FirmPlay - Employee Advocacy for Recruiting dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **FirmPlay - Employee Advocacy for Recruiting**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. Nel pannello dei risultati selezionare **FirmPlay - Employee Advocacy for Recruiting** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con FirmPlay - Employee Advocacy for Recruiting con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di FirmPlay - Employee Advocacy for Recruiting che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FirmPlay - Employee Advocacy for Recruiting.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in FirmPlay - Employee Advocacy for Recruiting.

Per configurare e testare l'accesso Single Sign-On di Azure AD con FirmPlay - Employee Advocacy for Recruiting, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di FirmPlay - Employee Advocacy for Recruiting](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**: per avere una controparte di Britta Simon in FirmPlay - Employee Advocacy for Recruiting collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione FirmPlay - Employee Advocacy for Recruiting.

**Per configurare l'accesso Single Sign-On di Azure AD con FirmPlay - Employee Advocacy for Recruiting, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **FirmPlay - Employee Advocacy for Recruiting** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. Nella casella di testo **URL di accesso** della sezione **URL e dominio di FirmPlay - Employee Advocacy for Recruiting** digitare un URL usando il modello seguente: `https://<your-subdomain>.firmplay.com/`

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Si noti che questo non è il valore reale. È necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto di FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. Nella sezione **Certificato di firma SAML** selezionare **Make new certificate active** (Rendi attivo il nuovo certificato) e fare clic sul pulsante **Salva**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. Nella finestra popup **Rollover certificate** (Certificato di rollover) fare clic su **OK**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer. 

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. Nella sezione **Configurazione di FirmPlay - Employee Advocacy for Recruiting** fare clic su **Configura FirmPlay - Employee Advocacy for Recruiting** per aprire la finestra di dialogo **Configura accesso**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Per configurare l'accesso SSO per l'applicazione, contattare il [team di supporto di FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) e fornire gli elementi seguenti: 

    •  **File del certificato** scaricato

    •  **URL servizio Single Sign-On SAML**

    •  **ID entità SAML**

    •  **URL di disconnessione**
  

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Creazione di un utente di test di FirmPlay - Employee Advocacy for Recruiting

In questa sezione si crea un utente di nome Britta Simon in FirmPlay - Employee Advocacy for Recruiting. Per aggiungere gli utenti nella piattaforma FirmPlay - Employee Advocacy for Recruiting, contattare il [team di supporto di FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com).


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a FirmPlay - Employee Advocacy for Recruiting.

![Assegna utente][200] 

**Per assegnare Britta Simon a FirmPlay - Employee Advocacy for Recruiting, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **FirmPlay - Employee Advocacy for Recruiting**.

    ![Configure Single Sign-On](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro FirmPlay - Employee Advocacy for Recruiting nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione FirmPlay - Employee Advocacy for Recruiting.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png