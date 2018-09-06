---
title: 'Esercitazione: Integrazione di Azure Active Directory con ArcGIS Enterprise | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: ea2b32b43fedacba7b8a60db29762c32fda65aa5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306343"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS Enterprise

Questa esercitazione descrive come integrare ArcGIS Enterprise con Azure Active Directory (Azure AD).

L'integrazione di ArcGIS Enterprise con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad ArcGIS Enterprise.
- È possibile abilitare gli utenti per l'accesso automatico a ArcGIS Enterprise (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ArcGIS Enterprise, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di ArcGIS Enterprise abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ArcGIS Enterprise dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Aggiunta di ArcGIS Enterprise dalla raccolta

Per configurare l'integrazione di ArcGIS Enterprise in Azure AD, è necessario aggiungere ArcGIS Enterprise dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ArcGIS Enterprise dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **ArcGIS Enterprise** selezionare **ArcGIS Enterprise** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![ArcGIS Enterprise nell'elenco risultati](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ArcGIS Enterprise con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di ArcGIS Enterprise che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ArcGIS Enterprise.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ArcGIS Enterprise, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di ArcGIS Enterprise](#create-an-arcgis-enterprise-test-user)**: per avere una controparte di Britta Simon in ArcGIS Enterprise collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ArcGIS Enterprise.

**Per configurare l'accesso Single Sign-On di Azure AD con ArcGIS Enterprise, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ArcGIS Enterprise** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_samlbase.png)

3. Nella sezione **URL e dominio ArcGIS Enterprise**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Informazioni su URL e dominio ArcGIS Enterprise per l'accesso Single Sign-On](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url1.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `<EXTERNAL_DNS_NAME>.portal`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio ArcGIS Enterprise per l'accesso Single Sign-On](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url2.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ArcGIS Enterprise](mailto:support@esri.com). Si otterrà il valore dell'identificatore dalla sezione **Set Identity Provider** (Imposta provider di identità). La procedura è descritta più avanti in questa esercitazione.

5. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_certificate.png)

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/arcgisenterprise-tutorial/tutorial_general_400.png)

7. In un'altra finestra del Web browser accedere al sito aziendale di ArcGIS Enterprise come amministratore.

8. Selezionare **Organization >EDIT SETTINGS** (Organizzazione > MODIFICA IMPOSTAZIONI).

    ![Configurazione di ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure1.png)

9. Selezionare la scheda **Security** (Sicurezza).

    ![Configurazione di ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure2.png)

10. Scorrere verso il basso alla sezione **Enterprise Logins via SAML** e selezionare **SET ENTERPRISE LOGIN** (IMPOSTA ACCESSO ENTERPRISE).

    ![Configurazione di ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure3.png)

11. Nella sezione **Set Identity Provider** (Imposta provider di identità) eseguire questa procedura:

    ![Configurazione di ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure4.png)

    a. Specificare un nome simile a **Test di Azure Active Directory** nella casella di testo **Nome**.

    b. Nella casella di testo **URL** incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Fare clic su **Mostra impostazioni avanzate**, copiare il valore **ID entità**e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio ArcGIS Enterprise** nel portale di Azure.
    
    ![Configurazione di ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure5.png)

    d. Fare clic su **AGGIORNA PROVIDER DI IDENTITÀ**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/arcgisenterprise-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/arcgisenterprise-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/arcgisenterprise-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/arcgisenterprise-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-an-arcgis-enterprise-test-user"></a>Creare un utente test di ArcGIS Enterprise

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in ArcGIS Enterprise. ArcGIS Enterprise supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso ad ArcGIS Enterprise viene creato un nuovo utente, se questo non esiste già.

> [!Note]
> Per creare un utente manualmente contattare il [team di supporto di ArcGIS Enterprise](mailto:support@esri.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ArcGIS Enterprise.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a ArcGIS Enterprise, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **ArcGIS Enterprise**.

    ![Collegamento di ArcGIS Enterprise nell'elenco delle applicazioni](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ArcGIS Enterprise nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ArcGIS Enterprise.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgisenterprise-tutorial/tutorial_general_01.png
[2]: ./media/arcgisenterprise-tutorial/tutorial_general_02.png
[3]: ./media/arcgisenterprise-tutorial/tutorial_general_03.png
[4]: ./media/arcgisenterprise-tutorial/tutorial_general_04.png

[100]: ./media/arcgisenterprise-tutorial/tutorial_general_100.png

[200]: ./media/arcgisenterprise-tutorial/tutorial_general_200.png
[201]: ./media/arcgisenterprise-tutorial/tutorial_general_201.png
[202]: ./media/arcgisenterprise-tutorial/tutorial_general_202.png
[203]: ./media/arcgisenterprise-tutorial/tutorial_general_203.png