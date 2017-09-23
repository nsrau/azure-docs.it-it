---
title: 'Esercitazione: Integrazione di Azure Active Directory con Workplace by Facebook | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 1590a66f215f0c093d24ff602c0ad951ba1e1eea
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Esercitazione: Integrazione di Azure Active Directory con Workplace by Facebook

Questa esercitazione descrive come integrare Workplace by Facebook con Azure Active Directory, ovvero Azure AD.

L'integrazione di Workplace by Facebook con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Workplace by Facebook
- È possibile abilitare gli utenti per l'accesso automatico a Workplace by Facebook (Single Sign-On) con i propri account di Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workplace by Facebook, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Una sottoscrizione di Workplace by Facebook abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Workplace by Facebook dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Aggiunta di Workplace by Facebook dalla raccolta
Per configurare l'integrazione di Workplace by Facebook in Azure AD, è necessario aggiungere Workplace by Facebook dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Workplace by Facebook dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Workplace by Facebook**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. Nel pannello dei risultati selezionare **Workplace by Facebook** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Workplace by Facebook usando un utente di test di nome "Britta Simon".

Affinché l'accesso Single Sign-On funzioni correttamente, Azure AD deve conoscere l'utente controparte di Workplace by Facebook corrispondente a un utente di Azure AD. In altre parole, si deve stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workplace by Facebook.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Workplace by Facebook.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Workplace by Facebook, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Configurazione della frequenza di riautenticazione](#configuring-reauthentication-frequency)**: per configurare Workplace per la richiesta di una verifica SAML.
3. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente di test di Workplace by Facebook](#creating-a-workplace-by-facebook-test-user)**: per avere una controparte di Britta Simon in Workplace by Facebook collegata alla rappresentazione dell'utente in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Workplace by Facebook.

**Per configurare l'accesso Single Sign-On di Azure AD con Workplace by Facebook, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Workplace by Facebook** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Nella sezione **Workplace by Facebook Domain and URLs** (URL e dominio Workplace by Facebook) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<instancename>.facebook.com`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.facebook.com/company/<instancename>`

    > [!NOTE] 
    > Questi non sono i valori reali. Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Workplace by Facebook](https://workplace.fb.com/faq/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_400.png)

6. Nella sezione **Workplace by Facebook Configuration** (Configurazione di Workplace by Facebook) fare clic su **Configure Workplace by Facebook** (Configurare Workplace by Facebook) per aprire la finestra **Configurare accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/config.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Workplace by Facebook come amministratore.
  
   > [!NOTE] 
   > Come parte del processo di autenticazione SAML, Workplace può usare stringhe di query di dimensioni massime di 2,5 kilobyte per passare i parametri ad Azure AD.

8. In **Company Dashboard** (Company Dashboard) passare alla scheda **Authentication** (Autenticazione).

9. In **SAML Authentication** (Autenticazione SAML) selezionare **SSO Only** (Solo SSO) dall'elenco a discesa.

10. Immettere i valori copiati dalla sezione **Workplace by Facebook Configuration** (Configurazione di Workplace by Facebook) del portale di Azure nei campi corrispondenti:

    *   Nella casella di testo **SAML URL** (URL SAML) incollare il valore di **URL servizio Single Sign-On** copiato dal portale di Azure.
    *   Nella **casella di testo SAML Issuer URL** (URL dell'autorità di certificazione SAML) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.
    *   In **SAML Logout Redirect (Optional)** (Reindirizzamento disconnessione SAML (facoltativo)) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.
    *   Aprire nel Blocco note il **certificato con codifica Base 64** scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **SAML Certificate** (Certificato SAML).

11. Potrebbe essere necessario inserire l'URL del pubblico, l'URL del destinatario e l'URL ACS (servizio consumer di asserzione) elencati nella sezione **SAML Configuration** (Configurazione SAML).

12. Scorrere fino alla fine della sezione e fare clic sul pulsante **Test SSO** (Testa SSO). Si ottiene una finestra popup visualizzata con la pagina di accesso di Azure AD. Immettere le credenziali come di consueto per l'autenticazione. 

    **Risoluzione dei problemi:** assicurarsi che l'indirizzo e-mail restituito da Azure AD sia lo stesso dell'account aziendale con cui si è connessi.

13. Dopo il completamento del test, scorrere fino alla fine della pagina e fare clic sul pulsante **Save** (Salva).

14. Tutti gli utenti che usano Workplace visualizzeranno ora una pagina di accesso di Azure AD per l'autenticazione.

15. **Reindirizzamento disconnessione SAML (facoltativo)** - 

    È possibile scegliere di configurare facoltativamente un URL di disconnessione SAML, che può essere usato per puntare alla pagina di disconnessione di Azure AD. Quando questa impostazione è abilitata e configurata, l'utente non sarà più indirizzato alla pagina di disconnessione di Workplace. Al contrario, l'utente verrà reindirizzato all'URL aggiunto nelle impostazioni di reindirizzamento di disconnessione SAML.


> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="configuring-reauthentication-frequency"></a>Configurazione della frequenza di riautenticazione

È possibile configurare Workplace per la richiesta di una verifica SAML ogni giorno, ogni 3 giorni, ogni settimana, ogni 2 settimane, ogni mese o mai.

> [!NOTE] 
>Il valore minimo per la verifica SAML nelle applicazioni per dispositivi mobili è impostato su una settimana.

È anche possibile forzare una reimpostazione SAML per tutti gli utenti che usano il pulsante: Require SAML authentication for all users now (Richiedi ora l'autenticazione SAML a tutti gli utenti).


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Creazione di un utente test di Workplace by Facebook

In questa sezione si crea un utente di nome Britta Simon in Workplace by Facebook. Workplace by Facebook supporta il provisioning JIT, abilitato per impostazione predefinita.

Non è necessaria alcuna azione dell'utente in questa sezione. Se un utente non esiste in Workplace by Facebook, si crea una nuova istanza quando si tenta di accedere a Workplace by Facebook.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto al cliente di Workplace by Facebook](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Workplace by Facebook.

![Assegna utente][200] 

**Per assegnare Britta Simon a Workplace by Facebook, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Workplace by Facebook**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configura provisioning utenti](active-directory-saas-workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_203.png


