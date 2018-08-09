---
title: 'Esercitazione: Integrazione di Azure Active Directory con NetSuite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 511fdcf587d16a59ff2bb11dfc55504b2218a569
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431410"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Esercitazione: Integrazione di Azure Active Directory con NetSuite

Questa esercitazione descrive come integrare NetSuite con Azure Active Directory (Azure AD).

L'integrazione di NetSuite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a NetSuite
- È possibile abilitare gli utenti per l'accesso automatico a NetSuite (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con NetSuite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di NetSuite abilitata per il Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di NetSuite dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-netsuite-from-the-gallery"></a>Aggiunta di NetSuite dalla raccolta
Per configurare l'integrazione di NetSuite in Azure AD, è necessario aggiungere NetSuite dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere NetSuite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]

1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **NetSuite**, selezionare **NetSuite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![NetSuite nell'elenco dei risultati](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con NetSuite in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di NetSuite corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in NetSuite.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in NetSuite.

Per configurare e testare l'accesso Single Sign-On di Azure AD con NetSuite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di NetSuite](#creating-a-netsuite-test-user)**: per avere una controparte di Britta Simon in NetSuite collegata alla relativa rappresentazione in Azure AD dell'utente.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione NetSuite.

**Per configurare l'accesso Single Sign-On di Azure AD con NetSuite, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **NetSuite** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

1. Nella sezione **URL e dominio NetSuite** seguire questa procedura:

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta effettivo. Per ottenere i valori, contattare il [team di supporto di NetSuite](http://www.NetSuite.com/portal/services/support.shtml).

1. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di NetSuite** fare clic su **Configura NetSuite** per visualizzare la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

1. Aprire una nuova scheda nel browser e accedere al sito della società NetSuite come amministratore.

1. Sulla barra degli strumenti nella parte superiore della pagina, fare clic su **Setup** (Installazione), quindi passare a **Company** (Società) e fare clic su **Enable Features** (Attiva funzionalità).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setupsaml.png)

1. Sulla barra degli strumenti al centro della pagina, fare clic su **SuiteCloud**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-suitecloud.png)

1. Nella sezione **Manage Authentication** (Gestisci autenticazione) selezionare **SAML SINGLE SIGN-ON** per abilitare l'opzione SAML SINGLE SIGN-ON in NetSuite.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-ticksaml.png)

1. Sulla barra degli strumenti nella parte superiore della pagina fare clic su **Setup** (Configurazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

1. Dall'elenco **SETUP TASKS** (Configura attività) selezionare **Integration** (Integrazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-integration.png)

1. Nella sezione **MANAGE AUTHENTICATION** (Gestione autenticazione) fare clic su **SAML Single Sign-on**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml.png)

1. Nella pagina **SAML Setup** (Configurazione SAML), nella sezione **NetSuite Configuration** (Configurazione NetSuite) seguire questa procedura:

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selezionare **PRIMARY AUTHENTICATION METHOD** (Metodo di autenticazione primario).

    b. Per il campo con etichetta **SAMLV2 IDENTITY PROVIDER METADATA** (Metadati provider identità SAMLV2) selezionare **UPLOAD IDP METADATA FILE** (Carica file di metadati IDP). Fare quindi clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.

    c. Fare clic su **Submit**.

1. In Azure AD fare clic sulla casella di controllo **Visualizza e modifica tutti gli altri attributi utente** e aggiungere l'attributo.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-attributes.png)

1. Nel campo **Nome attributo** immettere `account`. Nel campo **Valore attributo** immettere l'ID dell'account NetSuite. Questo valore è costante e cambia con l'account. Per istruzioni su come individuare l'ID dell'account, vedere di seguito:

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. In NetSuite fare clic su **Setup** (Configurazione) quindi passare a **Company** (Società) e fare clic su **Company Information** (Informazioni sulla società) dal menu di spostamento superiore.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-com.png)

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-account-id.png)

    b. Nella pagina **Company Information** (Informazioni sulla società) sulla colonna destra copiare **ID ACCOUNT**.

    c. Incollare l'**ID account** copiato dall'account NetSuite nel campo **Valore attributo** in Azure AD. 

1. Per consentire l'accesso Single Sign-On a NetSuite, è prima di tutto necessario che le autorizzazioni appropriate vengano assegnate agli utenti in NetSuite. Per assegnare le autorizzazioni, seguire le istruzioni seguenti.

    a. Dal menu di navigazione principale fare clic su **Setup** (Configurazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    b. Scegliere **Users/Roles** (Utenti/Ruoli) dal menu di navigazione a sinistra, quindi fare clic su **Manage Roles** (Gestisci ruoli).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Fare clic su **New Role**.

    d. Digitare un **nome** per il nuovo ruolo.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-new-role.png)

    e. Fare clic su **Save**.

    f. Scegliere **Permissions**dal menu disponibile nella parte superiore. Fare quindi clic su **Setup**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-sso.png)

    g. Selezionare **SAML Single Sign-on**, quindi fare clic su **Aggiungi**.

    h. Fare clic su **Save**.

    i. Scegliere **Setup** (Configurazione) dal menu di navigazione principale, quindi fare clic su **Setup Manager** (Configura gestore).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    j. Scegliere **Users/Roles** (Utenti/Ruoli) dal menu di navigazione a sinistra, quindi fare clic su **Manage Users** (Gestisci utenti).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selezionare un utente di test. Quindi fare clic su **Edit** (Modifica) e passare alla scheda **Access** (Accesso).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Nella finestra di dialogo Roles (Ruoli) selezionare il ruolo creato.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-add-role.png)

    m. Fare clic su **Save**.
 
### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/NetSuite-tutorial/create_aaduser_01.png) 

1.  Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/NetSuite-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/NetSuite-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea). 

### <a name="creating-a-netsuite-test-user"></a>Creazione di un utente test di NetSuite

In questa sezione si crea un utente di nome Britta Simon in NetSuite. NetSuite supporta il provisioning JIT, abilitato per impostazione predefinita.
Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in NetSuite, ne viene creato uno nuovo quando si tenta di accedere a NetSuite.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a NetSuite.

![Assegna utente][200] 

**Per assegnare Britta Simon a NetSuite, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **NetSuite**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/), quindi accedere all'account di test e fare clic su **NetSuite**.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura provisioning utenti](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

