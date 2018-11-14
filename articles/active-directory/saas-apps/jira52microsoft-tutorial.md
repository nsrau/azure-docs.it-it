---
title: 'Esercitazione: integrazione di Azure Active Directory con JIRA SAML SSO by Microsoft (V5.2) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e JIRA SAML SSO by Microsoft (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: jeedes
ms.openlocfilehash: 43ad99d4814923204b3b1993969bd6a0730530c5
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016132"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Esercitazione: integrazione di Azure Active Directory con JIRA SAML SSO by Microsoft (V5.2)

Questa esercitazione descrive come integrare JIRA SAML SSO by Microsoft (V5.2) con Azure Active Directory (Azure AD).

L'integrazione di JIRA SAML SSO by Microsoft (V5.2) con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi ha accesso a JIRA SAML SSO by Microsoft (V5.2).
- È possibile abilitare gli utenti per l'accesso automatico Single Sign-On a JIRA SAML SSO by Microsoft (V5.2) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="description"></a>DESCRIZIONE

Usare l'account di Microsoft Azure Active Directory con il server Atlassian JIRA per abilitare l'accesso Single Sign-On. In questo modo gli utenti dell'organizzazione possono usare le credenziali di Azure AD per accedere all'applicazione JIRA. Questo plug-in usa SAML 2.0 per la federazione.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con JIRA SAML SSO by Microsoft (V5.2) sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- JIRA Core e JIRA Software 5.2 installati e configurati nella versione a 64 bit di Windows
- Server JIRA abilitato per HTTPS
- Si noti che le versioni supportate per il plug-in JIRA sono indicate nella sezione seguente.
- Il server JIRA deve essere raggiungibile da Internet, in particolare per l'autenticazione nella pagina di accesso di Azure AD e deve essere in grado di ricevere il token da Azure AD
- Credenziali di amministratore configurate in JIRA
- WebSudo disabilitato in JIRA
- Utente test creato nell'applicazione server JIRA

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione JIRA per testare i passaggi di questa esercitazione. Testare prima di tutto l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e poi usare l'ambiente di produzione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

**Versioni supportate:**

* JIRA Core e JIRA Software: 5.2
* JIRA supporta anche le versioni da 6.0 a 7.12. Per informazioni dettagliate, fare clic su [JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di JIRA SAML SSO by Microsoft (V5.2) dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Aggiunta di JIRA SAML SSO by Microsoft (V5.2) dalla raccolta

Per configurare l'integrazione di JIRA SAML SSO by Microsoft (V5.2) in Azure AD è necessario aggiungere JIRA SAML SSO by Microsoft (V5.2) dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere JIRA SAML SSO by Microsoft (V5.2) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **JIRA SAML SSO by Microsoft (V5.2)**, selezionare **JIRA SAML SSO by Microsoft (V5.2)** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![JIRA SAML SSO by Microsoft (V5.2) nell'elenco risultati](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con JIRA SAML SSO by Microsoft (V5.2) tramite un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di JIRA SAML SSO by Microsoft (V5.2) corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra l'utente di Azure AD e l'utente correlato in JIRA SAML SSO by Microsoft (V5.2).

Per configurare e testare l'accesso Single Sign-On di Azure AD con JIRA SAML SSO by Microsoft (V5.2) è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di JIRA SAML SSO by Microsoft (V5.2)](#creating-jira-saml-sso-by-microsoft-v52-test-user)**: per avere una controparte di Britta Simon in JIRA SAML SSO by Microsoft (V5.2) collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione JIRA SAML SSO by Microsoft (V5.2).

**Per configurare l'accesso Single Sign-On di Azure AD con JIRA SAML SSO by Microsoft (V5.2), seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **JIRA SAML SSO by Microsoft (V5.2)** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per il dominio e gli URL di JIRA SAML SSO by Microsoft (V5.2)](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<domain:port>/`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La porta è facoltativa nel caso di un URL denominato. Questi valori vengono ricevuti durante la configurazione del plug Jira descritta più avanti nell'esercitazione.

5. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/jira52microsoft-tutorial/tutorial_metadataurl.png) 

6. In un'altra finestra del Web browser accedere all'istanza di JIRA come amministratore.

7. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).

    ![Configure Single Sign-On](./media/jira52microsoft-tutorial/addon1.png)

8. Nella sezione della scheda Add-ons (Componenti aggiuntivi) fare clic su **Manage add-ons** (Gestisci componenti aggiuntivi).

    ![Configure Single Sign-On](./media/jira52microsoft-tutorial/addon7.png)

9. Scaricare il plug-in dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=56521). Caricare manualmente il plug-in fornito da Microsoft tramite il menu **Upload add-on** (Carica componente aggiuntivo). Il download del plug-in è coperto dal [Contratto di Servizi Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configure Single Sign-On](./media/jira52microsoft-tutorial/addon12.png)

