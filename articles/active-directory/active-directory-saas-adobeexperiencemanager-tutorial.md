---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Experience Manager | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 4ba94f0f24b2791b3b32807aa60379aeadb79365
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Experience Manager

Questa esercitazione descrive come integrare Adobe Experience Manager con Azure Active Directory (Azure AD).

L'integrazione di Adobe Experience Manager con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Adobe Experience Manager.
- È possibile abilitare per gli utenti l'accesso automatico ad Adobe Experience Manager (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Experience Manager, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Adobe Experience Manager abilitata per Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Adobe Experience Manager dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Aggiunta di Adobe Experience Manager dalla raccolta
Per configurare l'integrazione di Adobe Experience Manager in Azure AD, è necessario aggiungere Adobe Experience Manager dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Adobe Experience Manager dalla raccolta, completare questi passaggi:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Adobe Experience Manager**, selezionare **Adobe Experience Manager** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Adobe Experience Manager nell'elenco dei risultati](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Experience Manager usando un utente di test di nome "Britta Simon".

Ai fini del funzionamento dell'accesso Single Sign-On, Azure AD deve identificare l'utente controparte in Adobe Experience Manager di un utente in Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Experience Manager.

In Adobe Experience Manager assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente) per stabilire una relazione di collegamento.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adobe Experience Manager, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Adobe Experience Manager](#create-an-adobe-experience-manager-test-user)** per avere una controparte di Britta Simon in Adobe Experience Manager collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Adobe Experience Manager.

**Per configurare l'accesso Single Sign-On di Azure AD con Adobe Experience Manager, completare questi passaggi:**

1. Nella pagina di integrazione dell'applicazione **Adobe Experience Manager** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Nella sezione **URL e dominio Adobe Experience Manager** completare i passaggi seguenti se si vuole configurare l'app in modalità **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Nella casella di testo **Identificatore** digitare un valore univoco definito anche nel server Adobe Experience Manager. 

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<AEM Server Url>/saml_login`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html).
 
4. Selezionare Mostra impostazioni URL avanzate e completare il passaggio seguente se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Nella casella di testo **URL di accesso** digitare l'URL del server Adobe Experience Manager. 

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Nella sezione Configurazione di Adobe Experience Manager fare clic su Configura Adobe Experience Manager per aprire la finestra Configura accesso. Copiare i valori di **URL servizio Single Sign-On SAML**, **ID entità SAML** e **URL di disconnessione** dalla sezione Riferimento rapido.

    ![Collegamento della sezione Configurazione](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Aprire il portale di amministrazione di **Adobe Experience Manager** in un'altra finestra del browser.

9. Selezionare **Settings** (Impostazioni) -> **Security** (Sicurezza) -> **Users** (Utenti).

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Selezionare **Administrator** (Amministratore) o un altro utente rilevante.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Selezionare **Account Settings** (Impostazioni account) -> **Create/Manage TrustStore** (Crea/Gestisci archivio trust).

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Fare clic sul pulsante **Select Certificate File** (Seleziona file di certificato) in **Add Certificate from CER file** (Aggiungi certificato da file CER). Individuare e selezionare il file di certificato, scaricato dal portale di Azure.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Il certificato viene aggiunto all'archivio trust. Notare l'alias del certificato.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Nella pagina **Users** (Utenti) selezionare **authentication-service**.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Selezionare **Account Settings** (Impostazioni account) -> **Create/Manage KeyStore** (Crea/Gestisci archivio chiavi). Creare l'archivio chiavi specificando una password.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Tornare alla schermata di amministrazione e selezionare **Settings** (Impostazioni) -> **Operations** (Operazioni) -> **Web Console** (Console Web).

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

17. Viene visualizzata la pagina di configurazione.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

18. Trovare **Adobe Granite SAML 2.0 Authentication Handler** e fare clic sull'icona **Add** (Aggiungi).

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. In questa pagina eseguire le operazioni seguenti.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Nella casella di testo **Path** (Percorso) immettere **/**.

    b. Nella casella di testo **IDP URL** (URL IDP) incollare il valore di **URL servizio Single Sign-On SAML**, copiato dal portale di Azure.

    c. Nella casella di testo **IDP Certificate Alias** (Alias certificato IDP) immettere il valore di **Certificate Alias** (Alias certificato), aggiunto nell'archivio trust.

    d. Nella casella di testo **Service Provider Entity ID** (ID entità provider di servizi) incollare il valore univoco di **ID di entità SAML**, configurato nel portale di Azure.

    e. Nella casella di testo **Assertion Consumer Service URL** (URL servizio consumer di asserzione) immettere il valore di **URL di risposta**, configurato nel portale di Azure.

    f. Nella casella di testo **Password of Key Store** (Password archivio chiavi) immettere il valore di **Password**, impostato nell'archivio chiavi.

    g. Nella casella di testo **User Attribute ID** (ID attributo utente) immettere l'**ID nome** o un altro ID utente rilevante per questo caso.

    h. Selezionare **Autocreate CRX Users** (Creazione automatica utenti CRX).

    i. Nella casella di testo **Logout URL** (URL di disconnessione) immettere il valore univoco di **URL di disconnessione** dal portale di Azure.

    j. Fare clic su **Save**

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Creare un utente di test di Adobe Experience Manager

In questa sezione viene creato un utente di nome Britta Simon in Adobe Experience Manager. Se è stata selezionata l'opzione **Autocreate CRX Users** (Creazione automatica utenti CRX), gli utenti verranno creati automaticamente dopo l'autenticazione. 

Se si vuole creare gli utenti manualmente, rivolgersi al [team di supporto di Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) per aggiungere gli utenti alla piattaforma Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso ad Adobe Experience Manager per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Adobe Experience Manager, completare questi passaggi:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Adobe Experience Manager**.

    ![Collegamento di Adobe Experience Manager nell'elenco delle applicazioni](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Adobe Experience Manager nel pannello di accesso, dovrebbe essere possibile accedere automaticamente all'applicazione Adobe Experience Manager.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

