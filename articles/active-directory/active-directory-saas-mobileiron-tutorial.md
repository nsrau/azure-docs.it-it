---
title: 'Esercitazione: Integrazione di Azure Active Directory con MobileIron | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: jeedes
ms.openlocfilehash: 214cc59b0a0d1cb55758e11f7fa87c8457531113
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Esercitazione: integrazione di Azure Active Directory con MobileIron

Questa esercitazione descrive come integrare MobileIron con Azure Active Directory (Azure AD).

L'integrazione di MobileIron con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a MobileIron.
- È possibile abilitare gli utenti per l'accesso automatico a MobileIron (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con MobileIron, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di MobileIron abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di MobileIron dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-mobileiron-from-the-gallery"></a>Aggiunta di MobileIron dalla raccolta
Per configurare l'integrazione di MobileIron in Azure AD, è necessario aggiungere MobileIron dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere MobileIron dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **MobileIron**, selezionare **MobileIron** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![MobileIron nell'elenco dei risultati](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con MobileIron con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di MobileIron che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MobileIron.

Per stabilire la relazione di collegamento, in MobileIron assegnare il valore di **nome utente** in Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con MobileIron, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di MobileIron](#create-a-mobileiron-test-user)**: per avere una controparte di Britta Simon in MobileIron collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione MobileIron.

**Per configurare l'accesso Single Sign-On di Azure AD con MobileIron, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **MobileIron** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_samlbase.png)

3. Nella sezione **URL e dominio MobileIron** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On di URL e dominio MobileIron](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.mobileiron.com/<key>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Accesso Single Sign-On di URL e dominio MobileIron](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<host>.mobileiron.com/user/login.html`
    
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterranno i valori di chiave e host dal portale di amministrazione di MobileIron illustrato più avanti nell'esercitazione.

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-mobileiron-tutorial/tutorial_general_400.png)

7. In un'altra finestra del Web browser accedere al sito aziendale di MobileIron come amministratore.

8. Passare ad **Admin** > **Identità**.

   * Selezionare l'opzione **AAD** nel campo **Info on Cloud IDP Setup** (Informazioni sulla configurazione del provider di identità cloud).

    ![Pulsante Admin nella configurazione dell'accesso Single Sign-On](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_admin.png)

9. Copiare i valori di **Chiave** e **Host** e incollarli per completare gli URL nella sezione **URL e dominio MobileIron** nel portale di Azure.

    ![Pulsante Admin nella configurazione dell'accesso Single Sign-On](./media/active-directory-saas-mobileiron-tutorial/key.png)

10. Nel campo **Export metadata file from AAD and import to MobileIron Cloud** (Esporta file di metadati da AAD e importa nel cloud di MobileIron) fare clic su **Scegli file** per caricare i metadati scaricati dal portale di Azure. Dopo il caricamento fare clic su **Fatto**.
 
    ![Pulsante per i metadati amministratore nella configurazione dell'accesso Single Sign-On](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-mobileiron-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-mobileiron-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-mobileiron-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-mobileiron-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-mobileiron-test-user"></a>Creare un utente di test di MobileIron

Per consentire agli utenti di Azure AD di accedere a MobileIron, è necessario eseguire il provisioning degli utenti in MobileIron.  
Nel caso di MobileIron, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di MobileIron come amministratore.

2. Passare a **Utenti** e fare clic su **Aggiungi** > **Utente singolo**.

    ![Pulsante Utenti nella configurazione dell'accesso Single Sign-On](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_user.png)

3. Nella pagina **"Utente singolo"** seguire questa procedura:

    ![Pulsante per l'aggiunta di utenti nella configurazione dell'accesso Single Sign-On](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    b. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio Britta.

    c. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio Simon.
    
    d. Fare clic su **Done**.  

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MobileIron.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a MobileIron seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **MobileIron**.

    ![Collegamento MobileIron nell'elenco delle applicazioni](./media/active-directory-saas-mobileiron-tutorial/tutorial_mobileiron_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro MobileIron nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione MobileIron.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mobileiron-tutorial/tutorial_general_203.png

