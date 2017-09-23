---
title: 'Esercitazione: Integrazione di Azure Active Directory con Humanity | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Humanity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 327cc1e3d0836e79376e0a3cd5a4422a967f5943
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Esercitazione: integrazione di Azure Active Directory con Humanity

Questa esercitazione descrive come integrare Humanity con Azure Active Directory (Azure AD).

L'integrazione di Humanity con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Humanity
- È possibile abilitare gli utenti per l'accesso automatico a Humanity (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Humanity, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Humanity abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Humanity dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-humanity-from-the-gallery"></a>Aggiunta di Humanity dalla raccolta
Per configurare l'integrazione di Humanity in Azure AD, è necessario aggiungere Humanity dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Humanity dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Humanity**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_search.png)

5. Nel pannello dei risultati selezionare **Humanity** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Humanity usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Humanity corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Humanity.

Per stabilire la relazione di collegamento, in Humanity assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Humanity, è necessario completare le procedure di base seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Humanity](#creating-a-humanity-test-user)**: per avere una controparte di Britta Simon in Humanity collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Humanity.

**Per configurare l'accesso Single Sign-On di Azure AD con Humanity, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Humanity** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_samlbase.png)

3. Nella sezione **URL e dominio Humanity** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://company.humanity.com/includes/saml/`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://company.humanity.com/app/`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di Humanity](https://www.humanity.com/support/). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Humanity** fare clic su **Configura Humanity** per aprire la finestra **Configura accesso**. Copiare l'**URL del servizio Single Sign-On SAML e l'URL di disconnessione** dalla **sezione di riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di **Humanity** come amministratore.

8. Nel menu in alto fare clic su **Admin**.
   
    ![Amministratore](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "Amministratore")

9. In **Integrazione** fare clic su **Single Sign-On**.
   
    ![Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/iC786620.png "Single Sign-On")

10. Nella sezione **Single Sign-On** , eseguire la procedura seguente:
   
    ![Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/iC786905.png "Single Sign-On")
   
    a. Selezionare **Abilitato SAML**.

    b. Selezionare **Allow Password Login** (Consenti accesso tramite password).

    c. Incollare il valore dell'**URL del servizio Single Sign-On SAML** nella casella di testo **SAML Issuer URL** (URL autorità di certificazione SAML).

    d. Incollare il valore dell'**URL di disconnessione** nella casella di testo **Remote Logout URL** (URL disconnessione remota).
   
    e. Aprire il certificato con codifica base 64 nel blocco note, copiarne il contenuto negli appunti e incollarlo nella casella di testo **Certificato X.509** .

11. Fare clic su **Salva impostazioni**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-humanity-test-user"></a>Creazione di un utente di test di Humanity

Per consentire agli utenti di Azure AD di accedere a Humanity, è necessario effettuarne il provisioning in Humanity. Nel caso di Humanity, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Humanity** come amministratore.

2. Fare clic su **Admin**.
   
    ![Amministratore](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "Amministratore")

3. Fare clic su **Personale**.
   
    ![Personale](./media/active-directory-saas-shiftplanning-tutorial/ic786623.png "Personale")

4. In **Actions** (Azioni) fare clic su **Add Employees** (Aggiungi dipendenti).
   
    ![Aggiungi dipendenti](./media/active-directory-saas-shiftplanning-tutorial/iC786624.png "Aggiungi dipendenti")

5. Nella sezione **Aggiungi dipendenti** eseguire la procedura seguente:
   
    ![Salvare i dipendenti](./media/active-directory-saas-shiftplanning-tutorial/iC786625.png "Salvare i dipendenti")
   
    a. Digitare il **nome**, il **cognome** e l'**indirizzo di posta elettronica** di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.

    b. Fare clic su **Salva dipendenti**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Humanity per eseguire il provisioning degli account utente di Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Humanity.

![Assegna utente][200] 

**Per assegnare Britta Simon a Humanity, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Humanity**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Humanity nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Humanity.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_203.png


