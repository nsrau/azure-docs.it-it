---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cisco Webex | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005521"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Webex

Questa esercitazione descrive come integrare Cisco Webex con Azure Active Directory (Azure AD).

L'integrazione di Cisco Webex con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Cisco Webex.
- È possibile abilitare gli utenti per l'accesso automatico a Cisco Webex con gli account Azure AD personali.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre dettagli sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cisco Webex, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Cisco Webex abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Cisco Webex dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-cisco-webex-from-the-gallery"></a>Aggiungere Cisco Webex dalla raccolta
Per configurare l'integrazione di Cisco Webex in Azure AD, è necessario aggiungere Cisco Webex dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Cisco Webex dalla raccolta, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Cisco Webex**. 

5. Selezionare **Cisco Webex** nel pannello dei risultati. Selezionare quindi il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Cisco Webex nell'elenco dei risultati](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cisco Webex mediante un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Cisco Webex corrispondente a un utente di Azure AD. In altre parole, deve essere stabilito un collegamento tra un utente di Azure AD e un utente correlato in Cisco Webex.

In Cisco Webex assegnare al valore **Username** (Nome utente) lo stesso valore del **nome utente** in Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti. 

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cisco Webex, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Cisco Webex](#create-a-cisco-webex-test-user) per avere una controparte di Britta Simon in Cisco Webex collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel nuovo portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Cisco Webex.

**Per configurare l'accesso Single Sign-On di Azure AD con Cisco Webex, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cisco Webex** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Per abilitare l'accesso Single Sign-On, nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** nell'elenco a discesa **Modalità**.
 
    ![Finestra di dialogo Single Sign-On](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. In un'altra finestra del Web browser accedere al sito aziendale di Cisco Webex come amministratore.

4. Fare clic su **Settings** (Impostazioni) nel menu a sinistra.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. Nella pagina delle impostazioni scorrere verso il basso fino alla sezione **Authentication** (Autenticazione) e fare clic su **Modify** (Modifica).

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Selezionare **Integrate a 3rd-party identity provider. (Advanced)** (Integra provider di terze parti - Avanzate) e passare alla schermata successiva.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. Nella pagina **Export Directory Metadata** (Esporta metadati directory) fare clic su **Download Metadata File** (Scarica file metadati) per scaricare il file di metadati.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. Nel portale di Azure, nella sezione **URL e dominio Cisco Webex**, caricare il **file di metadati del provider di servizi** e configurare l'applicazione seguendo questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. Al termine del caricamento del **file di metadati del provider di servizi**, i valori dell'**identificatore** e dell'**URL di risposta** popolano automaticamente le caselle di testo della sezione **URL e dominio Cisco Webex**, come illustrato di seguito:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. Nella casella **URL di accesso** digitare un URL usando il modello seguente: `https://<SUBDOMAIN>.webex.com/`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso effettivo. Per ottenere questi valori contattare il [team di supporto clienti di Cisco Webex](https://www.webex.co.in/support/support-overview.html).

9. L'applicazione Cisco Webex prevede che le asserzioni SAML contengano attributi specifici. Configurare gli attributi seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    |  Nome attributo  | Valore attributo |
    | --------------- | -------------------- |    
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   uid    | user.mail |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

11. Nella sezione **Certificato di firma SAML** selezionare **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. Selezionare **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. Nella pagina di amministrazione del sito aziendale Cisco Webex usare l'opzione del browser di file per individuare e caricare il file di metadati di Azure AD. Selezionare quindi **Require certificate signed by a certificate authority in Metadata (more secure)** (Richiedi certificato firmato da un'autorità di certificazione in metadati - più sicura) e passare alla schermata successiva. 

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. Selezionare **Test SSO Connection** (Test connessione SSO) e, quando viene aperta una nuova scheda del browser, eseguire l'autenticazione con Azure AD effettuando l'accesso.

15. Tornare alla scheda **Cisco Cloud Collaboration Management** del browser. Se il test ha avuto esito positivo, selezionare l'opzione **This test was successful. Enable Single Sign-On** (Test riuscito. Abilita Single Sign-On) e fare clic su **Save** (Salva).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-cisco-webex-test-user"></a>Creare un utente di test per Cisco Webex

Questa sezione descrive come creare un utente denominato Britta Simon in Cisco Webex. Cisco Webex supporta il provisioning just-in-time e il provisioning utenti automatico, che è abilitato per impostazione predefinita. Altri dettagli su come configurare il provisioning utenti automatico sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione l'utente Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Webex.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Cisco Webex, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione applicazioni. Passare poi alla visualizzazione directory e quindi ad **Applicazioni aziendali**.  

2. Selezionare **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

3. Nell'elenco delle applicazioni selezionare **Cisco Webex**.

    ![Collegamento di Cisco Webex nell'elenco delle applicazioni](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Selezionare il pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cisco Webex nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cisco Webex.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

