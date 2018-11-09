---
title: 'Esercitazione: Integrazione di Azure Active Directory con BlueJeans | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095229"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Esercitazione: Integrazione di Azure Active Directory con BlueJeans

Questa esercitazione descrive come integrare BlueJeans con Azure Active Directory (Azure AD).

L'integrazione di BlueJeans con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a BlueJeans.
- È possibile abilitare gli utenti per l'accesso automatico a BlueJeans (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con BlueJeans, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di BlueJeans abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di BlueJeans dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-bluejeans-from-the-gallery"></a>Aggiunta di BlueJeans dalla raccolta

Per configurare l'integrazione di BlueJeans in Azure AD, è necessario aggiungere BlueJeans dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere BlueJeans dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **BlueJeans**, selezionare **BlueJeans** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![BlueJeans nell'elenco risultati](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con BlueJeans usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di BlueJeans corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BlueJeans.

Per configurare e testare l'accesso Single Sign-On di Azure AD con BlueJeans, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di BlueJeans](#creating-a-bluejeans-test-user)**: per avere una controparte di Britta Simon in BlueJeans collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione BlueJeans.

**Per configurare l'accesso Single Sign-On di Azure AD con BlueJeans, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **BlueJeans** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/bluejeans-tutorial/tutorial_general_301.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Il valore di accesso non è reale. è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di BlueJeans](https://support.bluejeans.com/contact).

6. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Certificato (Base64)** e salvare il file di certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. Nella sezione **Configura BlueJeans** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di BlueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. In un'altra finestra del Web browser accedere al sito aziendale di **BlueJeans** come amministratore.

9. Passare ad **ADMIN \> GROUP SETTINGS \> SECURITY**.

    ![Amministratore](./media/bluejeans-tutorial/IC785868.png "Amministratore")

10. Nella sezione **SECURITY** seguire questa procedura:

    ![Single Sign-On SAML](./media/bluejeans-tutorial/IC785869.png "Single Sign-On SAML")

    a. Selezionare **SAML Single Sign On**.

    b. Selezionare **Enable automatic provisioning**.

11. Continuare e seguire questa procedura:

    ![Percorso certificato](./media/bluejeans-tutorial/IC785870.png "Percorso certificato")

    a. Fare clic su **Choose a File** (Scegli un file) per caricare il certificato codificato in base 64 scaricato dal portale di Azure.

    b. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Modifica URL password** incollare il valore dell'**URL di modifica password** copiato dal portale di Azure.

    d. Nella casella di testo **URL disconnessione** incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

12. Continuare e seguire questa procedura:

    ![Salvare le modifiche](./media/bluejeans-tutorial/IC785874.png "Salvare le modifiche")

    a. Nella casella di testo **User id** (ID utente) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Nella casella di testo **Email** (Posta elettronica) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Fare clic su **SAVE CHANGES** (Salva modifiche).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-a-bluejeans-test-user"></a>Creazione di un utente di test di BlueJeans

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in BlueJeans. BlueJeans supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. È possibile scoprire più dettagli [qui](bluejeans-provisioning-tutorial.md) su come configurare il provisioning utenti automatico.

**Per creare un utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di **BlueJeans** come amministratore.

2. Passare ad **AMMINISTRATORE \> GESTISCI UTENTI \> AGGIUNGI UTENTE**.

    ![Amministratore](./media/bluejeans-tutorial/IC785877.png "Amministratore")

    >[!IMPORTANT]
    >La scheda **AGGIUNGI UTENTE** è disponibile solo se nella scheda **SICUREZZA** l'opzione **Enable automatic provisioning** è deselezionata. 

3. Nella sezione **AGGIUNGI UTENTE** seguire questa procedura:

    ![Aggiungere un utente](./media/bluejeans-tutorial/IC785886.png "Aggiungere un utente")

    a. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Pick a BlueJeans Username** (Scegli un nome utente BlueJeans) immettere il nome utente dell'utente, ad esempio **Brittasimon**

    d. Nella casella di testo **Crea una password** immettere la password.

    e. Nella casella di testo **Società** immettere il nome della società.

    f. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    g. Nella casella di testo **Create a BlueJeans Meeting I.D** (Crea un ID riunione BlueJeans) immettere l'ID riunione.

    h. Nella casella di testo **Pick a Moderator Passcode** (Seleziona passcode moderatore) immettere il passcode.

    i. Fare clic su **CONTINUE** (Continua).

    ![Aggiungi utente](./media/bluejeans-tutorial/IC785887.png "Aggiungi utente")

    J. Fare clic su **Aggiungi utente**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da BlueJeans per eseguire il provisioning degli account utente di Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BlueJeans.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **BlueJeans**.

    ![Configure Single Sign-On](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro BlueJeans nel pannello di accesso, si accederà automaticamente all'applicazione BlueJeans.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
