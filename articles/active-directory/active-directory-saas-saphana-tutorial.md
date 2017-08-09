---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP HANA | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: a7e73f6ee763d1005ad85935cf2d8f6b24ecf116
ms.contentlocale: it-it
ms.lasthandoff: 07/27/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Esercitazione: Integrazione di Azure Active Directory con SAP HANA

Questa esercitazione descrive come integrare SAP HANA con Azure Active Directory (Azure AD).

L'integrazione di SAP HANA con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SAP HANA
- È possibile abilitare gli utenti per l'accesso automatico a SAP HANA (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP HANA, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di SAP HANA abilitata per l'accesso Single Sign-On
- Istanza di HANA in esecuzione in qualsiasi VM IaaS pubblica, locale o di Azure oppure in istanze di grandi dimensioni di SAP in Azure
- Interfaccia Web di amministrazione di XSA e HANA Studio installati nell'istanza di HANA.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione SAP HANA per testare i passaggi di questa esercitazione. Testare prima l'integrazione nell'ambiente di sviluppo o di gestione temporanea dell'applicazione e successivamente usare l'ambiente di produzione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP HANA dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sap-hana-from-the-gallery"></a>Aggiunta di SAP HANA dalla raccolta
Per configurare l'integrazione di SAP HANA in Azure AD, è necessario aggiungere SAP HANA dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP HANA dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SAP HANA**, selezionare **SAP HANA** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione. 

    ![Nuova applicazione](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP HANA usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di SAP HANA corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP HANA.

Per stabilire la relazione di collegamento, in SAP HANA assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP HANA, è necessario completare le procedure di base seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di SAP HANA](#creating-a-sap-hana-test-user)**: per avere una controparte di Britta Simon in SAP HANA collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP HANA.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP HANA, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SAP HANA** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. Nella sezione **URL e dominio SAP HANA** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. Nella casella di testo **Identificatore** digitare: `HA100` 

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SAP HANA](https://cloudplatform.sap.com/contact.html). 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Se il certificato non è attivo, attivarlo selezionando la casella di controllo "Make new certificate active" (Rendi attivo nuovo certificato) in Azure AD. 

5. L'applicazione SAP HANA prevede un formato specifico per le asserzioni SAML. La schermata seguente illustra un esempio relativo a questa operazione. In questo caso è stato eseguito il mapping di **Identificatore utente** con la funzione **ExtractMailPrefix()** di **user.mail**. In questo modo si ottiene il valore di prefisso della posta elettronica dell'utente che rappresenta l'ID univoco dell'utente. Il valore viene inviato all'applicazione SAP HANA in ogni risposta con esito positivo.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On**:

    a. Nell'elenco a discesa **Identificatore utente** selezionare **ExtractMailPrefix**.
    
    b. Nell'elenco a discesa **Posta** selezionare **user.mail**.

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Per configurare l'accesso Single Sign-On sul lato **SAP HANA**, accedere alla **console Web HANA XSA** passando al relativo endpoint HTTPS.

    > [!Note]
    > Nella configurazione predefinita l'URL reindirizza la richiesta a una schermata di accesso, che richiede le credenziali di un utente del database SAP HANA autenticato per completare il processo di accesso. L'utente che esegue l'accesso deve avere i privilegi necessari per eseguire attività di amministrazione di SAML.

9. Nell'interfaccia Web XSA passare a **SAML Identity Provider** (Provider di identità SAML), da questa posizione fare clic sul pulsante **"+"** nella parte inferiore dello schermo per visualizzare il riquadro Add Identity Provider Info (Aggiungi informazioni provider di identità) e seguire questa procedura:

    ![Aggiungi provider di identità](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. Nel riquadro **Add Identity Provider Info** (Aggiungi informazioni provider di identità) incollare i contenuti del file XML metadati scaricato dal portale di Azure nella casella di testo **Metadata** (Metadati).

    ![Impostazioni di aggiunta del provider di identità](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Se i contenuti del documento XML sono validi, il processo di analisi estrae le informazioni necessarie per inserire i campi **Subject, Entity ID, and Issuer** (Oggetto, ID entità e Autorità emittente) nell'area dello schermo relativa ai dati generali e i campi degli URL nell'area dello schermo relativa alla destinazione, ad esempio per quanto riguarda **URL di Base e URL Single Sign-On (*)**.

    ![Impostazioni di aggiunta del provider di identità](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Nella casella Name (Nome) dell'area dello schermo relativa ai dati generali immettere un nome per il nuovo provider di identità SSO SAML.

    > [!Note]
    > Il nome dell'IDP SAML è obbligatorio e deve essere univoco. Viene visualizzato nell'elenco di IDP SAML disponibili quando si seleziona SAML come metodo di autenticazione per le applicazioni SAP HANA XS, ad esempio, nell'area dello schermo relativa all'autenticazione dello strumento XS Artifact Administration.

10. Salvare i dettagli del nuovo provider di identità SAML. Scegliere **Save** (Salva) per salvare i dettagli del provider di identità SAML e aggiungere il nuovo IDP SAML all'elenco di IDP SAML noti.

    ![Pulsante per il salvataggio](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. In HANA Studio, nelle proprietà di sistema della scheda **Configuration** (Configurazione) filtrare le impostazioni in base a **saml** e modificare **assertion_timeout** da **10 sec** a **120 sec**.

    ![Impostazione assertion_timeout](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-sap-hana-test-user"></a>Creazione di un utente di test di SAP HANA

Per consentire agli utenti di Azure AD di accedere a SAP HANA, è necessario effettuarne il provisioning in SAP HANA.
SAP HANA supporta il provisioning JIT, abilitato per impostazione predefinita.

Per creare un utente manualmente, seguire questa procedura:

>[!Note]
>È possibile modificare l'autenticazione esterna usata dall'utente.
Gli utenti esterni vengono autenticati mediante un sistema esterno, ad esempio un sistema Kerberos. Per informazioni dettagliate sulle identità esterne, contattare l'[amministratore di dominio](https://cloudplatform.sap.com/contact.html).

1. Aprire [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) come amministratore e abilitare DB-User per l'accesso SSO SAML.

    ![crea utente](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Selezionare la casella di controllo invisibile a sinistra di **SAML** e fare clic sul collegamento per la configurazione.

3. Fare clic su **Add** (Aggiungi) per aggiungere l'IDP SAML e fare clic su **OK** selezionando l'IDP SAML appropriato.

4. Aggiungere l'**identità esterna** (in questo caso, ad esempio, BrittaSimon) oppure scegliere **"Any"** (Qualsiasi) e fare clic su **OK**.

    >[!Note]
    >Se la casella di controllo "ANY" (QUALSIASI) non è selezionata, il nome utente in HANA deve corrispondere esattamente al nome dell'utente nell'UPN prima del suffisso di dominio (ad esempio BrittaSimon@contoso.com diventerebbe BrittaSimon in HANA).

5. A scopo di test, assegnare tutti i ruoli **"XS"** all'utente.

    ![assegnazione di ruoli](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > È consigliabile assegnare solo le autorizzazioni appropriate per il caso d'uso.

6. Salvare l'utente.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP HANA.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SAP HANA, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SAP HANA**.

    ![Assegna utente](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP HANA nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAP HANA.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

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


