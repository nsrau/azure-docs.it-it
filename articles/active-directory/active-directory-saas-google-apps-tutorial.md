---
title: 'Esercitazione: Integrazione di Azure Active Directory con Google Apps | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Google Apps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: f3b0d48534113dea152aba632e59d03ed78db301
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Esercitazione: Integrazione di Azure Active Directory con Google Apps

Questa esercitazione descrive come integrare Google Apps con Azure Active Directory, ovvero Azure AD.

L'integrazione di Google Apps con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi accede a Google Apps.
- È possibile abilitare gli utenti per l'accesso automatico a Google Apps, ovvero l'accesso Single Sign-On, con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Google Apps sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Google Apps abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Domande frequenti
1. **D: I dispositivi Chromebooks e altri dispositivi Chrome sono compatibili con Single Sign-On di Azure AD?**
   
    R: Sì, gli utenti possono accedere ai dispositivi Chromebook con le credenziali di Azure AD. Per informazioni sui motivi per cui agli utenti può essere richiesto di immettere le credenziali due volte, vedere questo [articolo del supporto tecnico di Google Apps](https://support.google.com/chrome/a/answer/6060880) .

2. **D: Se si abilita il Single Sign-On, gli utenti potranno usare le credenziali di Azure AD per accedere a qualsiasi prodotto di Google, ad esempio Google Classroom, GMail, Google Drive, YouTube e così via?**
   
    R: Sì, a seconda delle [Google Apps](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) che si sceglie di abilitare o disabilitare per la propria organizzazione.

3. **D: È possibile abilitare il Single Sign-On solo per un sottoinsieme di utenti di Google Apps?**
   
    R: No, l'attivazione del Single Sign-On richiede immediatamente a tutti gli utenti di Google Apps di autenticarsi con le proprie credenziali di Azure AD. Poiché Google Apps non supporta più provider di identità, il provider di identità per l'ambiente di Google Apps può essere AD Azure o Google, ma non entrambi contemporaneamente.

4. **D: Se un utente ha eseguito l'accesso tramite Windows, viene autenticano automaticamente in Google Apps senza che venga richiesta una password?**
   
    R: Sono disponibili due opzioni per l'abilitazione di questo scenario. Nel primo caso gli utenti possono accedere ai dispositivi Windows 10 tramite [Aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md). In alternativa, gli utenti possono accedere ai dispositivi Windows appartenenti a un dominio di Active Directory locale abilitato per il Single Sign-On in Azure AD tramite una distribuzione di [Active Directory Federation Services (AD FS)](active-directory-aadconnect-user-signin.md) . Per entrambe le opzioni è necessario eseguire la procedura descritta nell'esercitazione seguente per abilitare il Single Sign-On tra Azure AD e Google Apps.

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Google Apps dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-google-apps-from-the-gallery"></a>Aggiunta di Google Apps dalla raccolta
Per configurare l'integrazione di Google Apps in Azure AD, è necessario aggiungere Google Apps dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Google Apps dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Google Apps**, selezionare **Google Apps** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Google Apps nell'elenco dei risultati](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Google Apps usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Google Apps che corrisponde all'utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra l'utente di Azure AD e l'utente correlato in Google Apps.

Per stabilire la relazione di collegamento, in Google Apps assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Google Apps, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Google Apps](#create-a-google-apps-test-user)**: per avere una controparte di Britta Simon in Google Apps collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Google Apps.

**Per configurare l'accesso Single Sign-On di Azure AD con Google Apps, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Google Apps** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. Nella sezione **Google Apps Domain and URLs** (URL e dominio di Google Apps) seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://mail.google.com/a/<yourdomain.com>`

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:

    | |
    |--|
    | `google.com`|
    | `http://google.com`|
    | `google.com/<yourdomain.com>`|
    | `http://google.com/a/<yourdomain.com>`|
       
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Google Apps](https://www.google.com/contact/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

6. Nella sezione **Google Apps Configuration** (Configurazione di Google Apps) fare clic su **Configure Google Apps** (Configura Google Apps) per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'URL di modifica password e l'URL del servizio Single Sign-On SAML** dalla sezione di **Riferimento rapido**.

    ![Configurazione di Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

7. Aprire una nuova scheda nel browser e accedere a [Console di amministrazione di Google Apps](http://admin.google.com/) utilizzando l'account amministratore.

8. Fare clic su **Security**. Se non viene visualizzato il collegamento, può essere nascosto sotto il menu **More Controls** nella parte inferiore della schermata.
   
    ![Fare clic su sicurezza.][10]

9. Nella pagina **Security** (Sicurezza) fare clic su **Set up single sign-on (SSO)** (Configurazione Single Sign-On (SSO)).
   
    ![Fare clic su SSO.][11]

10. Eseguire le seguenti modifiche di configurazione:
   
    ![Configurare SSL][12]
   
    a. Selezionare **Setup SSO with third party identity provider** (Configurare l'accesso SSO con un provider di terze parti).

    b. Nel campo **Sign-in page URL** (URL pagina di accesso) di Google Apps incollare il valore dell'**URL del servizio Single Sign-On** copiato dal portale di Azure.

    c. Nel campo **Sign-out page URL** (URL pagina di disconnessione) di Google Apps incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure. 

    d. Nel campo **Change password URL** (URL di modifica della password) di Google Apps incollare il valore dell'**URL di modifica della password** copiato dal portale di Azure. 

    e. In Google Apps per il **certificato di verifica**, caricare il certificato che è stato scaricato dal portale di Azure.

    f. Fare clic su **Salva modifiche**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-google-apps-test-user"></a>Creare un utente di test di Google Apps

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Google Apps Software. Google Apps supporta il provisioning automatico abilitato per impostazione predefinita. Non è necessaria alcuna azione dell'utente in questa sezione. Se un utente non esiste in Google Apps, ne viene creato uno nuovo quando si tenta di accedere a Google Apps Software.

>[!NOTE] 
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Google Apps.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Google Apps, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Google Apps**.

    ![Collegamento Google Apps nell'elenco delle applicazioni](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Google Apps nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Google Apps.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

