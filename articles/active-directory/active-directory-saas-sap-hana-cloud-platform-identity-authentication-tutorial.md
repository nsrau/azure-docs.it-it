---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP HANA Cloud Platform Identity Authentication |Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e SAP HANA Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 18fa3fd2fc9bb68b9e68c0c5ac5663a8a8348fd7
ms.openlocfilehash: e02cb1916c65d137dc06b1ce6a9dd9621a31b56c


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform-identity-authentication"></a>Esercitazione: Integrazione di Azure Active Directory con SAP HANA Cloud Platform Identity Authentication

Questa esercitazione descrive la modalità di integrazione di SAP HANA Cloud Platform Identity Authentication con Azure Active Directory (Azure AD). SAP HANA Cloud Platform Identity Authentication è usato come IdP proxy per l'accesso ad applicazioni SAP usando Azure AD come IdP principale.

L'integrazione di SAP HANA Cloud Platform Identity Authentication con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere all'applicazione SAP
- È possibile abilitare gli utenti per l'accesso automatico all'applicazione SAP (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP HANA Cloud Platform Identity Authentication, è necessario quanto segue:

- Sottoscrizione di Azure AD.
- Sottoscrizione di **SAP HANA Cloud Platform Identity Authentication** abilitata per l'accesso Single Sign-On


>[!NOTE] 
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).



## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP HANA Cloud Platform Identity Authentication dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

Prima di approfondire i dettagli tecnici, è fondamentale comprendere i concetti che si desidera esaminare. La federazione di SAP HANA Cloud Platform Identity Authentication e Azure Active Directory consente di implementare l'accesso Single Sign-On per applicazioni o servizi protetti da AAD (come IdP) con applicazioni SAP e servizi protetti da SAP HANA Cloud Platform Identity Authentication.

Attualmente SAP HANA Cloud Platform Identity Authentication funge da provider di identità proxy per applicazioni SAP. Azure Active Directory a sua volta agisce come provider di identità iniziale in questa configurazione. Il diagramma seguente illustra questo processo:    

![Creazione di un utente test di Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Con questa configurazione, il tenant di SAP HANA Cloud Platform Identity Authentication viene configurato come applicazione attendibile in Azure Active Directory. 

Tutte le applicazioni e i servizi SAP che si desidera proteggere in questo modo vengono configurati successivamente nella console di gestione di SAP HANA Cloud Platform Identity Authentication.

Questo è un aspetto molto importante da comprendere. Significa che l'autorizzazione per la concessione dell'accesso a servizi e applicazioni SAP deve avvenire in SAP HANA Cloud Platform Identity Authentication per una configurazione di questo tipo (anziché la configurazione dell'autorizzazione in Azure Active Directory).

Configurando SAP HANA Cloud Platform Identity Authentication come applicazione tramite il Marketplace di Azure Active Directory, non è necessario occuparsi di configurare le necessarie attestazioni singole / asserzioni SAML e le trasformazioni necessarie per produrre un token di autenticazione valido per le applicazioni SAP.

>[!NOTE] 
>Attualmente solo Web SSO è stato testato da entrambe le parti. I flussi necessari per la comunicazione da App a API o da API a API dovrebbero funzionare ma non sono ancora stati testati. I test verranno eseguiti come parte delle attività successive. La documentazione verrà aggiornata di conseguenza al termine della convalida.

## <a name="adding-sap-hana-cloud-platform-identity-authentication-from-the-gallery"></a>Aggiunta di SAP HANA Cloud Platform Identity Authentication dalla raccolta
Per configurare l'integrazione di SAP HANA Cloud Platform Identity Authentication in Azure AD, è necessario aggiungere SAP HANA Cloud Platform Identity Authentication dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP HANA Cloud Platform Identity Authentication dalla raccolta, eseguire i passaggi seguenti:**

1. Nel [**portale di gestione di Azure**](https://portal.azure.com) fare clic sull'icona di **Azure Active Directory** nel pannello di navigazione sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **SAP HANA Cloud Platform Identity Authentication**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_01.png)

5. Nel pannello risultati selezionare **SAP HANA Cloud Platform Identity Authentication**, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP HANA Cloud Platform Identity Authentication con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SAP HANA Cloud Platform Identity Authentication che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP HANA Cloud Platform Identity Authentication.

La relazione di collegamento viene stabilita assegnando al valore di **Nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in SAP HANA Cloud Platform Identity Authentication.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP HANA Cloud Platform Identity Authentication, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di SAP HANA Cloud Platform Identity Authentication](#creating-a-sap-hana-cloud-platform-identity-authentication-test-user)**: per avere in SAP HANA Cloud Platform Identity Authentication un utente corrispondente a Britta Simon collegato alla sua rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP HANA Cloud Platform Identity Authentication.

L'applicazione SAP HANA Cloud Platform Identity Authentication prevede le asserzioni SAML in un formato specifico. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_03.png)

**Per configurare l'accesso Single Sign-On di Azure AD con SAP HANA Cloud Platform Identity Authentication, eseguire la procedura seguente:**

1. Nel portale di gestione di Azure, nella pagina di integrazione dell'applicazione **SAP HANA Cloud Platform Identity Authentication**, fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On][5]

3. Nella sezione **Attributi utente** nella finestra di dialogo **Single Sign-On**, se l'applicazione SAP prevede un attributo, ad esempio "firstName". Nella finestra di dialogo relativa agli attributi del token SAML aggiungere l'attributo "firstName".

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On][6]

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_05.png)
    
    b. Nella casella di testo **Nome attributo** digitare il nome dell'attributo "firstName".

    c. Nell'elenco **Valore attributo** selezionare il valore dell'attributo "user.givenname".
    
    d. Fare clic su **Ok**


4. Nella sezione **URL e dominio di SAP HANA Cloud Platform Identity Authentication** eseguire la procedura seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_06.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL di accesso per l'applicazione SAP.

    b. Nella casella di testo **Identificatore** digitare il valore nel formato seguente: `<tenant-id>.accounts.ondemand.com`. Se non si conosce questo valore, seguire la documentazione di SAP HANA Cloud Platform Identity Authentication in [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)(Configurazione del tenant SAML 2.0).


5. Nella sezione **SAP HANA Cloud Platform Identity Authentication Configuration** (Configurazione di SAP HANA Cloud Platform Identity Authentication) fare clic su **Configure SAP HANA Cloud Platform Identity Authentication** (Configura SAP HANA Cloud Platform Identity Authentication) per aprire la finestra di dialogo **Configura accesso**. Fare clic su **Metadati SAML XML** e salvare il file nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_07.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_08.png)


