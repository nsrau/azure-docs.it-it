---
title: 'Esercitazione: Integrazione di Azure Active Directory con T&E Express| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e T&E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 869e5284c71904fcc817ceee0f39d94fab1bc6f3
ms.contentlocale: it-it
ms.lasthandoff: 04/07/2017

---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Esercitazione: Integrazione di Azure Active Directory con T&E Express

Questa esercitazione illustra come integrare T&E Express con Azure Active Directory (Azure AD).

L'integrazione di T&E Express con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a T&E Express
- È possibile abilitare gli utenti per l'accesso automatico a T&E Express (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con T&E Express, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di T&E Express abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di T&E Express dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-te-express-from-the-gallery"></a>Aggiungere T&E Express dalla raccolta
Per configurare l'integrazione di T&E Express in Azure AD, è necessario aggiungere T&E Express dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere T&E Express dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Digitare **T&E Express** nella casella di ricerca.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_search.png)

5. Nel pannello dei risultati selezionare **T&E Express** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con T&E Express usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente controparte di T&E Express che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in T&E Express.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** (Nome utente) in T&E Express.

Per configurare e testare l'accesso Single Sign-On di Azure AD con T&E Express, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di T&E Express](#creating-a-te-express-test-user)** : per avere una controparte di Britta Simon in T&E Express collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione T&E Express.

**Per configurare Single Sign-On di Azure AD con T&E Express, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **T&E Express** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

3. Nella sezione **URL e dominio T&E Express** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. Nella casella di testo **Identificatore** digitare il valore `https://<domain>.tyeexpress.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di risposta effettivi. In questo caso è consigliabile usare in Identificatore il valore univoco della stringa. Per ottenere questi valori, contattare il [team di supporto di T&E Express](http://www.tyeexpress.com/contacto.aspx).

5. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_400.png)

8. Per configurare l'accesso Single Sign-On sul lato **T&E Express**, accedere all'applicazione T&E Express senza SAML Single Sign-On usando le credenziali di amministratore.

9. Nella scheda **Admin** fare clic su **SAML domain** (Dominio SAML) per aprire la pagina delle impostazioni di SAML.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tye-SAML.png)

10. Spostare il selettore dell'opzione **Activar** (Attivare) da **NO** a **SI**. Nella casella di testo **Identity Provider Metadata** (Metadati provider di identità) incollare il file XML dei metadati scaricato in precedenza dal portale di Azure.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tyeAdmin.png)

11. Fare clic sul pulsante **Guardar** (Salva) per salvare le impostazioni. 


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-te-express-test-user"></a>Creare un utente test di T&E Express

Per consentire agli utenti di Azure AD di accedere a T&E Express, è necessario eseguirne il provisioning in T&E Express.  
Nel caso di T&E Express, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di T&E Express come amministratore.

2. Sotto il tag Admin fare clic su Users (Utenti) per aprire la pagina master degli utenti.

    ![Aggiungere un dipendente](./media/active-directory-saas-tyeexpress-tutorial/tye-adminusers.png)

3. Nella home page fare clic su **+** per aggiungere gli utenti.

    ![Aggiungere un dipendente](./media/active-directory-saas-tyeexpress-tutorial/tye-usershome.png)

4. Immettere tutti i dettagli obbligatori richiesti nel modulo e fare clic sul pulsante di salvataggio per salvare i dettagli.

    ![Aggiungere un dipendente](./media/active-directory-saas-tyeexpress-tutorial/tye-usersadd.png)

    ![Aggiungere un dipendente](./media/active-directory-saas-tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a T&E Express per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200] 

**Per assegnare Britta Simon a T&E Express, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione con le applicazioni e quindi passare alla visualizzazione con le directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Selezionare **T&E Express** dall'elenco delle applicazioni.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro T&E Express nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione T&E Express.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_203.png


