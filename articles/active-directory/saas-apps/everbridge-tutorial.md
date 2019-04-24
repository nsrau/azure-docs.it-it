---
title: 'Esercitazione: Integrazione di Azure Active Directory con EverBridge | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1bb62c9a11971f72a6c96c4652b136c19812cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60279268"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Esercitazione: Integrazione di Azure Active Directory con EverBridge

Questa esercitazione descrive come integrare EverBridge con Azure Active Directory (Azure AD).

L'integrazione di EverBridge con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a EverBridge.
- È possibile abilitare gli utenti per l'accesso automatico Single Sign-On a EverBridge con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con EverBridge sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di EverBridge abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di EverBridge dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-everbridge-from-the-gallery"></a>Aggiunta di EverBridge dalla raccolta

Per configurare l'integrazione di EverBridge in Azure AD è necessario aggiungere EverBridge dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere EverBridge dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **EverBridge**, selezionare **EverBridge** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![EverBridge nell'elenco risultati](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con EverBridge mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di EverBridge corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in EverBridge.

Per configurare e testare l'accesso Single Sign-On di Azure AD con EverBridge è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di EverBridge](#creating-an-everbridge-test-user)**: per avere una controparte di Britta Simon in EverBridge collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione EverBridge.

**Per configurare l'accesso Single Sign-On di Azure AD con EverBridge, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **EverBridge** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

    >[!NOTE]
    >È necessario effettuare le configurazioni dell'applicazione come portale di gestione o come portale membri a entrambe le estremità, ovvero nel portale di Azure e nel portale Everbridge.

4. Per configurare l'applicazione **EverBridge** come **portale di gestione di EverBridge** nella sezione **Configurazione SAML di base** eseguire la procedura seguente:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://sso.everbridge.net/<API_Name>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto di EverBridge](mailto:support@everbridge.com).

5. Per configurare l'applicazione **EverBridge** come **portale membri di EverBridge** nella sezione **Configurazione SAML di base** eseguire la procedura seguente:

   * Se si vuole configurare l'applicazione in modalità avviata da **IDP**:

       ![Informazioni sull'accesso Single Sign-On per URL e dominio di EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

       * Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

       * Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

       ![Informazioni sull'accesso Single Sign-On per URL e dominio di EverBridge](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

       * Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto di EverBridge](mailto:support@everbridge.com).

6. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Metadati federativi XML** e salvare il file di metadati nel computer in uso.

    ![Collegamento di download del certificato](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. Nella sezione **Configura EverBridge** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di EverBridge](common/configuresection.png)

8. Per ottenere l'accesso Single Sign-On configurato per **EverBridge** come applicazione del **portale di gestione di EverBridge**, procedere come segue: 
 
9. In un'altra finestra del Web browser accedere a EverBridge come amministratore.

9. Nel menu in alto fare clic sulla scheda **Settings** (Impostazioni) e selezionare **Single Sign-On** sotto **Security** (Sicurezza).
   
     ![Configure Single Sign-On](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Nella casella di testo **Nome** digitare il nome del provider di identità (ad esempio, il nome della propria società).
   
     b. Nella casella di testo **API Name** (Nome API) digitare il nome dell'API.
   
     c. Fare clic sul pulsante **Choose File** (Scegli file) per caricare il file di metadati scaricato dal portale di Azure.
   
     d. In SAML Identity Location (Percorso identità SAML) selezionare **Identity is in the NameIdentifier element of the Subject statement** (L'identità è nell'elemento NameIdentifier dell'istruzione Subject).
   
     e. Nella casella di testo **URL di accesso del provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
     f. Per Service Provider Initiated Request Binding (Binding richiesta avviato dal provider di servizi), selezionare **HTTP Redirect** (Reindirizzamento HTTP).

     g. Fare clic su **Save** (Salva).

10. Per configurare l'accesso single sign-on nelle applicazioni **EverBridge** come **portale membri EverBridge**, è necessario inviare il file **XML dei metadati di federazione** al [ team di supporto di Everbridge](mailto:support@everbridge.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
  
### <a name="creating-an-everbridge-test-user"></a>Creazione di un utente test di EverBridge

In questa sezione viene creato un utente chiamato Britta Simon in Everbridge. Collaborare con il [team di supporto di EverBridge](mailto:support@everbridge.com) per aggiungere gli utenti nella piattaforma EverBridge.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a EverBridge.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **EverBridge**.

    ![Configure Single Sign-On](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro EverBridge nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione EverBridge.
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
