---
title: 'Esercitazione: Integrazione di Azure Active Directory con RFPIO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 26a8bb17dad5a01b401ce7f9b484f09822825cbf
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Esercitazione: Integrazione di Azure Active Directory con RFPIO

Questa esercitazione descrive come integrare RFPIO con Azure Active Directory (Azure AD).

L'integrazione di RFPIO con Azure AD offre i vantaggi seguenti:

- In Azure AD è possibile controllare chi può accedere a RFPIO.
- È possibile abilitare gli utenti per l'accesso automatico a RFPIO (Single Sign-On) con i propri account Azure AD.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con RFPIO, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure AD.
- Sottoscrizione RFPIO abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di RFPIO dalla raccolta.
2. Configurazione e test dell'accesso Single Sign-On di Azure AD.

## <a name="add-rfpio-from-the-gallery"></a>Aggiungere RFPIO dalla raccolta
Per configurare l'integrazione di RFPIO in Azure AD, è necessario aggiungere RFPIO dalla raccolta al proprio elenco di app SaaS gestite.

### <a name="to-add-rfpio-from-the-gallery"></a>Per aggiungere RFPIO dalla raccolta

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare l'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Per aggiungere una nuova applicazione selezionare il pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **RFPIO**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. Nel pannello dei risultati selezionare **RFPIO** e quindi selezionare il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con RFPIO in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è la relazione esistente fra un utente di Azure AD e l'utente corrispondente in RFPIO. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RFPIO.

Per stabilire la relazione di collegamento, in RFPIO assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con RFPIO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di RFPIO](#creating-a-rfpio-test-user)**: per avere una controparte di Britta Simon in RFPIO collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#testing-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione RFPIO.

**Per configurare l'accesso Single Sign-On di Azure AD con RFPIO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **RFPIO** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. Nella sezione **URL e dominio RFPIO**, se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://www.rfpio.com`

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Selezionare **Mostra impostazioni URL avanzate**

    c. Nella casella di testo **Stato dell'inoltro** immettere un valore stringa. Per ottenere tale valore, contattare il [team di supporto di RFPIO](https://www.rfpio.com/contact/). 

4. Selezionare **Mostra impostazioni URL avanzate** se si desidera configurare l'applicazione in modalità avviata da **SP**: 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    Nella casella di testo **URL di accesso** digitare l'URL: `https://www.app.rfpio.com`

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. In un'altra finestra del Web browser accedere al sito Web **RFPIO** come amministratore.

8. Fare clic sull'elenco a discesa a sinistra nella parte inferiore.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. Fare clic su di **Impostazioni organizzazione**. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. Fare clic sulla sezione relativa a **FUNZIONALITÀ E INTEGRAZIONE**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. In **SAML Configurazione SSO** fare clic su **Modifica**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. In questa sezione eseguire le seguenti operazioni:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. Copiare il contenuto del **file XML di metadati scaricato** e incollarlo nella casella della **configurazione identità**.

    > [!NOTE]
    >Per copiare il contenuto del file di **metadata XML** scaricato usare **Notepad++** o un **editor XML** appropriato. 

    b. Fare clic su **Convalida**.

    c. Dopo aver fatto clic su **Convalida**, impostare **SAML (Abilitato)** su attivato.

    d. Fare clic su **Submit**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-rfpio-test-user"></a>Creare un utente di test di RFPIO

Per consentire agli utenti di Azure AD di accedere a RFPIO, è necessario eseguirne il provisioning in RFPIO.  
Nel caso di RFPIO, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di RFPIO come amministratore.

2. Fare clic sull'elenco a discesa a sinistra nella parte inferiore.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. Fare clic su di **Impostazioni organizzazione**. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. Fare clic su **MEMBRI DEL TEAM**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. Fare clic su **AGGIUNGI MEMBRI**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. Nella sezione **Aggiungere nuovi membri**. eseguire le seguenti operazioni:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. Immettere l'**indirizzo di posta elettronica** nella casella che richiede **un indirizzo di posta elettronica per riga**.

    b. Selezionare il **ruolo** in base alle esigenze.

    c. Fare clic su **AGGIUNGI MEMBRI**.
        
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RFPIO.

![Assegna utente][200] 

**Per assegnare Britta Simon a RFPIO seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **RFPIO**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro RFPIO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RFPIO.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png


