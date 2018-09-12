---
title: 'Esercitazione: Integrazione di Azure Active Directory con Comeet Recruiting Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Comeet Recruiting Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 75f51dc9-9525-4ec6-80bf-28374f0c8adf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: jeedes
ms.openlocfilehash: 137ba7a7e82ff3e57d862868859e8049838701a3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43311325"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>Esercitazione: Integrazione di Azure Active Directory con Comeet Recruiting Software

Questa esercitazione descrive come integrare Comeet Recruiting Software con Azure Active Directory (Azure AD).

L'integrazione di Comeet Recruiting Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Comeet Recruiting Software.
- È possibile abilitare gli utenti per l'accesso automatico a Comeet Recruiting Software (Single Sign-On) con i propri account di Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Comeet Recruiting Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Comeet Recruiting Software abilitata per l'accesso Single Sign-On

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Comeet Recruiting Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-comeet-recruiting-software-from-the-gallery"></a>Aggiunta di Comeet Recruiting Software dalla raccolta

Per configurare l'integrazione di Comeet Recruiting Software in Azure AD, è necessario aggiungere Comeet Recruiting Software dalla raccolta nel proprio elenco di app SaaS gestite.

**Per aggiungere Comeet Recruiting Software dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Comeet Recruiting Software**, selezionare **Comeet Recruiting Software** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Comeet Recruiting Software nell'elenco dei risultati](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione si configura e si testa l'accesso Single Sign-On di Azure AD con Comeet Recruiting Software con un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On Azure AD deve conoscere qual è l'utente di Comeet Recruiting Software che corrisponde a un utente di Azure AD. Deve essere stabilita in altre parole una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Comeet Recruiting Software.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Comeet Recruiting Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Comeet Recruiting Software](#create-a-comeet-recruiting-software-test-user)**: per avere una controparte di Britta Simon in Comeet Recruiting Software collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione nel portale di Azure si abilita l'accesso Single Sign-On di Azure AD, che viene configurato nell'applicazione Comeet Recruiting Software.

**Per configurare l'accesso Single Sign-On di Azure AD con Comeet Recruiting Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Comeet Recruiting Software** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_samlbase.png)

3. Nella sezione **URL e dominio Comeet Recruiting Software** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Comeet](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url1.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore effettivo, l'URL di risposta. Si otterranno questi valori dal portale di Comeet Recruiting Software come illustrato nella [pagina del supporto](https://support.comeet.co/knowledgebase/adfs-single-sign-on/).

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Comeet Recruiting Software](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url2.png)

    Nella casella di testo **URL accesso** digitare l'URL: `https://app.comeet.co`

5. L'applicazione Comeet Recruiting Software prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito dell'**ID utente** è **user.userprincipalname** ma **Comeet Recruiting Software** si aspetta che venga mappato all'indirizzo e-mail dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione.

    ![Configure Single Sign-On](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_attribute.png)

6. Fare clic sulla casella di controllo **Visualizza e modifica tutti gli altri attributi utente** nella sezione **Attributi utente** per espandere gli attributi. Seguire questa procedura per ogni attributo visualizzato:

    | Nome attributo | Valore attributo |
    | ---------------| --------------- |
    | comeet_id | user.userprincipalname |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il **nome dell'attributo** indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Fare clic su **OK**.

7. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_certificate.png)

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/comeetrecruitingsoftware-tutorial/tutorial_general_400.png)

9. Per configurare l'accesso Single Sign-On sul lato di **Software Recruiting Comeet**, incollare il contenuto del codice XML di metadata scaricato in Comeet Recruiting Software, come illustrato nella [pagina del supporto](https://support.comeet.co/knowledgebase/adfs-single-sign-on/).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/comeetrecruitingsoftware-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/comeetrecruitingsoftware-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/comeetrecruitingsoftware-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/comeetrecruitingsoftware-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-comeet-recruiting-software-test-user"></a>Creare un utente test di Software Recruiting Comeet

In questa sezione si crea un utente chiamato Britta Simon in Comeet Recruiting Software. Rivolgersi [team di supporto di Comeet Recruiting Software](mailto:support@comeet.co) per aggiungere gli utenti alla piattaforma Comeet Recruiting Software. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Comeet Recruiting Software.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Comeet Recruiting Software, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Comeet Recruiting Software**.

    ![Nell'elenco delle applicazioni selezionare il collegamento Comeet Recruiting Software](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione si testa la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Comeet Recruiting Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Comeet Recruiting Software.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_01.png
[2]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_02.png
[3]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_03.png
[4]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_04.png

[100]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_100.png

[200]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_200.png
[201]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_201.png
[202]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_202.png
[203]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_203.png