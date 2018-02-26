---
title: 'Esercitazione: Integrazione di Azure Active Directory con Central Desktop | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Central Desktop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 94c67bef7a0c6ba60fc9c7a60c79a23bf7984fb1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Esercitazione: Integrazione di Azure Active Directory con Central Desktop

Questa esercitazione descrive come integrare Central Desktop con Azure Active Directory (Azure AD).

L'integrazione di Central Desktop con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Central Desktop.
- È possibile abilitare gli utenti per l'accesso automatico a Central Desktop con gli account Azure AD personali.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre dettagli sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Central Desktop, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Central Desktop abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è già disponibile un ambiente di prova di Azure AD, ottenere una [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Central Desktop dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-central-desktop-from-the-gallery"></a>Aggiungere Central Desktop dalla raccolta
Per configurare l'integrazione di Central Desktop in Azure AD, è necessario aggiungere Central Desktop dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Central Desktop dalla raccolta, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere nuove applicazioni, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Central Desktop**. Selezionare **Central Desktop** nel pannello dei risultati e quindi selezionare il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Central Desktop nell'elenco dei risultati](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione si configura e si testa l'accesso Single Sign-On di Azure AD con Central Desktop in base a un utente test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Central Desktop corrispondente a un utente di Azure AD. In altre parole, deve essere stabilito un collegamento tra un utente di Azure AD e un utente correlato in Central Desktop.

In Central Desktop assegnare a **Username** (Nome utente) lo stesso valore del **nome utente** in Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Central Desktop, è necessario completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Central Desktop](#create-a-central-desktop-test-user) per avere una controparte di Britta Simon in Central Desktop collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure e si configura l'accesso Single Sign-On nell'applicazione Central Desktop.

**Per configurare l'accesso Single Sign-On di Azure AD con Central Desktop, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Central Desktop** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Per abilitare l'accesso Single Sign-On, nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** nell'elenco a discesa **Modalità**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. Nella sezione **URL e dominio Central Desktop** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. Nella casella **URL di accesso** digitare un URL usando il modello seguente: `https://<companyname>.centraldesktop.com`

    b. Nella casella **Identificatore** digitare un URL usando il modello seguente:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. Nella casella **URL di risposta** digitare un URL nel formato seguente: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Central Desktop](https://imeetcentral.com/contact-us). 

4. Nella sezione **Certificato di firma SAML** selezionare **Certificato** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Fare clic sul pulsante **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. Nella sezione **Configurazione di Central Desktop** fare clic su **Configura Central Desktop** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido**.

    ![Configurazione di Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Accedere al tenant di **Central Desktop**.

8. Passare a **Impostazioni**. Selezionare **Advanced** (Avanzate) e quindi selezionare **Single Sign On** (Single Sign-On).

    ![Installazione - Avanzate](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "Installazione - Avanzate")

9. Nella pagina **Single Sign-On Settings** (Impostazioni Single Sign-On) seguire questa procedura:

    ![Impostazioni di Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "Impostazioni di Single Sign-On")
    
    a. Selezionare **Enable SAML v2 Single Sign On**.
    
    b. Nella casella **SSO URL** (URL SSO) incollare il valore **ID di entità SAML** copiato dal portale di Azure.
    
    c. Nella casella **SSO Login URL** (URL di accesso SSO) incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.
    
    d. Nella casella **SSO Logout URL** (URL disconnessione SSO) incollare il valore **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.

10. Nella sezione **Message Signature Verification Method** (Metodo di verifica della firma del messaggio) seguire questa procedura:

    ![Message Signature Verification Method (Metodo di verifica della firma del messaggio)](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "Message Signature Verification Method (Metodo di verifica della firma del messaggio)") Selezionare **Certificate**.
    
    b. Nell'elenco **SSO Certificate** (Certificato SSO) selezionare **RSH SHA256**.
    
    c. Aprire il certificato scaricato nel Blocco note. Copiare quindi il contenuto del certificato e incollarlo nel campo **SSO Certificate** (Certificato SSO).
        
    d. Selezionare **Display a link to your SAMLv2 login page**.
    
    e. Selezionare **Aggiorna**.

> [!TIP]
> Un riepilogo di queste istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente selezionare la scheda **Single Sign-On** e quindi accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata, vedere la [documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Per visualizzare un elenco di utenti, passare a **Utenti e gruppi**. Selezionare quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-central-desktop-test-user"></a>Creare un utente di test di Central Desktop

Per consentire agli utenti di Azure AD di accedere, è necessario eseguirne il provisioning nell'applicazione Central Desktop. Questa sezione descrive come creare account utente di Azure AD in Central Desktop.

> [!NOTE]
> Per effettuare il provisioning degli account utente di Azure AD, è possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Central Desktop.

**Per eseguire il provisioning degli account utente in Central Desktop:**

1. Accedere al tenant di Central Desktop.

2. Passare a **People** > **Internal Members** (Persone > Membri interni).

3. Selezionare **Add Internal Members** (Aggiungi membri interni).

    ![Persone](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "Persone")
    
4. Nella casella **Email Address of New Members** (Indirizzo posta elettronica nuovi membri) digitare un account Azure AD di cui si vuole effettuare il provisioning e quindi selezionare **Next** (Avanti).

    ![Email Addresses of New Members (Indirizzi di posta elettronica dei nuovi membri)](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "Email Addresses of New Members (Indirizzi di posta elettronica dei nuovi membri)")

5. Selezionare **Add Internal member(s)** (Aggiungi membri interni).

    ![Aggiungere un membro interno](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "Aggiungere un membro interno")
   
   >[!NOTE]
   >Gli utenti aggiunti ricevono un messaggio di posta elettronica che include un collegamento di conferma per attivare gli account.
   
### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione l'utente Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Central Desktop.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Central Desktop, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione applicazioni. Passare alla visualizzazione directory e quindi ad **Applicazioni aziendali**.

2. Selezionare **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

2. Nell'elenco delle applicazioni selezionare **Central Desktop**.

    ![Collegamento di Central Desktop nell'elenco delle applicazioni](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Central Desktop nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Central Desktop.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png

