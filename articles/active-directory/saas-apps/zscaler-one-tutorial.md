---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler One | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.openlocfilehash: c3160f86619a6eb7e8bec7bf6e1663fc9b139852
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156854"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler One

Questa esercitazione descrive come integrare Zscaler One con Azure Active Directory (Azure AD).

L'integrazione di Zscaler One con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Zscaler One.
- È possibile abilitare gli utenti per l'accesso automatico a Zscaler One (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler One, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Zscaler One abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Zscaler One dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-zscaler-one-from-the-gallery"></a>Aggiunta di Zscaler One dalla raccolta

Per configurare l'integrazione di Zscaler One in Azure AD, è necessario aggiungere Zscaler One dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Zscaler One dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Zscaler One**, selezionare **Zscaler One** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler One nell'elenco risultati](./media/zscaler-one-tutorial/tutorial_zscalerone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zscaler One usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Zscaler One corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler One.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zscaler One, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Zscaler One](#creating-a-zscaler-one-test-user)**: per avere una controparte di Britta Simon in Zscaler One collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Zscaler One.

**Per configurare l'accesso Single Sign-On di Azure AD con Zscaler One, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Zscaler One** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Zscaler One](./media/zscaler-one-tutorial/tutorial_zscalerone_url.png)

    Nella casella di testo dell'URL di accesso digitare l'URL usato dagli utenti per accedere all'applicazione Zscaler One.

    > [!NOTE] 
    > È necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questi valori contattare il [team di supporto clienti di Zscaler One](https://www.zscaler.com/company/contact).

5. L'applicazione Zscaler One prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente e attestazioni** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente e attestazioni**.

    ![Collegamento Attributi](./media/zscaler-one-tutorial/tutorial_zscalerone_attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | NOME  | Source Attribute  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo.

    c. Fare clic su **OK**.

    d. Fare clic su **Save**.

    > [!NOTE]
    > Fare clic [qui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) per sapere come configurare un Ruolo in Azure AD

7. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Certificato (Base64)** e salvare il file di certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/zscaler-one-tutorial/tutorial_zscalerone_certificate.png) 

8. Nella sezione **Configura Zscaler One** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di Zscaler One](common/configuresection.png)

9. In un'altra finestra del Web browser accedere al sito aziendale di Zscaler One come amministratore.

10. Passare a **Amministrazione > Autenticazione > Impostazioni di autenticazione** ed eseguire i passaggi seguenti:
   
    ![Amministrazione](./media/zscaler-one-tutorial/ic800206.png "Amministrazione")

    a. In Tipo di autenticazione scegliere **SAML**.

    b. Fare clic su **Configure SAML**.

11. Nella finestra **Modifica SAML** eseguire i passaggi seguenti: e fare clic su Salva.  
            
    ![Gestire utenti e autenticazione](./media/zscaler-one-tutorial/ic800208.png "Gestire utenti e autenticazione")
    
    a. Nella casella di testo **URL portale SAML** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Attributo del nome di accesso** immettere **NameID**.

    c. Fare clic su **Carica** per caricare il certificato di firma di Azure SAML scaricato dal portale di Azure nel **certificato pubblico SSL**.

    d. Selezionare **Abilita provisioning automatico SAML**.

    e. Nella casella di testo **Attributo nome visualizzato dell'utente** immettere **displayName** se si desidera abilitare il provisioning automatico di SAML per gli attributi displayName.

    f. Nella casella di testo **Attributo nome gruppo** immettere **memberOf** se si desidera abilitare il provisioning automatico di SAML per gli attributi memberOf.

    g. Nella casella di testo **Attributo nome reparto** immettere **department** se si desidera abilitare il provisioning automatico di SAML per gli attributi department.

    i. Fare clic su **Save**.

12. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:

    ![Administration](./media/zscaler-one-tutorial/ic800207.png)

    a. Passare il mouse sul menu **Attivazione** nella parte inferiore sinistra.

    b. Fare clic su **Attiva**.

## <a name="configuring-proxy-settings"></a>Configurazione delle impostazioni proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni proxy in Internet Explorer

1. Avviare **Internet Explorer**.

1. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.   
    
     ![Opzioni Internet](./media/zscaler-one-tutorial/ic769492.png "Opzioni Internet")

1. Fare clic sulla scheda **Connessioni** .   
  
     ![Connessioni](./media/zscaler-one-tutorial/ic769493.png "Connessioni")

1. Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.

1. Nella sezione del server proxy seguire questa procedura:   
   
    ![Server proxy](./media/zscaler-one-tutorial/ic769494.png "Server proxy")

    a. Selezionare **Usa un server proxy per la LAN**.

    b. Nella casella di testo Indirizzo digitare **gateway.Zscaler One.net**.

    c. Nella casella di testo Porta digitare **80**.

    d. Selezionare **Ignora server proxy per indirizzi locali**.

    e. Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)**.

1. Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

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

### <a name="creating-a-zscaler-one-test-user"></a>Creazione di un utente di test di Zscaler One

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Zscaler One. Zscaler One supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Zscaler One viene creato un nuovo utente, se non esiste già.
>[!Note]
>Se è necessario creare manualmente un utente contattare il  [team di supporto di Zscaler One](https://www.zscaler.com/company/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zscaler One.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Zscaler One**.

    ![Configure Single Sign-On](./media/zscaler-one-tutorial/tutorial_zscalerone_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare l'utente **Britta Simon** nell'elenco e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_users.png)

6. Nella finestra di dialogo **Seleziona ruolo** scegliere il ruolo utente appropriato dall'elenco e fare clic sul pulsante **Seleziona** in basso nella schermata.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_roles.png)

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

    ![image](./media/zscaler-one-tutorial/tutorial_zscalerone_assign.png)

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Zscaler One nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zscaler One.
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