6. Per configurare SSO per l'applicazione, passare alla console di amministrazione di SAP HANA Cloud Platform Identity Authentication. L'URL ha il formato seguente: `https://<tenant-id>.accounts.ondemand.com/admin`. Seguire la documentazione di SAP HANA Cloud Platform Identity Authentication per [configurare Microsoft Azure AD come provider di identità aziendale in SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html) 

10. Nel portale di gestione di Azure fare clic sul pulsante **Salva**.  
  

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel nuovo portale.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** eseguire la procedura seguente:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **Britta Simon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-a-sap-hana-cloud-platform-identity-authentication-test-user"></a>Creazione di un utente test di SAP HANA Cloud Platform Identity Authentication

Non è necessario creare un utente in SAP HANA Cloud Platform Identity Authentication. Gli utenti presenti nell'archivio utenti di Azure AD possono usare la funzionalità di accesso Single Sign-On (SSO).

SAP HANA Cloud Platform Identity Authentication supporta l'opzione di federazione delle identità. Questa opzione consente all'applicazione di controllare se gli utenti autenticati dal provider di identità aziendale sono presenti nell'archivio utenti di SAP HANA Cloud Platform Identity Authentication. Nella configurazione predefinita l'opzione di federazione delle identità è disabilitata. Se la federazione delle identità è abilitata, solo gli utenti che vengono importati in SAP HANA Cloud Platform Identity Authentication sono in grado di accedere all'applicazione. 

Per altre informazioni su come abilitare o disabilitare la federazione delle identità con SAP HANA Cloud Platform Identity Authentication, vedere Abilita federazione delle identità con SAP HANA Cloud Platform Identity Authentication in [Configure Identity Federation with the User Store of SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Configura federazione delle identità con l'archivio utenti di SAP HANA Cloud Platform Identity Authentication).


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAPHANA Cloud Platform Identity Authentication.

![Assegna utente][200] 

**Per assegnare Britta Simon a SAP HANA Cloud Platform Identity Authentication, eseguire la procedura seguente:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **SAP HANA Cloud Platform Identity Authentication**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_09.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAP HANA Cloud Platform Identity Authentication nel pannello di accesso, si dovrebbe ottenere automaticamente l'accesso all'applicazione SAP HANA Cloud Platform Identity Authentication.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_06.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_203.png


<!--HONumber=Jan17_HO2-->


