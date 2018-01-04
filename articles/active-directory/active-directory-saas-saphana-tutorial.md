---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP HANA | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 855525e2c1d3c33cc7134bbc1cd9b53ca59e1a70
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Esercitazione: Integrazione di Azure Active Directory con SAP HANA

Questa esercitazione descrive come integrare SAP HANA con Azure Active Directory (Azure AD).

L'integrazione di SAP HANA con Azure AD offre i vantaggi seguenti:

- In Azure AD è possibile controllare chi può accedere a SAP HANA.
- È possibile abilitare gli utenti per l'accesso automatico a SAP HANA con gli account Azure AD personali.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP HANA, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SAP HANA abilitata per l'accesso Single Sign-On (SSO)
- Istanza di HANA in esecuzione in qualsiasi VM IaaS pubblica, locale o di Azure oppure in istanze di grandi dimensioni di SAP in Azure
- Interfaccia Web di amministrazione di XSA e HANA Studio installati nell'istanza di HANA

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione SAP HANA per testare i passaggi di questa esercitazione. Testare prima l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e successivamente usare l'ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- [Ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/) di Azure AD, se non si dispone già di un ambiente di test di Azure AD.

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP HANA dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-sap-hana-from-the-gallery"></a>Aggiungere di SAP HANA dalla raccolta
Per configurare l'integrazione di SAP HANA in Azure AD, aggiungere SAP HANA dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP HANA dalla raccolta, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere la nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SAP HANA**. Selezionare quindi **SAP HANA** dal pannello Risultati. Fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione. 

    ![Nuova applicazione](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP HANA usando un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di SAP HANA corrispondente a un utente di Azure AD. In altre parole, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in SAP HANA.

In SAP HANA assegnare a **Username** (Nome utente) lo stesso **nome utente** di Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP HANA, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#creating-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di SAP HANA](#creating-a-sap-hana-test-user) per avere una controparte di Britta Simon in SAP HANA collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assigning-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#testing-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP HANA.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP HANA, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SAP HANA** del portale di Azure selezionare **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Modalità** in **Accesso basato su SAML**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. Nella sezione **URL e dominio SAP HANA** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. Nella casella **Identificatore** digitare il valore seguente: `HA100` 

    b. Nella casella **URL di risposta** digitare un URL nel formato seguente: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SAP HANA](https://cloudplatform.sap.com/contact.html). 

4. Nella sezione **Certificato di firma SAML** selezionare **XML metadati** e quindi salvare il file di metadati sul computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Se il certificato non è attivo, attivarlo selezionando la casella di controllo **Make new certificate active** (Rendi attivo nuovo certificato) in Azure AD. 

5. L'applicazione SAP HANA prevede un formato specifico per le asserzioni SAML. Lo screenshot seguente ne illustra un esempio. 

    In questo caso è stato eseguito il mapping di **Identificatore utente** con la funzione **ExtractMailPrefix()** di **user.mail**. In questo modo si ottiene il valore di prefisso della posta elettronica dell'utente che rappresenta l'ID univoco dell'utente. L'ID utente viene inviato all'applicazione SAP HANA in ogni risposta con esito positivo.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** eseguire questa procedura:

    a. Nell'elenco a discesa **Identificatore utente** selezionare **ExtractMailPrefix**.
    
    b. Nell'elenco a discesa **Posta** selezionare **user.mail**.

7. Fare clic sul pulsante **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Per configurare l'accesso Single Sign-On sul lato SAP HANA, accedere alla **console Web HANA XSA** passando al relativo endpoint HTTPS.

    > [!NOTE]
    > Nella configurazione predefinita l'URL reindirizza la richiesta a una schermata di accesso, che richiede le credenziali di un utente del database SAP HANA autenticato. L'utente che effettua l'accesso deve disporre delle autorizzazioni per eseguire attività di amministrazione di SAML.

9. Nell'interfaccia Web XSA passare a **SAML Identity Provider** (Provider di identità SAML). A questo punto selezionare il pulsante **+** nella parte inferiore della schermata per visualizzare il riquadro **Add Identity Provider Info** (Aggiungi informazioni provider di identità). Seguire quindi questa procedura:

    ![Aggiungi provider di identità](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. Nel riquadro **Add Identity Provider Info** (Aggiungi informazioni provider di identità) incollare i contenuti del file XML metadati scaricato dal portale di Azure nella casella **Metadata** (Metadati).

    ![Impostazioni di aggiunta del provider di identità](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Se il contenuto del documento XML è valido, il processo di analisi estrae le informazioni necessarie per i campi **Subject, Entity ID, and Issuer** (Oggetto, ID entità e Autorità di certificazione) nell'area dello schermo **General data** (Dati generali). Vengono estratte anche le informazioni necessarie per i campi relativi agli URL nell'area dello schermo **Destination** (Destinazione), ad esempio i campi **Base URL e SingleSignOn URL (*)** (URL di base e URL Single Sign-On).

    ![Impostazioni di aggiunta del provider di identità](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Nella casella **Name** (Nome) dell'area dello schermo **General Data** (Dati generali) immettere un nome per il nuovo provider di identità SSO SAML.

    > [!NOTE]
    > Il nome del provider di identità SAML è obbligatorio e deve essere univoco. Viene visualizzato nell'elenco di provider di identità SAML disponibili che viene visualizzato quando si seleziona SAML come metodo di autenticazione per le applicazioni SAP HANA XS da usare. Ad esempio, è possibile eseguire questa procedura nell'area dello schermo **Authentication** (Autenticazione) dello strumento di amministrazione di elementi XS.

10. Selezionare **Save** (Salva) per salvare i dettagli del provider di identità SAML e aggiungere il nuovo provider di identità SAML all'elenco di provider noti.

    ![Pulsante per il salvataggio](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. In Studio HANA, nella scheda **Configuration** (Configurazione) filtrare le impostazioni all'interno delle proprietà del sistema in base a **saml**. Modificare quindi il valore del parametro **assertion_timeout** da **10 sec** a **120 sec**.

    ![Impostazione assertion_timeout](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente selezionare la scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation](https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel **portale di Azure** fare clic sull'icona **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Per visualizzare un elenco di utenti, passare a **Utenti e gruppi**. Selezionare quindi **Tutti gli utenti**.
    
    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Per aprire la finestra di dialogo **Utente**, selezionare **Aggiungi** nella parte superiore della finestra di dialogo.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'**indirizzo e-mail** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota della password.

    d. Selezionare **Create**.
 
### <a name="create-a-sap-hana-test-user"></a>Creare un utente di test di SAP HANA

Per consentire agli utenti di Azure AD di accedere a SAP HANA, è necessario effettuare il provisioning di tali utenti in SAP HANA.
SAP HANA supporta il provisioning JIT, abilitato per impostazione predefinita.

Per creare un utente manualmente, seguire questa procedura:

>[!NOTE]
>È possibile modificare l'autenticazione esterna usata dagli utenti. Questi possono autenticarsi con un sistema esterno, ad esempio Kerberos. Per informazioni dettagliate sulle identità esterne, contattare l'[amministratore di dominio](https://cloudplatform.sap.com/contact.html).

1. Aprire [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) come amministratore e quindi abilitare DB-User per l'accesso SSO SAML.

    ![Create user](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Selezionare la casella di controllo invisibile a sinistra di **SAML** e quindi selezionare il collegamento **Configura**.

3. Selezionare **Aggiungi** per aggiungere il provider di identità SAML.  Selezionare il provider di identità SAML appropriato e quindi **OK**.

4. Aggiungere un valore nel campo **External Identity** (Identità esterna), in questo caso BrittaSimon, oppure scegliere **Any** (Qualsiasi). Selezionare **OK**.

    >[!Note]
    >Se la casella di controllo **Any** (Qualsiasi) non è selezionata, il nome utente in HANA deve corrispondere esattamente al nome dell'utente nell'UPN prima del suffisso di dominio (ad esempio BrittaSimon@contoso.com diventerebbe BrittaSimon in HANA).

5. A scopo di test, assegnare tutti i ruoli **XS** all'utente.

    ![Assegnazione di ruoli](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > È consigliabile assegnare solo le autorizzazioni appropriate per i casi d'uso.

6. Salvare l'utente.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP HANA.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SAP HANA, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione applicazioni. Passare alla visualizzazione directory e quindi ad **Applicazioni aziendali**. Selezionare **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

2. Nell'elenco delle applicazioni selezionare **SAP HANA**.

    ![Assegnare utenti](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202] 

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Selezionare il pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro SAP HANA nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAP HANA.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png

