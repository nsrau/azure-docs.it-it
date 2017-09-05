---
title: 'Esercitazione: Integrazione di Azure Active Directory con Workplace by Facebook | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 27e62a00832484667117d8718db9f2fc05e2f4e2
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Esercitazione: Integrazione di Azure Active Directory con Workplace by Facebook

Questa esercitazione descrive come integrare Workplace by Facebook con Azure Active Directory, ovvero Azure AD.

L'integrazione di Workplace by Facebook con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Workplace by Facebook.
- È possibile abilitare gli utenti per l'accesso automatico a Workplace by Facebook (single sign-on) con i propri account di Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workplace by Facebook, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Una sottoscrizione di Workplace by Facebook abilitata per l'accesso single sign-on (SSO)

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene testato l'accesso SSO di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Workplace by Facebook dalla raccolta.
2. Configurare e testare l'accesso Single Sign-On di Azure AD.

## <a name="add-workplace-by-facebook-from-the-gallery"></a>Aggiunta di Workplace by Facebook dalla raccolta
Per configurare l'integrazione di Workplace by Facebook in Azure AD, aggiungere Workplace by Facebook dalla raccolta al proprio elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Esplorare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, fare clic su **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Workplace by Facebook**, selezionare **Workplace by Facebook** dai risultati. Selezionare quindi **Aggiungi**, per aggiungere l'applicazione.

    ![Workplace by Facebook nell'elenco risultati](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con Workplace by Facebook usando un utente di test di nome "Britta Simon".

Affinché l'accesso SSO funzioni correttamente, Azure AD deve conoscere l'utente controparte di Workplace by Facebook corrispondente a un utente di Azure AD. In altre parole, si deve stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workplace by Facebook.

Stabilire questa relazione assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Workplace by Facebook.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso SSO di Azure AD nel portale di gestione di Azure e viene configurato l'accesso SSO nell'applicazione WORKS MOBILE.

1. Nella pagina di integrazione dell'applicazione **Workplace by Facebook** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso SSO.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Nella sezione **Workplace by Facebook Domain and URLs** (URL e dominio Workplace by Facebook) eseguire i passaggi riportati di seguito:

    a. Nella casella di testo **URL accesso** digitare un URL che utilizza il modello seguente: `https://<company subdomain>.facebook.com`

    b. Nella casella di testo **Identificatore** digitare l'URL che utilizza il modello seguente:`https://www.facebook.com/company/<scim company id>`

    > [!NOTE]
    > Questi valori sono solo un esempio. Aggiornare questi valori con URL di accesso e identificatore effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Workplace by Facebook](https://workplace.fb.com/faq/). 

4. Nella sezione **Certificato di firma SAML** selezionare **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Selezionare **Salva**.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. Nella sezione **Workplace by Facebook Configuration** (Configurazione di Workplace by Facebook) selezionare **Configure Workplace by Facebook** (Configurare Workplace by Facebook) per aprire la finestra **Configurare accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido**.

    ![Configurazione di Workplace by Facebook](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Workplace by Facebook come amministratore.
  
   > [!NOTE] 
   > Come parte del processo di autenticazione SAML, Workplace può usare stringhe di query di dimensioni massime di 2,5 kilobyte per passare i parametri ad Azure AD.

8. In **Company Dashboard** (Company Dashboard) passare alla scheda **Authentication** (Autenticazione).

9. In **SAML Authentication** (Autenticazione SAML) selezionare **SSO Only** (Solo SSO) dall'elenco a discesa.

10. Immettere i valori copiati dalla sezione **Workplace by Facebook Configuration** (Configurazione di Workplace by Facebook) del portale di Azure nei campi corrispondenti:

    *   Nella casella di testo **SAML URL** (URL SAML) incollare il valore di **URL servizio Single Sign-On** copiato dal portale di Azure.
    *   Nella **casella di testo SAML Issuer URL** (URL dell'autorità di certificazione SAML) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.
    *   In **SAML Logout Redirect (Optional)** (Reindirizzamento disconnessione SAML (facoltativo)) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.
    *   Aprire il **certificato con codifica base 64** nel blocco note, scaricato dal portale di Azure. Copiare il contenuto negli Appunti e quindi incollarlo nella casella di testo del **certificato SAML**.

11. Può essere necessario inserire l'URL del pubblico, l'URL del destinatario e l'URL ACS (servizio consumer di asserzione) elencati nella sezione **SAML Configuration** (Configurazione SAML).

12. Scorrere fino alla fine della sezione e selezionare **Test SSO** (Testa SSO). Viene visualizzata una finestra popup con la pagina di accesso di Azure AD. Immettere le credenziali come di consueto per l'autenticazione. Assicurarsi che l'indirizzo e-mail restituito da Azure AD sia lo stesso dell'account aziendale con cui si è connessi.

13. Se il test è stato completato, scorrere fino alla fine della pagina e selezionare **Salva**.

14. Chiunque utilizzi all'area di lavoro visualizza ora la pagina di accesso ad Azure AD per l'autenticazione.

È possibile scegliere di configurare un URL di disconnessione SAML, che può essere usato per puntare alla pagina di disconnessione di Azure AD. Quando questa impostazione è abilitata e configurata, l'utente non viene più indirizzato alla pagina di disconnessione di Workplace. Al contrario, l'utente viene reindirizzato all'URL aggiunto nelle impostazioni di reindirizzamento di disconnessione SAML.


> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente selezionare scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="configure-reauthentication-frequency"></a>Configurazione della frequenza di riautenticazione

È possibile configurare Workplace per la richiesta di una verifica SAML ogni giorno, ogni 3 giorni, ogni settimana, ogni 2 settimane, ogni mese o mai.

> [!NOTE] 
>Il valore minimo per la verifica SAML nelle applicazioni per dispositivi mobili è impostato su una settimana.

È inoltre possibile forzare una reimpostazione SAML per tutti gli utenti. A tale scopo, utilizzare il pulsante **Richiedi autenticazione SAML per tutti gli utenti ora**.


### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

1. Nel **portale di Azure** fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. Per aprire la finestra di dialogo **Utente**, fare clic su **Aggiungi**.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo e-mail** di BrittaSimon.

    c. Selezionare **Mostra password**e annotarla.

    d. Selezionare **Crea**.
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Creare un utente test di Workplace by Facebook

In questa sezione si crea un utente di nome Britta Simon in Workplace by Facebook. Workplace by Facebook supporta il provisioning JIT, abilitato per impostazione predefinita.

Non è necessaria alcuna azione dell'utente in questa sezione. Se un utente non esiste in Workplace by Facebook, si crea una nuova istanza quando si tenta di accedere a Workplace by Facebook.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto al cliente di Workplace by Facebook](https://workplace.fb.com/faq/).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso SSO di Azure concedendo l'accesso a Workplace by Facebook.

![Assegnare utenti][200] 

1. Nel portale di Azure, aprire la visualizzazione delle applicazioni. Passare alla visualizzazione directory, accedere ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

2. Nell'elenco di applicazioni selezionare **Workplace by Facebook**.

    ![Collegamento di Workplace by Facebook nell'elenco delle applicazioni](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202] 

4. Selezionare **Aggiungi**. Quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco utenti.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic su **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Per testare le impostazioni di SSO, aprire il pannello di accesso.
Per altre informazioni, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


## <a name="next-steps"></a>Passaggi successivi

* Vedere l'[Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md).
* Leggere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory?](active-directory-appssoaccess-whatis.md).
* Altre informazioni su come [Configurare il provisioning utente](active-directory-saas-facebook-at-work-provisioning-tutorial.md).



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png


