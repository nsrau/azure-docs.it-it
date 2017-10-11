---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.contentlocale: it-it
ms.lasthandoff: 09/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication

Questa esercitazione descrive la modalità di integrazione di SAP Cloud Platform Identity Authentication con Azure Active Directory (Azure AD). SAP Cloud Platform Identity Authentication è usato come IdP proxy per l'accesso ad applicazioni SAP usando Azure AD come IdP principale.

L'integrazione di SAP Cloud Platform Identity Authentication con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere all'Applicazione SAP.
- È possibile abilitare gli utenti per l'accesso automatico alle applicazioni SAP (SSO) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Cloud Platform Identity Authentication, è necessario quanto segue:

- Sottoscrizione di Azure AD.
- Sottoscrizione di SAP Cloud Platform Identity Authentication abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

Prima di approfondire i dettagli tecnici, è fondamentale comprendere i concetti che si desidera esaminare. La federazione di SAP Cloud Platform Identity Authentication e Azure Active Directory consente di implementare l'accesso SSO per applicazioni o servizi protetti da AAD (come IdP) con applicazioni e servizi SAP protetti da SAP Cloud Platform Identity Authentication.

Attualmente SAP Cloud Platform Identity Authentication funge da provider di identità proxy per applicazioni SAP. Azure Active Directory a sua volta agisce come provider di identità iniziale in questa configurazione. 

Il diagramma seguente illustra questo processo:

![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Con questa configurazione, il tenant di SAP Cloud Platform Identity Authentication viene configurato come applicazione attendibile in Azure Active Directory. 

Tutte le applicazioni e i servizi SAP che si desidera proteggere in questo modo vengono configurati successivamente nella console di gestione di SAP Cloud Platform Identity Authentication.

L'autorizzazione per la concessione dell'accesso a servizi e applicazioni SAP deve pertanto avvenire in SAP Cloud Platform Identity Authentication per una configurazione di questo tipo (diversamente dalla configurazione dell'autorizzazione in Azure Active Directory).

Configurando SAP Cloud Platform Identity Authentication come applicazione tramite il Marketplace di Azure Active Directory, non è necessario occuparsi di configurare le necessarie attestazioni singole/asserzioni SAML e le trasformazioni necessarie per produrre un token di autenticazione valido per le applicazioni SAP.

>[!NOTE] 
>Attualmente solo Web SSO è stato testato da entrambe le parti. I flussi necessari per la comunicazione da App a API o da API a API dovrebbero funzionare ma non sono ancora stati testati. I test verranno eseguiti come parte delle attività successive.
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta
Per configurare l'integrazione di SAP Cloud Platform Identity Authentication in Azure AD, è necessario aggiungere SAP Cloud Platform Identity Authentication dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP Cloud Platform Identity Authentication dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SAP Cloud Platform Identity Authentication** selezionare **SAP Cloud Platform Identity Authentication** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAP Cloud Platform Identity Authentication nell'elenco di risultati](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Cloud Platform Identity Authentication con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SAP Cloud Platform Identity Authentication che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud Platform Identity Authentication.

Per stabilire la relazione di collegamento, in SAP Cloud Platform Identity Authentication, assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Cloud Platform Identity Authentication, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di SAP Cloud Platform Identity Authentication](#create-an-sap-cloud-platform-identity-authentication-test-user)**: per avere in SAP Cloud Platform Identity Authentication un utente corrispondente a Britta Simon collegato alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP Cloud Platform Identity Authentication.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP Cloud Platform Identity Authentication, eseguire la procedura seguente:**

1. Nel portale di Azure, nella pagina di integrazione di **SAP Cloud Platform Identity Authentication** fare clic su **Single sign-on**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Nella sezione **Dominio e URL di SAP Cloud Platform Identity Authentication** per configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'ID effettivo. Contattare il [team di supporto di SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) per ottenere questo valore. Se non si conosce questo valore, seguire la documentazione di SAP Cloud Platform Identity Authentication in [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) (Configurazione del tenant SAML 2.0).

4. Selezionare **Mostra impostazioni URL avanzate** se si desidera configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<entity-id>.accounts.ondemand.com/admin`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Contattare il [team di supporto di SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) per ottenere questo valore.

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. L'applicazione SAP Cloud Platform Identity Authentication prevede le asserzioni SAML in un formato specifico. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. Nella sezione **Attributi utente** nella finestra di dialogo **Single Sign-On**, se l'applicazione SAP prevede un attributo, ad esempio "firstName". Nella finestra di dialogo relativa agli attributi del token SAML aggiungere l'attributo "firstName".

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo "firstName".
    
    c. Nell'elenco **Valore** selezionare il valore dell'attributo "user.givenname".
    
    d. Fare clic su **OK**.

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. Nella sezione **Configurazione di SAP Cloud Platform Identity Authentication** fare clic su **Configura SAP HANA Cloud Platform Identity Authentication** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Per configurare SSO per l'applicazione, passare alla console di amministrazione di SAP Cloud Platform Identity Authentication. L'URL ha il formato seguente: `https://<tenant-id>.accounts.ondemand.com/admin`. Seguire quindi la documentazione di SAP Cloud Platform Identity Authentication per [configurare Microsoft Azure AD come provider di identità aziendale in SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. Nel portale di Azure fare clic sul pulsante **Salva**.

12. Continuare la procedura seguente solo se si vuole aggiungere e abilitare l'accesso SSO per un'altra applicazione SAP. Ripetere i passaggi nella sezione "Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta" per aggiungere un'altra istanza di SAP Cloud Platform Identity Authentication.

13. Nel portale di Azure, nella pagina di integrazione di **SAP Cloud Platform Identity Authentication** fare clic su **Accesso collegato**.

    ![Configurare l'accesso collegato](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Salvare la configurazione.

>[!NOTE] 
>La nuova applicazione sfrutterà la configurazione del processo SSO per l'applicazione SAP precedente. Assicurarsi di usare gli stessi provider di identità aziendale nella console di amministrazione di SAP Cloud Platform Identity Authentication.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Creare un utente test di SAP Cloud Platform Identity Authentication

Non è necessario creare un utente in SAP Cloud Platform Identity Authentication. Gli utenti presenti nell'archivio utenti di Azure AD possono usare la funzionalità di accesso SSO.

SAP Cloud Platform Identity Authentication supporta l'opzione di federazione delle identità. Questa opzione consente all'applicazione di controllare se gli utenti autenticati dal provider di identità aziendale sono presenti nell'archivio utenti di SAP Cloud Platform Identity Authentication. 

Nella configurazione predefinita l'opzione di federazione delle identità è disabilitata. Se la federazione delle identità è abilitata, solo gli utenti che vengono importati in SAP Cloud Platform Identity Authentication sono in grado di accedere all'applicazione. 

Per altre informazioni su come abilitare o disabilitare la federazione delle identità con SAP Cloud Platform Identity Authentication, vedere Enable Identity Federation with SAP Cloud Platform Identity Authentication (Abilita federazione delle identità con SAP Cloud Platform Identity Authentication) in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Configura federazione delle identità con l'archivio utenti di SAP Cloud Platform Identity Authentication).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a SAP Cloud Platform Identity Authentication.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SAP Cloud Platform Identity Authentication, eseguire la procedura seguente:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SAP Cloud Platform Identity Authentication**.

    ![Collegamento di SAP HANA Cloud Platform Identity Authentication nell'elenco di applicazioni](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAP Cloud Platform Identity Authentication nel pannello di accesso, si dovrebbe ottenere automaticamente l'accesso all'applicazione SAP Cloud Platform Identity Authentication.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png


