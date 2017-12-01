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
ms.openlocfilehash: 591fadb7a218802ec5685d3bd5886ecded1f9920
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication

Questa esercitazione descrive la modalità di integrazione di SAP Cloud Platform Identity Authentication con Azure Active Directory (Azure AD). SAP Cloud Platform Identity Authentication è usato come IdP proxy per l'accesso ad applicazioni SAP che usano Azure AD come IdP principale.

L'integrazione di SAP Cloud Platform Identity Authentication con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere alle applicazioni SAP.
- È possibile abilitare gli utenti per l'accesso automatico alle applicazioni SAP con i propri account Azure AD.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere l'articolo [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Cloud Platform Identity Authentication, è necessario quanto segue:

- Una sottoscrizione di Azure AD.
- Una sottoscrizione di SAP Cloud Platform Identity Authentication abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, ottenere una [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

Prima di approfondire i dettagli tecnici, è fondamentale comprendere i concetti che si desidera esaminare. SAP Cloud Platform Identity Authentication e Active Directory Federation Services consentono di implementare l'accesso SSO per applicazioni o servizi protetti da Azure AD (come IdP) con applicazioni e servizi SAP protetti da SAP Cloud Platform Identity Authentication.

Attualmente SAP Cloud Platform Identity Authentication funge da provider di identità proxy per applicazioni SAP. Azure Active Directory a sua volta agisce come provider di identità iniziale in questa configurazione. 

Il diagramma seguente illustra questa relazione:

![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Con questa configurazione, il tenant di SAP Cloud Platform Identity Authentication viene configurato come applicazione attendibile in Azure Active Directory. 

Tutte le applicazioni e i servizi SAP che si desidera proteggere in questo modo vengono configurati successivamente nella console di gestione di SAP Cloud Platform Identity Authentication.

L'autorizzazione per la concessione dell'accesso a servizi e applicazioni SAP deve pertanto avvenire in SAP Cloud Platform Identity Authentication (diversamente dalla configurazione dell'autorizzazione in Azure Active Directory).

Tramite la configurazione di SAP Cloud Platform Identity Authentication come un'applicazione tramite Marketplace di Azure Active Directory, non è necessario configurare singole attestazioni o asserzioni SAML.

>[!NOTE] 
>Attualmente solo Web SSO è stato testato da entrambe le parti. I flussi necessari per la comunicazione da app ad API o da API ad API dovrebbero funzionare ma non sono ancora stati testati. I test verranno eseguiti durante le attività successive.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Aggiungere SAP Cloud Platform Identity Authentication dalla raccolta
Per configurare l'integrazione di SAP Cloud Platform Identity Authentication in Azure AD, è necessario aggiungere SAP Cloud Platform Identity Authentication dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP Cloud Platform Identity Authentication dalla raccolta, eseguire la procedura seguente:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere la nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SAP Cloud Platform Identity Authentication**. 

5. Selezionare **SAP Cloud Platform Identity Authentication** nel riquadro dei risultati e quindi selezionare il pulsante **Aggiungi**.

    ![SAP Cloud Platform Identity Authentication nell'elenco di risultati](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Cloud Platform Identity Authentication con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SAP Cloud Platform Identity Authentication corrispondente. In altre parole, è necessario stabilire un collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud Platform Identity Authentication.

In SAP Cloud Platform Identity Authentication, all'attributo **Username** (Nome utente) assegnare il stesso valore di **nome utente** di Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Cloud Platform Identity Authentication, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di SAP Cloud Platform Identity Authentication](#create-an-sap-cloud-platform-identity-authentication-test-user) per avere in SAP Cloud Platform Identity Authentication un utente corrispondente a Britta Simon collegato alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP Cloud Platform Identity Authentication.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP Cloud Platform Identity Authentication, eseguire la procedura seguente:**

1. Nel portale di Azure, nella pagina di integrazione di **SAP Cloud Platform Identity Authentication** selezionare **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Se si desidera configurare l'applicazione in modalità avviata da **IDP**, nella sezione **Dominio e URL di SAP Cloud Platform Identity Authentication** digitare un URL nella casella **Identificatore** usando il modello seguente: `https://<entity-id>.accounts.ondemand.com`.  

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'ID effettivo. Contattare il [team di supporto di SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) per ottenere questo valore. Se non si conosce questo valore, seguire la documentazione di SAP Cloud Platform Identity Authentication in [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) (Configurazione del tenant SAML 2.0).

4. Se si desidera configurare l'applicazione in modalità avviata da **SP**, selezionare **Mostra impostazioni URL avanzate**. 

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    Nella casella **URL accesso** digitare un URL corrispondente al modello seguente: `https://<entity-id>.accounts.ondemand.com/admin`.

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Contattare il [team di supporto di SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) per ottenere questo valore.

5. Nella sezione **Certificato di firma SAML** selezionare **XML metadati** e quindi salvare il file di metadati sul computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. L'applicazione SAP Cloud Platform Identity Authentication prevede le asserzioni SAML in un formato specifico. Gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Lo screenshot seguente ne illustra un esempio. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. Se l'applicazione SAP prevede un attributo quale, ad esempio, **firstName**, aggiungere l'attributo **firstName** nella sezione **Attributi utente**. Questa opzione è disponibile nella finestra di dialogo **Single Sign-On** della finestra di dialogo **Attributi token SAML**.

    a. Per aprire la finestra di dialogo **Aggiungi attributo**, selezionare **Aggiungi attributo**. 
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella **Nome** digitare il nome dell'attributo **firstName**.
    
    c. Nell'elenco **Valore** selezionare il valore dell'attributo **user.givenname**.
    
    d. Selezionare **OK**.

8. Fare clic sul pulsante **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. Nella sezione **Configurazione di SAP Cloud Platform Identity Authentication** selezionare **Configura SAP HANA Cloud Platform Identity Authentication** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Per configurare SSO per l'applicazione, passare alla console di amministrazione di SAP Cloud Platform Identity Authentication. L'URL ha il formato seguente: `https://<tenant-id>.accounts.ondemand.com/admin`. Leggere quindi la documentazione relativa a SAP Cloud Platform Identity Authentication disponibile nella pagina [Integration with Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html) (Integrazione con Microsoft Azure AD). 

11. Nel portale di Azure selezionare il pulsante **Salva**.

12. Continuare la procedura seguente solo se si vuole aggiungere e abilitare l'accesso SSO per un'altra applicazione SAP. Ripetere la procedura descritta nella sezione **Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta**.

13. Nel portale di Azure, nella pagina di integrazione di **SAP Cloud Platform Identity Authentication** selezionare **Accesso collegato**.

    ![Configurare l'accesso collegato](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Salvare la configurazione.

>[!NOTE] 
>La nuova applicazione sfrutterà la configurazione del processo SSO dell'applicazione SAP precedente. Assicurarsi di usare gli stessi provider di identità aziendale nella console di amministrazione di SAP Cloud Platform Identity Authentication.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** selezionare la scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata, vedere la [documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Crea**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Creare un utente test di SAP Cloud Platform Identity Authentication

Non è necessario creare un utente in SAP Cloud Platform Identity Authentication. Gli utenti presenti nell'archivio utenti di Azure AD possono usare la funzionalità di accesso SSO.

SAP Cloud Platform Identity Authentication supporta l'opzione di federazione delle identità. Questa opzione consente all'applicazione di controllare se gli utenti autenticati dal provider di identità aziendale sono presenti nell'archivio utenti di SAP Cloud Platform Identity Authentication. 

Per impostazione predefinita l'opzione di federazione delle identità è disabilitata. Se la federazione delle identità è abilitata, solo gli utenti che vengono importati in SAP Cloud Platform Identity Authentication sono in grado di accedere all'applicazione. 

Per altre informazioni su come abilitare o disabilitare la federazione delle identità con SAP Cloud Platform Identity Authentication, vedere Enable Identity Federation with SAP Cloud Platform Identity Authentication (Abilitare la federazione delle identità con SAP Cloud Platform Identity Authentication) in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Configurare la federazione delle identità con l'archivio utenti di SAP Cloud Platform Identity Authentication).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a SAP Cloud Platform Identity Authentication.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SAP Cloud Platform Identity Authentication, eseguire la procedura seguente:**

1. Nel portale di Azure aprire la visualizzazione applicazioni e quindi passare alla visualizzazione directory. Passare quindi ad **Applicazioni aziendali** e selezionare **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SAP Cloud Platform Identity Authentication**.

    ![Collegamento di SAP HANA Cloud Platform Identity Authentication nell'elenco di applicazioni](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Users and groups** (Utenti e gruppi) selezionare **Britta Simon** nell'elenco di utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Selezionare il pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di SAP Cloud Platform Identity Authentication nel pannello di accesso, si ottiene automaticamente l'accesso all'applicazione SAP Cloud Platform Identity Authentication.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 

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

