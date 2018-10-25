---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zendesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268175"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Esercitazione: Integrazione di Azure Active Directory con Zendesk

Questa esercitazione descrive come integrare Zendesk con Azure Active Directory (Azure AD).

L'integrazione di Zendesk con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Zendesk.
- È possibile abilitare gli utenti per l'accesso automatico a Zendesk (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zendesk, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Zendesk abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Zendesk dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-zendesk-from-the-gallery"></a>Aggiunta di Zendesk dalla raccolta

Per configurare l'integrazione di Zendesk in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Zendesk dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/zendesk-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/zendesk-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/zendesk-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **Zendesk**, selezionare **Zendesk** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zendesk in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Zendesk che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zendesk.

Per stabilire la relazione di collegamento, in Zendesk assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zendesk, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Zendesk](#create-a-zendesk-test-user)**: per avere una controparte di Britta Simon in Zendesk collegata alla relativa rappresentazione in Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Zendesk.

**Per configurare Single Sign-On di Azure AD con Zendesk, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Zendesk** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![image](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.zendesk.com`.

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<subdomain>.zendesk.com`.

    ![image](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto del client Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise).

6. Zendesk prevede che le asserzioni SAML abbiano un formato specifico. Non vi sono attributi SAML obbligatori ma, facoltativamente, è possibile aggiungere un attributo dalla sezione **Attributi utente** della pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](./media/zendesk-tutorial/i4-attribute.png)

7. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/zendesk-tutorial/i2-attribute.png)

    ![image](./media/zendesk-tutorial/i3-attribute.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.
    
    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.
    
    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

    > [!NOTE]
    > Usare gli attributi dell'estensione per aggiungere attributi che non sono in Azure AD per impostazione predefinita. Fare clic su [User attributes that can be set in SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) (Attributi utente che possono essere impostati in SAML) per ottenere l'elenco completo degli attributi accettati da **Zendesk**.

8. Nella sezione **Certificato di firma SAML** copiare l'**Identificazione digitale** e salvarla nel computer.

    ![image](./media/zendesk-tutorial/C3_certificate.png)

    a. Selezionare l'opzione appropriata per **Opzione di firma**, se necessario.

    b. Selezionare l'opzione appropriata per **Algoritmo di firma**, se necessario.

    c. Fare clic su **Save**

9. Nella sezione **Set up Zendesk** (Configura Zendesk) fare clic su **Visualizzare le istruzioni dettagliate** per aprire la finestra **Configura accesso**. Copiare gli URL sottostanti dalla sezione **Riferimento rapido**.

    La descrizione dell'URL potrebbe essere:

    a. SAML Single Sign-On Service URL (URL servizio Single Sign-On SAML)

    b. ID entità

    c. Sign-Out URL (URL di disconnessione)

    ![image](./media/zendesk-tutorial/d1_saml.png) 

10. Esistono due modi per configurare Zendesk: automaticamente e manualmente.
  
11. Per automatizzare la configurazione all'interno di Zendesk, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installare l'estensione**.

    ![image](./media/zendesk-tutorial/install_extension.png)

12. Dopo aver aggiunto l'estensione al browser, fare clic su **Setup Zendesk** (Configura Zendesk) per passare direttamente all'applicazione Zendesk. Da qui, fornire le credenziali di amministratore per accedere a Zendesk. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà il passaggio 13.

     ![image](./media/zendesk-tutorial/d2_saml.png) 

13. Se si vuole configurare manualmente Zendesk, aprire una nuova finestra del Web browser, accedere al sito aziendale di Zendesk come amministratore e seguire questa procedura:

    * Fare clic su **Admin**.

    * Nel riquadro di spostamento sinistro fare clic su **Impostazioni** e quindi su **Sicurezza**.

    * Nella pagina **Sicurezza** eseguire la procedura seguente:

      ![Sicurezza](././media/zendesk-tutorial/ic773089.png "Sicurezza")

      ![Single Sign-On](././media/zendesk-tutorial/ic773090.png "Single Sign-On")

      a. Fare clic sulla scheda **Amministratore e agenti**.

      b. Selezionare **Single sign-on (SSO) e SAML** e quindi **SAML**.

      c. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

      d. Nella casella di testo **Remote Log Out URL** (URL di disconnessione remota) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

      e. Nella casella di testo **Certificate Fingerprint** (Impronta digitale certificato) incollare il valore **Identificazione personale** del certificato copiato dal portale di Azure.

      f. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/zendesk-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/zendesk-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/zendesk-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="create-a-zendesk-test-user"></a>Creare un utente test Zendesk

Questa sezione descrive come creare un utente chiamato Britta Simon in Zendesk. Zendesk supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. È possibile scoprire più dettagli [qui](Zendesk-provisioning-tutorial.md) su come configurare il provisioning utenti automatico.

**Per creare un utente manualmente, seguire questa procedura:**

> [!NOTE]
> All'accesso viene eseguito automaticamente il provisioning degli account dell'**utente finale**. Il provisioning degli account **Agente** e **Amministratore** devono essere eseguiti manualmente in **Zendesk** prima dell'accesso.

1. Accedere al tenant di **Zendesk** .

2. Selezionare la scheda **Customer List** .

3. Selezionare la scheda **User** e fare clic su **Add**.

    ![Aggiungere un utente](././media/zendesk-tutorial/ic773632.png "Aggiungere un utente")
4. Digitare il **Nome** e l'**Indirizzo di posta elettronica** di un account Azure AD esistente da sottoporre a provisioning, quindi fare clic su **Salva**.

    ![Nuovo utente](././media/zendesk-tutorial/ic773633.png "Nuovo utente")

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zendesk per eseguire il provisioning degli account utente Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zendesk.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/zendesk-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **Zendesk**.

    ![image](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/zendesk-tutorial/d_leftpaneusers.png)

4. Fare clic sul pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/zendesk-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Zendesk nel riquadro di accesso, si dovrebbe accedere automaticamente all'applicazione Zendesk.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura provisioning utenti](zendesk-provisioning-tutorial.md)
