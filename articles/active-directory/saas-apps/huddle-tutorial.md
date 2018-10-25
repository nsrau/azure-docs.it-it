---
title: 'Esercitazione: Integrazione di Azure Active Directory con Huddle | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Huddle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 050a5a210ab10a557b149513a0416b188f4618ba
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885904"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Esercitazione: Integrazione di Azure Active Directory con Huddle

Questa esercitazione descrive come integrare Huddle con Azure Active Directory (Azure AD).

L'integrazione di Huddle con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Huddle
- È possibile abilitare gli utenti per l'accesso automatico a Huddle (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Huddle, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Huddle abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Huddle dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-huddle-from-the-gallery"></a>Aggiunta di Huddle dalla raccolta

Per configurare l'integrazione di Huddle in Azure AD, è necessario aggiungere Huddle dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Huddle dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Huddle**. Nel pannello dei risultati selezionare **Huddle** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Huddle usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Huddle corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Huddle.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Huddle, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Huddle](#creating-a-huddle-test-user)**: per avere una controparte di Britta Simon in Huddle collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Huddle.

**Per configurare l'accesso Single Sign-On di Azure AD con Huddle, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Huddle** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

    ![Configure Single Sign-On](./media/huddle-tutorial/tutorial_general_300.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare il Single Sign-On.

    ![Configure Single Sign-On](./media/huddle-tutorial/tutorial_general_301.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic su **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](./media/huddle-tutorial/tutorial_general_302.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    > [!NOTE]
    > L'istanza di Huddle verrà automaticamente rilevata dal dominio specificate di seguito.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Huddle](./media/huddle-tutorial/tutorial_huddle_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL:

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. Nella casella di testo **URL di risposta** digitare un URL:

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Huddle](./media/huddle-tutorial/tutorial_huddle_url1.png)

    Nella casella di testo **URL di accesso** digitare uno degli URL usando il modello seguente:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > Poiché il valore dell'URL di accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Huddle](https://huddle.zendesk.com).

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il certificato appropriato in base ai propri requisiti e salvarlo nel computer in uso.

    ![Configure Single Sign-On](./media/huddle-tutorial/tutorial_huddle_certificate.png)

7. Nella sezione **Set up Huddle** (Configura Huddle) copiare gli URL appropriati in base alle proprie esigenze.

    ![Configure Single Sign-On](./media/huddle-tutorial/tutorial_huddle_configure.png)

8. Per configurare l'accesso Single Sign-On sul lato **Huddle**, è necessario inviare il certificato scaricato e gli URL copiati dalla sezione **Set up** **Huddle** (Configura Huddle) del portale di Azure al [team di supporto clienti di Huddle](https://huddle.zendesk.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

    >[!NOTE]
    > L'accesso Single Sign-On deve essere abilitato dal team di supporto di Huddle. Al termine della configurazione verrà visualizzata una notifica.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](./media/huddle-tutorial/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](./media/huddle-tutorial/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-a-huddle-test-user"></a>Creazione di un utente di test di Huddle

Per consentire agli utenti di Azure AD di accedere a Huddle, è necessario effettuarne il provisioning in Huddle. Nel caso di Huddle, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **Huddle** come amministratore.

2. Fare clic su **Area di lavoro**.

3. Fare clic su **Persone \> Invite People (Invita persone)**.

    ![Persone](./media/huddle-tutorial/IC787838.png "Persone")

4. Nella sezione **Create a new invitation** seguire questa procedura:
  
    ![Nuovo invito](./media/huddle-tutorial/IC787839.png "Nuovo invito")
  
    a. Nell'elenco **Scegli un team per invitare persone a partecipare** selezionare **team**.

    b. Nella casella di testo **Enter email address for people you'd like to invite** (Immetti indirizzo di posta elettronica degli utenti da invitare) digitare l'**indirizzo di posta elettronica** di un account Azure AD valido di cui si vuole effettuare il provisioning.

    c. Fare clic su **Invita**.

    >[!NOTE]
    > Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Huddle per effettuare il provisioning degli account utente di Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Huddle.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **Huddle**.

    ![Configure Single Sign-On](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Huddle nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Huddle.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