10. Dopo aver installato il plug-in, questo verrà visualizzato nella sezione **User Installed add-ons** (Componenti aggiuntivi installati dall'utente). Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Configure Single Sign-On](./media/jira52microsoft-tutorial/addon13.png)

11. Seguire questa procedura nella pagina di configurazione:

    ![Configure Single Sign-On](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Assicurarsi che esista un solo certificato mappato per l'app in modo che non si verifichino errori durante la risoluzione dei metadati. Se sono presenti più certificati, durante la risoluzione dei metadati, l'amministratore riceve un errore.

    a. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure e fare clic sul pulsante **Resolve** (Risolvi). Viene letto l'URL dei metadati IdP e vengono compilate tutte le informazioni dei campi.

    b. Copiare i valori relativi all'**identificatore, all'URL di risposta e all'URL di accesso** e incollarli rispettivamente nelle caselle **Identificatore, URL di risposta e URL di accesso** nella sezione **URL e dominio di JIRA SAML SSO by Microsoft (V5.2)** nel portale di Azure.

    c. In **Login Button Name** (Nome pulsante di accesso) digitare il nome del pulsante che l'organizzazione vuole mostrare agli utenti nella schermata di accesso.

    d. In **SAML User ID Locations** (Posizioni ID utente SAML) selezionare **User ID is in the NameIdentifier element of the Subject statement** (ID utente nell'elemento NameIdentifier dell'istruzione Subject) oppure **User ID is in an Attribute element** (ID utente in un elemento Attribute).  Questo ID deve essere l'ID utente di JIRA. Se non viene trovata una corrispondenza per l'ID utente, il sistema non consentirà agli utenti di accedere.

    > [!Note]
    > La posizione predefinita dell'ID utente SAML è nell'identificatore del nome. È possibile sostituirlo con un attributo e immettere il nome dell'attributo appropriato.

    e. Se si seleziona l'opzione **User ID is in an Attribute element** (ID utente in un elemento Attribute), nella casella di testo **Attribute name** (Nome attributo) digitare il nome dell'attributo per cui è previsto l'ID utente. 

    f. Se si usa un dominio federato, come AD FS o altri, con Azure AD fare clic sull'opzione **Enable Home Realm Discovery** (Abilita individuazione area di autenticazione principale) e configurare **Domain Name** (Nome dominio).

    g. In **Domain Name** (Nome dominio) digitare il nome del dominio in caso di accesso basato su AD FS.

    h. Selezionare **Enable Single Sign out** (Abilita Single Sign-Out) per impostare la disconnessione da Azure AD quando un utente si disconnette da JIRA. 

    i. Fare clic sul pulsante **Save** (Salva) per salvare le impostazioni.

    > [!NOTE]
    > Per altre informazioni sull'installazione e la risoluzione dei problemi, vedere [MS JIRA SSO Connector Admin Guide](../ms-confluence-jira-plugin-adminguide.md) (Guida all'amministrazione dei connettori Microsoft per SSO in JIRA), che contiene anche una sezione di [domande frequenti](../ms-confluence-jira-plugin-faq.md) per informazioni utili

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-jira-saml-sso-by-microsoft-v52-test-user"></a>Creazione dell'utente test di JIRA SAML SSO by Microsoft (V5.2)

Per consentire agli utenti di Azure AD di accedere a un server JIRA locale, è necessario eseguirne il provisioning sul server JIRA locale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server locale JIRA come amministratore.

2. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Aggiungere un dipendente](./media/jira52microsoft-tutorial/user1.png)

3. Si verrà reindirizzati alla pagina Administrator Access (Accesso amministratore) per inserire la **Password** e fare clic sul pulsante **Confirm** (Conferma).

    ![Aggiungere un dipendente](./media/jira52microsoft-tutorial/user2.png)

4. Nella sezione della scheda **User management** (Gestione utenti) fare clic su **Create user** (Crea utente).

    ![Aggiungere un dipendente](./media/jira52microsoft-tutorial/user3.png) 

5. Nella pagina della finestra di dialogo **"Create New User"** (Crea nuovo utente) effettuare le operazioni seguenti:

    ![Aggiungere un dipendente](./media/jira52microsoft-tutorial/user4.png)

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio Britta Simon.

    c. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.

    d. Nella casella di testo **Password** digitare la password dell'utente.

    e. Fare clic su **Create User** (Crea utente).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure AD concedendole l'accesso a JIRA SAML SSO by Microsoft (V5.2).

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **JIRA SAML SSO by Microsoft (V5.2)**.

    ![Configure Single Sign-On](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro JIRA SAML SSO by Microsoft (V5.2) nel pannello di accesso si accede automaticamente all'applicazione JIRA SAML SSO by Microsoft (V5.2).
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
