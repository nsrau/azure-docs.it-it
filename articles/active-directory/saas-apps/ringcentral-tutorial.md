---
title: 'Esercitazione: Integrazione di Azure Active Directory con RingCentral | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 35033e52fb54177428f8869ebcc462bd9465ad4c
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48872319"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Esercitazione: Integrazione di Azure Active Directory con RingCentral

Questa esercitazione descrive come integrare RingCentral con Azure Active Directory (Azure AD).

L'integrazione di RingCentral con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a RingCentral.
- È possibile abilitare gli utenti per l'accesso automatico a RingCentral (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con RingCentral, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione a RingCentral abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di RingCentral dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-ringcentral-from-the-gallery"></a>Aggiunta di RingCentral dalla raccolta
Per configurare l'integrazione di RingCentral in Azure AD, è necessario aggiungere RingCentral dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere RingCentral dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **RingCentral**, selezionare **RingCentral** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con RingCentral in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di RingCentral che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RingCentral.

Per configurare e testare l'accesso Single Sign-On di Azure AD con RingCentral, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di RingCentral](#create-a-ringcentral-test-user)**: per avere una controparte di Britta Simon in RingCentral collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione RingCentral.

**Per configurare il Single Sign-On di Azure AD con RingCentral, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **RingCentral** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. Nella sezione **Configurazione SAML di base**, se si dispone di un **file di metadati di un provider di servizi**, seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. Dopo che è stato caricato il file di metadati, i valori **Identificatore** e **URL di risposta** vengono popolati automaticamente nella casella di testo della sezione **Configurazione SAML di base**, come illustrato di seguito:

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. Nella casella di testo **URL di accesso** digitare un URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > Il **file di metadati del provider di servizi** sarà disponibile nella pagina di configurazione SSO di RingCentral, come spiegato più avanti nell'esercitazione.

6. Se non si dispone del **file di metadati del provider di servizi**, seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. Nella casella di testo **Identificatore** digitare un URL:
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. Nella casella di testo **URL di risposta** digitare un URL:
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il certificato definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. In un'altra finestra del Web browser accedere a RingCentral come amministratore della sicurezza.

9. In alto fare clic su **Tools** (Strumenti).

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. Passare a **Single Sign-on**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. Nella pagina **Single Sign-on**, nella sezione **SSO Configuration** (Configurazione SSO), fare clic sul pulsante **Edit** (Modifica) relativo a **Step 1** (Passaggio 1) e seguire questa procedura:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. Nella pagina **Set up Single Sign-on** (Configura Single Sign-On) seguire questa procedura:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Fare clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.

    b. Dopo il caricamento dei metadati, i valori vengono popolati automaticamente nella sezione **SSO General Information** (Informazioni generali SSO).

    c. Nella sezione **Attribute Mapping** (Mapping attributi), in **Map Email Attribute to** (Esegui mapping attributo posta elettronica in) selezionare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Fare clic su **Save**.

    e. In **Step 2** (Passaggio 2) fare clic su **Download** (Scarica) per scaricare il **file di metadati del provider di servizi** e caricarlo nella sezione **Configurazione SAML di base** per popolare automaticamente i valori **Identificatore** e **URL di risposta** nel portale di Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Nella stessa pagina, passare alla sezione **Enable SSO** (Abilita SSO) e seguire questa procedura:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    a. Selezionare **Enable SSO Service** (Abilita servizio SSO).
    
    b. Selezionare **Allow users to log in with SSO or RingCentral credential** (Consenti agli utenti di accedere con le credenziali SSO o RingCentral).

    c. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-a-ringcentral-test-user"></a>Creare un utente di test RingCentral

In questa sezione viene creato un utente chiamato Britta Simon in RingCentral. Collaborare con il [team di supporto clienti di RingCentral](https://success.ringcentral.com/RCContactSupp) per aggiungere gli utenti alla piattaforma RingCentral. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RingCentral.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **RingCentral**.

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Fare clic sul pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro RingCentral nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RingCentral.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

