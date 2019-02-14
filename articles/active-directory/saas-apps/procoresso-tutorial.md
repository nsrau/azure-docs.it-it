---
title: 'Esercitazione: integrazione di Azure Active Directory con Procore SSO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d72f4ac39ac992d0386b3971cd4d3bc6616f0a28
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186456"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Esercitazione: integrazione di Azure Active Directory con Procore SSO

Questa esercitazione descrive come integrare Procore SSO con Azure Active Directory (Azure AD).

L'integrazione di Procore SSO con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Procore SSO.
- È possibile abilitare gli utenti per l'accesso automatico a Procore SSO (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Procore SSO, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Procore SSO abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Procore SSO dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-procore-sso-from-the-gallery"></a>Aggiungere Procore SSO dalla raccolta

Per configurare l'integrazione di Procore SSO in Azure AD, è necessario aggiungere Procore SSO dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Procore SSO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Procore SSO**, selezionare **Procore SSO** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Procore SSO nell'elenco risultati](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Procore SSO con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Procore SSO che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Procore SSO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Procore SSO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Procore SSO](#creating-a-procore-sso-test-user)** : per avere una controparte di Britta Simon in Procore SSO collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Procore SSO.

**Per configurare Single Sign-On di Azure AD con Procore SSO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Procore SSO** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione in quanto l'app è già preintegrata in Azure.

    ![Informazioni sull'accesso Single Sign-On per l'URL e il dominio di Procore SSO](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. Nella sezione **Certificato di firma SAML** della pagina **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Metadati federativi XML** e salvare il file di metadati nel computer in uso.

    ![Collegamento di download del certificato](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. Nella sezione **Configura Procore SSO** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di Procore SSO](common/configuresection.png)

7. Per configurare l'accesso Single Sign-On sul lato **Procore SSO**, accedere al sito aziendale di Procore come amministratore.

8. Nell'elenco della casella degli strumenti scegliere **Admin** per aprire la pagina delle impostazioni SSO.

    ![Configure Single Sign-On](./media/procoresso-tutorial/procore_tool_admin.png)

9. Incollare i valori nelle caselle come descritto di seguito.

    ![Configure Single Sign-On](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Nella casella di testo **Single Sign On Issuer URL** (URL autorità di certificazione Single Sign-On) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella **SAML Sign On Target URL** (URL di destinazione accesso SAML). incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire il file **XML metadati federazione** scaricato in precedenza dal portale di Azure e copiare il certificato nel tag denominato **X509Certificate**. Incollare il valore copiato nel **Single Sign On x509 Certificate** (Certificato x509 Single Sign-On).

10. Fare clic su **Save Changes** (Salva modifiche).

11. Dopo queste impostazioni, è necessario inviare il **nome di dominio**, ad esempio **contoso.com**, con cui si effettua l'accesso a Procore al [team di supporto Procore](https://support.procore.com/), che attiverà l'accesso Single Sign-On federato per quel dominio.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

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

### <a name="creating-a-procore-sso-test-user"></a>Creare un utente test di Procore SSO

Eseguire la procedura seguente per creare un utente test di Procore sul lato Procore SSO.

1. Accedere al sito aziendale di Procore come amministratore.  

2. Nell'elenco a discesa della casella degli strumenti fare clic su **Directory** per aprire la pagina della directory aziendale.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Fare clic su **Add a Person** (Aggiungi una persona) per aprire il modulo e immettere le opzioni seguenti.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_add.png)

    a. Digitare il nome di Britta Simon nella casella di testo **First Name** (Nome), ad esempio **Britta**.

    b. Digitare il cognome di Britta Simon nella casella di testo **Last Name** (Cognome), ad esempio **Simon**.

    c. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio **BrittaSimon@contoso.com**.

    d. Selezionare **Permission Template** (Modello di autorizzazione) per **Apply Permission Template Later** (Applica modello di autorizzazione più tardi).

    e. Fare clic su **Create**(Crea).

4. Verificare e aggiornare i dettagli del contatto appena aggiunto.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_check.png)

5. Se è richiesto un invito via posta elettronica, fare clic su **Save and Send Invitation** (Salva e invia invito) oppure scegliere **Save** (Salva) per salvare direttamente e completare la registrazione dell'utente.
    
    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a Procore SSO per consentirle di usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **Procore SSO**.

    ![Configure Single Sign-On](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Procore SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Procore SSO.
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
