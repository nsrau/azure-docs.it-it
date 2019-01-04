---
title: 'Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 3284202ffaa6767a8dd4a6a5050dbdc928075237
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846118"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online

Questa esercitazione descrive come integrare ArcGIS Online con Azure Active Directory (Azure AD).

L'integrazione di ArcGIS Online con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad ArcGIS Online.
- È possibile abilitare gli utenti per l'accesso automatico ad ArcGIS Online (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ArcGIS Online, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di ArcGIS Online abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ArcGIS Online dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-arcgis-online-from-the-gallery"></a>Aggiunta di ArcGIS Online dalla raccolta

Per configurare l'integrazione di ArcGIS Online in Azure AD, è necessario aggiungere ArcGIS Online dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ArcGIS Online dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/arcgis-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/arcgis-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/arcgis-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **ArcGIS Online** selezionare **ArcGIS Online** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ArcGIS Online usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di ArcGIS Online corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ArcGIS Online.

Per stabilire la relazione di collegamento, in ArcGIS Online assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con ArcGIS Online, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di ArcGIS Online](#create-an-arcgis-online-test-user)**: per avere una controparte di Britta Simon in ArcGIS Online collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ArcGIS Online.

**Per configurare l'accesso Single Sign-On di Azure AD con ArcGIS Online, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ArcGIS Online** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![image](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![image](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyname>.maps.arcgis.com`.

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<companyname>.maps.arcgis.com`.

    ![image](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ArcGIS Online](https://support.esri.com/en/).

6. Nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **XML metadati federazione** e quindi salvare il file XML nel computer.

    ![image](./media/arcgis-tutorial/federationxml.png)

7. Per automatizzare la configurazione all'interno di **ArcGIS Online**, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Install the extension** (Installa estensione).

    ![image](./media/arcgis-tutorial/install_extension.png)

8. Dopo aver aggiunto l'estensione al browser, fare clic su **setup ArcGIS Online** (Configura ArcGIS Online) per passare direttamente all'applicazione ArcGIS Online. Da qui, fornire le credenziali di amministratore per accedere ad ArcGIS Online. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 9 a 13.

9. Se si vuole configurare manualmente ArcGIS Online, aprire una nuova finestra del Web browser, accedere al sito aziendale di ArcGIS come amministratore e seguire questa procedura:

10. Fare clic su **EDIT SETTINGS** (Modifica impostazioni).

    ![Modificare le impostazioni](./media/arcgis-tutorial/ic784742.png "Modificare le impostazioni")

11. Fare clic su **Security**.

    ![Sicurezza](./media/arcgis-tutorial/ic784743.png "Sicurezza")

12. In **Enterprise Logins** (Accessi aziendali) fare clic su **SET IDENTITY PROVIDER** (Imposta provider di identità).

    ![Accessi aziendali](./media/arcgis-tutorial/ic784744.png "Accessi aziendali")

13. Nella pagina di configurazione **Set Identity Provider** seguire questa procedura:

    ![Impostare il provider di identità](./media/arcgis-tutorial/ic784745.png "Impostare il provider di identità")

    a. Nella casella di testo **Name** (Nome) digitare il nome della propria organizzazione.

    b. In **I metadati per il provider di identità dell'organizzazione verranno forniti mediante**, selezionare **Un File**.

    c. Per caricare il file di metadati scaricato, fare clic su **Seleziona file**.

    d. Fare clic su **SET IDENTITY PROVIDER** (Imposta provider di identità).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![image](./media/arcgis-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/arcgis-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/arcgis-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="create-an-arcgis-online-test-user"></a>Creare un utente di test di ArcGIS Online

Per consentire agli utenti di Azure AD di accedere ad ArcGIS Online, è necessario effettuarne il provisioning in ArcGIS Online.  
Nel caso di ArcGIS Online, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **ArcGIS** .

2. Fare clic su **INVITE MEMBERS** (Invita membri).
   
    ![Invitare i membri](./media/arcgis-tutorial/ic784747.png "Invitare i membri")

3. Selezionare **Add members automatically without sending an email** (Aggiungi membri automaticamente senza inviare un'e-mail) e quindi fare clic su **NEXT** (Avanti).
   
    ![Aggiungere i membri automaticamente](./media/arcgis-tutorial/ic784748.png "Aggiungere i membri automaticamente")

4. Nella finestra di dialogo **Membri** seguire questa procedura:
   
     ![Aggiungere ed esaminare](./media/arcgis-tutorial/ic784749.png "Aggiungere ed esaminare")
    
     a. Nelle caselle di testo **Email** (Posta elettronica), **First name** (Nome) e **Last name** (Cognome) digitare rispettivamente l'indirizzo di posta elettronica, il nome e il cognome relativi a un account Azure AD valido di cui si vuole effettuare il provisioning.
  
     b. Fare clic su **ADD AND REVIEW** (Aggiungi e verifica).
5. Verificare i dati immessi e quindi fare clic su **ADD MEMBERS** (Aggiungi membri).
   
    ![Aggiungere un membro](./media/arcgis-tutorial/ic784750.png "Aggiungere un membro")
        
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ArcGIS Online.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/arcgis-tutorial/d_all_applications.png)

2. Nell'elenco delle applicazioni selezionare **ArcGIS Online**.

    ![image](./media/arcgis-tutorial/d_all_application.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/arcgis-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/arcgis-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ArcGIS Online nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ArcGIS Online.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



