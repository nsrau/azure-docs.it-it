---
title: 'Esercitazione: Integrazione di Azure Active Directory con Central Desktop | Documentazione Microsoft'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e Central Desktop.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Esercitazione: Integrazione di Azure Active Directory con Central Desktop

In questa esercitazione informazioni su come integrare Central Desktop con Azure Active Directory (Azure AD).

Integrazione di Central Desktop con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso a Central Desktop.
- È possibile consentire agli utenti di ottenere accesso viene eseguito automaticamente a Central Desktop con i propri account Azure AD.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre dettagli sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Central Desktop, è necessario quanto segue:

- Sottoscrizione di Azure AD
- Una sottoscrizione di Central Desktop single sign in abilitato

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se si dispone già di un ambiente di valutazione di Azure AD, [ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Central Desktop dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-central-desktop-from-the-gallery"></a>Aggiungere Central Desktop dalla raccolta
Per configurare l'integrazione di Central Desktop in Azure AD, è necessario aggiungere all'elenco di App SaaS gestite Central Desktop dalla raccolta.

**Per aggiungere Central Desktop dalla raccolta, eseguire le operazioni seguenti:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere nuove applicazioni, selezionare il **nuova applicazione** pulsante nella parte superiore nella finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **Central Desktop**. Selezionare **Central Desktop** dal riquadro dei risultati e quindi selezionare il **Aggiungi** pulsante per aggiungere l'applicazione.

    ![Nell'elenco dei risultati Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con Central Desktop basato su un utente di test denominato "Britta Simon".

Per single sign-on a funzionare, Azure AD deve conoscere l'identità dell'utente corrispondente in Central Desktop a un utente in Azure AD. In altre parole, è necessario stabilire un collegamento tra un utente di Azure AD e un utente correlato in Central Desktop.

In Central Desktop, assegnare **Username** lo stesso valore come **nome utente** in Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti.

Per configurare e testare Azure AD single sign-on con Central Desktop, è necessario completare i seguenti blocchi predefiniti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente test Central Desktop](#create-a-central-desktop-test-user) disporre di un equivalente di Britta Simon in Central Desktop che è collegata la rappresentazione di Azure AD dell'utente.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on nell'applicazione Central Desktop.

**Per configurare Azure single sign-on AD con Central Desktop, procedere come segue:**

1. Nel portale di Azure, sul **Central Desktop** pagina di integrazione dell'applicazione, seleziona **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Per abilitare single sign-on, nel **Single sign-on** della finestra di dialogo di **modalità** elenco a discesa, seleziona **basato su SAML Sign-on**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. Nel **dominio Desktop centrale e gli URL** sezione, eseguire le operazioni seguenti:

    ![Informazioni URL single sign-on e dominio Desktop centrale](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. Nella casella **URL di accesso** digitare un URL usando il modello seguente: `https://<companyname>.centraldesktop.com`

    b. Nel **identificatore** casella, digitare un URL con il modello seguente:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. Nella casella **URL di risposta** digitare un URL nel formato seguente: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore effettivo URL di risposta e URL di accesso. Contattare il [team di supporto client di Central Desktop](https://imeetcentral.com/contact-us) per ottenere questi valori. 

4. Nel **certificato di firma SAML** selezionare **certificato**. Quindi salvare il file del certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Fare clic sul pulsante **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. Nel **configurazione Desktop centrale** selezionare **configurare Central Desktop** per aprire la **Configura sign-on** finestra. Copiare l'**URL di disconnessione, l'ID entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido**.

    ![Configurazione del Desktop centrale](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Accedi al **Central Desktop** tenant.

8. Passare a **Impostazioni**. Selezionare **avanzate**, quindi selezionare **Single Sign-On**.

    ![Installazione - Avanzate](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "Installazione - Avanzate")

9. Nel **Single Sign On Settings** pagina, procedere come segue:

    ![Single sign-on impostazioni](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "Single Sign On Settings")
    
    a. Selezionare **Enable SAML v2 Single Sign On**.
    
    b. Nel **URL SSO** incollare il **ID entità SAML** valore copiato dal portale di Azure.
    
    c. Nel **URL di accesso SSO** incollare il **SAML Single Sign-On Service URL** valore copiato dal portale di Azure.
    
    d. Nel **URL disconnessione SSO** incollare il **Sign-Out URL** valore copiato dal portale di Azure.

10. Nel **metodo verifica della firma del messaggio** sezione, eseguire le operazioni seguenti:

    ![Metodo di verifica della firma del messaggio](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "metodo verifica della firma del messaggio") una. Selezionare **Certificate**.
    
    b. Nel **certificato SSO** elenco, selezionare **RSH SHA256**.
    
    c. Aprire il certificato scaricato nel blocco note. Quindi copiare il contenuto del certificato e incollarlo il **certificato SSO** campo.
        
    d. Selezionare **Display a link to your SAMLv2 login page**.
    
    e. Selezionare **aggiornamento**.

> [!TIP]
> Un riepilogo di queste istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto questa app dal **Active Directory** > **applicazioni aziendali** sezione, selezionare il **Single Sign-On** scheda e quindi accedere incorporato documentazione tramite il **configurazione** sezione nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata, vedere la [documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

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
 
### <a name="create-a-central-desktop-test-user"></a>Creare un utente test Central Desktop

Per gli utenti di Azure AD essere in grado di accedere, è necessario essere il provisioning nell'applicazione Central Desktop. In questa sezione viene descritto come creare account utente di Azure AD in Central Desktop.

> [!NOTE]
> Per eseguire il provisioning degli account utente AD Azure, è possibile utilizzare qualsiasi altro strumento di creazione di account utente Central Desktop o le API fornite da Central Desktop.

**Per eseguire il provisioning degli account utente in Central Desktop:**

1. Accedere al tenant di Central Desktop.

2. Passare a **persone** > **membri interni**.

3. Selezionare **Aggiungi membri interni**.

    ![Persone](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "Persone")
    
4. Nel **Email Address of New Members** , digitare un account di Azure AD che si desidera eseguire il provisioning e quindi selezionare **Avanti**.

    ![Gli indirizzi dei nuovi membri di posta elettronica](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "gli indirizzi dei nuovi membri di posta elettronica")

5. Selezionare **Aggiungi membri interni**.

    ![Aggiungi membri interni](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "Aggiungi membri interni")
   
   >[!NOTE]
   >Gli utenti aggiunti ricevono un messaggio di posta elettronica che include un collegamento di conferma per attivare i propri account.
   
### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare utente Britta Simon per utilizzare single sign-on Azure concedendo l'accesso a Central Desktop.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Central Desktop, procedere come segue:**

1. Nel portale di Azure aprire la visualizzazione applicazioni. Passare alla visualizzazione directory e quindi passare alla **applicazioni aziendali**.

2. Selezionare **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

2. Nell'elenco delle applicazioni, selezionare **Central Desktop**.

    ![Il collegamento di Central Desktop nell'elenco delle applicazioni](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Nel **Aggiungi** la finestra di dialogo, seleziona il **assegnare** pulsante.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione, utilizzare il pannello di accesso test configurazione di Azure AD single sign-on.

Quando si seleziona il riquadro di Central Desktop nel Pannello di accesso, l'utente automaticamente ottenere connesso all'applicazione Central Desktop.
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

