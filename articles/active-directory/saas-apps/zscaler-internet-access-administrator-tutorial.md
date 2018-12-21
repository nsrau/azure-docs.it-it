---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Internet Access Administrator | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler Internet Access Administrator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/7/2018
ms.author: jeedes
ms.openlocfilehash: 1a28a4f8dd5087377d559c50ef3432251510810b
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195021"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler Internet Access Administrator

Questa esercitazione descrive come integrare Zscaler Internet Access Administrator con Azure Active Directory (Azure AD).
L'integrazione di Zscaler Internet Access Administrator con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Zscaler Internet Access Administrator.
* È possibile abilitare gli utenti per l'accesso automatico a Zscaler Internet Access Administrator (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler Internet Access Administrator sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Una sottoscrizione attiva con accesso Single Sign-on per Zscaler Internet Access Administrator

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zscaler Internet Access Administrator supporta SSO avviato da **IDP**

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Aggiunta di Zscaler Internet Access Administrator dalla raccolta

Per configurare l'integrazione di Zscaler Internet Access Administrator in Azure AD è necessario aggiungere Zscaler Internet Access Administrator dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Zscaler Internet Access Administrator dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, digitare **Zscaler Internet Access Administrator**, selezionare **Zscaler Internet Access Administrator** dal pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Zscaler Internet Access Administrator nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zscaler Internet Access Administrator in base a un utente test di nome **Britta Simon**.
Per il funzionamento corretto di Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler Internet Access Administrator.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zscaler Internet Access Administrator è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Zscaler Internet Access Administrator](#configure-zscaler-internet-access-administrator-single-sign-on)**: per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creare un utente test di Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)**: per avere una controparte di Britta Simon in Zscaler Internet Access Administrator collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Zscaler Internet Access Administrator, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [Zscaler Internet Access Administrator](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Informazioni sull'accesso Single Sign-On per domini e URL Zscaler Internet Access Administrator](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL in base alle esigenze:
    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` | 
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Nella casella di testo **URL di risposta** digitare un URL in base alle esigenze:
    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` | 
    | `https://admin.zscalertwo.net/adminsso.do` | 
    | `https://admin.zscalerthree.net/adminsso.do` | 
    | `https://admin.zscloud.net/adminsso.do` | 
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. L'applicazione Zscaler Internet Access Administrator prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente e attestazioni** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente e attestazioni**.

    ![Collegamento Attributi](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | NOME  | Source Attribute  |
    | ---------| ------------ |
    | Ruolo     | user.assignedroles |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo.

    c. Fare clic su **OK**.

    d. Fare clic su **Save**.

    > [!NOTE]
    > Fare clic [qui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) per sapere come configurare un Ruolo in Azure AD

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Zscaler Internet Access Administrator** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Configurare l'accesso Single Sign-On per Zscaler Internet Access Administrator

9. In un'altra finestra del Web browser accedere al sito aziendale di Zscaler Internet Access Administrator come amministratore.

10. Passare a **Amministrazione > Autenticazione > Impostazioni di autenticazione** ed eseguire i passaggi seguenti:
   
    ![Amministrazione](./media/zscaler-internet-access-administrator-tutorial/ic800206.png "Amministrazione")

    a. In Tipo di autenticazione scegliere **SAML**.

    b. Fare clic su **Configure SAML**.

11. Nella finestra **Modifica SAML** eseguire i passaggi seguenti e fare clic su Salva.  
            
    ![Gestire utenti e autenticazione](./media/zscaler-internet-access-administrator-tutorial/ic800208.png "Gestire utenti e autenticazione")
    
    a. Nella casella di testo **URL portale SAML** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Attributo del nome di accesso** immettere **NameID**.

    c. Fare clic su **Carica** per caricare il certificato di firma di Azure SAML scaricato dal portale di Azure nel **certificato pubblico SSL**.

    d. Selezionare **Abilita provisioning automatico SAML**.

    e. Nella casella di testo **Attributo nome visualizzato dell'utente** immettere **displayName** se si desidera abilitare il provisioning automatico di SAML per gli attributi displayName.

    f. Nella casella di testo **Attributo nome gruppo** immettere **memberOf** se si desidera abilitare il provisioning automatico di SAML per gli attributi memberOf.

    g. Nella casella di testo **Attributo nome reparto** immettere **department** se si desidera abilitare il provisioning automatico di SAML per gli attributi department.

    i. Fare clic su **Save**.

12. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:

    ![Administration](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Passare il mouse sul menu **Attivazione** nella parte inferiore sinistra.

    b. Fare clic su **Attiva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a Zscaler Internet Access Administrator.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Zscaler Internet Access Administrator**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, digitare e selezionare **Zscaler Internet Access Administrator**.

    ![Collegamento a Zscaler Internet Access Administrator nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Creare un utente di test di Zscaler Internet Access Administrator

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Zscaler Internet Access Administrator. Zscaler Internet Access Administrator supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Zscaler Internet Access Administrator viene creato un nuovo utente, se questo non esiste già.
>[!Note]
>Se è necessario creare manualmente un utente contattare il  [team di supporto di Zscaler Internet Access Administrator](Phone: +1-408-701-0534 URL: help.zscaler.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Zscaler Internet Access Administrator nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zscaler Internet Access Administrator per cui si è impostato SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

