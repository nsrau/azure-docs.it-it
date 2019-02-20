---
title: 'Esercitazione: integrazione di Azure Active Directory con Spotinst | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 679284783ac93662c1c96e813f9f028fffb434f0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173658"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Esercitazione: integrazione di Azure Active Directory con Spotinst

Questa esercitazione descrive come integrare Spotinst con Azure Active Directory (Azure AD).

L'integrazione di Spotinst con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Spotinst.
- È possibile abilitare gli utenti per l'accesso automatico a Spotinst (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Spotinst, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di Spotinst abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Spotinst dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-spotinst-from-the-gallery"></a>Aggiunta di Spotinst dalla raccolta
Per configurare l'integrazione di Spotinst in Azure AD, è necessario aggiungere Spotinst dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Spotinst dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Spotinst**, selezionare **Spotinst** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Spotinst nell'elenco risultati](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Spotinst con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Spotinst che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Spotinst.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Spotinst, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Spotinst](#create-a-spotinst-test-user)**: per avere una controparte di Britta Simon in Spotinst collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Spotinst.

**Per configurare Single Sign-On di Azure AD con Spotinst, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Spotinst** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. Nella sezione **URL e dominio Spotinst** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da IDP:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Spotinst](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. Selezionare **Mostra impostazioni URL avanzate**

    b. Nella casella di testo **Stato dell'inoltro** digitare un valore: `<ID>`

    c. Se si vuole configurare l'applicazione in modalità avviata da **SP** nella sezione **URL di accesso** digitare l'URL: `https://console.spotinst.com`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornarlo con il valore di Stato dell'inoltro reale, descritto più avanti nell'esercitazione.

4. L'applicazione Spotinst prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configure Single Sign-On](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:

    | Nome attributo | Valore attributo |
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.

    e. Fare clic su **Ok**

6. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/spotinst-tutorial/tutorial_general_400.png)

8. In un'altra finestra del Web browser accedere a Spotinst come amministratore della sicurezza.

9. Fare clic sull'**icona utente** in alto a destra nella schermata e fare clic su **Settings** (Impostazioni).

    ![Impostazioni di Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. Fare clic sulla scheda **SECURITY** (SICUREZZA) nella parte superiore e quindi selezionare **Identity Providers** (Provider di identità) e seguire questa procedura:

    ![Sicurezza di Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Copiare il valore di **Relay State** (Stato dell'inoltro) per l'istanza e incollarlo nella casella di testo **Stato dell'inoltro** della sezione **URL e dominio Spotinst** del portale di Azure.

    b. Fare clic su **BROWSE** (SFOGLIA) per caricare il file XML di metadati scaricato dal portale di Azure

    c. Fare clic su **SAVE**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/spotinst-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/spotinst-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/spotinst-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/spotinst-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-spotinst-test-user"></a>Creare un utente di test di Spotinst

Questa sezione descrive come creare un utente chiamato Britta Simon in Spotinst.

1. Se l'applicazione è stata configurata in modalità avviata da **SP**, seguire questa procedura:

   a. In un'altra finestra del Web browser accedere a Spotinst come amministratore della sicurezza.

   b. Fare clic sull'**icona utente** in alto a destra nella schermata e fare clic su **Settings** (Impostazioni).

    ![Impostazioni di Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Fare clic su **Users** (Utenti) e selezionare **ADD USER** (AGGIUNGI UTENTE).

    ![Impostazioni di Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Nella sezione Add User (Aggiungi utente) seguire questa procedura:

    ![Impostazioni di Spotinst](./media/spotinst-tutorial/adduser2.png)

    * Nella casella di testo **Full Name** (Nome completo) immettere nome e cognome dell'utente, ad esempio **BrittaSimon**.

    * Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    * Selezionare i dettagli specifici dell'organizzazione per **Organization Role, Account Role e Accounts** (Ruolo organizzazione, Ruolo account e Account).

2. Se l'applicazione è stata configurata nella modalità avviata da **IDP**, non è necessario alcun intervento dell'utente in questa sezione. Spotinst supporta il provisioning JIT, che è abilitato per impostazione predefinita. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a Spotinst.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Spotinst.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Spotinst, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Spotinst**.

    ![Collegamento di Spotinst nell'elenco delle applicazioni](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Spotinst nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Spotinst.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

