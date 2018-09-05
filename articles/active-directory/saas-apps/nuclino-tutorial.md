---
title: 'Esercitazione: Integrazione di Azure Active Directory con Nuclino | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Nuclino.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.openlocfilehash: 1a5346b98de48b1a2f8928c3c2bf30730588e9c1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43145707"
---
# <a name="tutorial-azure-active-directory-integration-with-nuclino"></a>Esercitazione: Integrazione di Azure Active Directory con Nuclino

Questa esercitazione descrive come integrare Nuclino con Azure Active Directory (Azure AD).

L'integrazione di Nuclino con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Nuclino.
- È possibile abilitare gli utenti per l'accesso automatico a Nuclino (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Nuclino, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Nuclino abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Nuclino dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-nuclino-from-the-gallery"></a>Aggiunta di Nuclino dalla raccolta

Per configurare l'integrazione di Nuclino in Azure AD, è necessario aggiungere Nuclino dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Nuclino dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Nuclino**, selezionare **Nuclino** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Nuclino nell'elenco dei risultati](./media/nuclino-tutorial/tutorial_nuclino_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Nuclino usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Nuclino che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Nuclino.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Nuclino, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Nuclino](#create-a-nuclino-test-user)**: per avere una controparte di Britta Simon in Nuclino collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Nuclino.

**Per configurare l'accesso Single Sign-On di Azure AD con Nuclino, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Nuclino** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/nuclino-tutorial/tutorial_nuclino_samlbase.png)

3. Nella sezione **URL e dominio Nuclino** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Nuclino](./media/nuclino-tutorial/tutorial_nuclino_url1.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore e URL di risposta effettivi dalla sezione di **autenticazione**, come descritto più avanti in questa esercitazione.

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Nuclino](./media/nuclino-tutorial/tutorial_nuclino_url2.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Contattare il [team di supporto clienti di Nuclino](mailto:contact@nuclino.com) per ottenere questo valore.

5. L'applicazione Nuclino prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configure Single Sign-On](./media/Nuclino-tutorial/tutorial_attribute.png)

6. Fare clic sulla casella di controllo **Visualizza e modifica tutti gli altri attributi utente** nella sezione **Attributi utente** per espandere gli attributi. Seguire questa procedura per ogni attributo visualizzato:

    | Nome attributo | Valore attributo |
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/nuclino-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/nuclino-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il **nome dell'attributo** indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Fare clic su **OK**.

7. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/nuclino-tutorial/tutorial_nuclino_certificate.png)

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/nuclino-tutorial/tutorial_general_400.png)

9. Nella sezione **Configurazione Nuclino** fare clic su **Configurare Nuclino** per aprire la finestra **Configura accesso** finestra. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Nuclino](./media/nuclino-tutorial/tutorial_nuclino_configure.png)

10. In un'altra finestra del Web browser accedere al sito aziendale di Nuclino come amministratore.

11. Fare clic su **ICONA**.

    ![Configurazione di Nuclino](./media/nuclino-tutorial/configure1.png)

12. Fare clic su **Azure AD SSO** e selezionare **Impostazioni team** nell'elenco a discesa.

    ![Configurazione di Nuclino](./media/nuclino-tutorial/configure2.png)

13. Selezionare **Autenticazione** nel riquadro di spostamento a sinistra.

    ![Configurazione di Nuclino](./media/nuclino-tutorial/configure3.png)

14. Nella sezione **Autenticazione** seguire questa procedura:

    ![Configurazione di Nuclino](./media/nuclino-tutorial/configure4.png)

    a. Selezionare **Accesso Single Sign-On basato su SAML**.

    b. Copiare il valore **URL ACS** (è necessario copiare e incollare questo valore nel provider SSO) e incollarlo nella casella di testo **URL di risposta** della sezione **dominio e URL di Nuclino** nel portale di Azure.

    c. Copiare il valore **ID entità** (è necessario copiare e incollare questo valore nel provider SSO) e incollarlo nella casella di testo **Identificatore** della sezione **dominio e URL di Nuclino** nel portale di Azure.

    d. Nella casella di testo **URL SSO** incollare il valore **URL servizio Single Sign-On SAML** copiato dal portale di Azure.

    e. Nella casella di testo **Entity ID** (ID entità) incollare il valore di **ID entità SAML** copiato dal portale di Azure.

    f. Aprire il file del **certificato (Base64)** scaricato nel blocco note. Copiare il contenuto negli Appunti e quindi incollarlo nella casella di testo **Certificato pubblico**.

    g. Fare clic su **SAVE CHANGES** (Salva modifiche).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/nuclino-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/nuclino-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/nuclino-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/nuclino-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-nuclino-test-user"></a>Creare un utente di test di Nuclino

Questa sezione descrive come creare un utente chiamato Britta Simon in Nuclino. Nuclino supporta il provisioning JIT, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Nuclino viene creato un nuovo utente, se questo non esiste già.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di Nuclino](mailto:contact@nuclino.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Nuclino.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a Nuclino, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **Nuclino**.

    ![Collegamento Nuclino nell'elenco delle applicazioni](./media/nuclino-tutorial/tutorial_nuclino_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Nuclino nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Nuclino.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/nuclino-tutorial/tutorial_general_01.png
[2]: ./media/nuclino-tutorial/tutorial_general_02.png
[3]: ./media/nuclino-tutorial/tutorial_general_03.png
[4]: ./media/nuclino-tutorial/tutorial_general_04.png

[100]: ./media/nuclino-tutorial/tutorial_general_100.png

[200]: ./media/nuclino-tutorial/tutorial_general_200.png
[201]: ./media/nuclino-tutorial/tutorial_general_201.png
[202]: ./media/nuclino-tutorial/tutorial_general_202.png
[203]: ./media/nuclino-tutorial/tutorial_general_203.png