---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Business Object Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 6d517c5e302ac36e5bba2053998c75f8f4d42683
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Business Object Cloud

Questa esercitazione descrive come integrare SAP Business Object Cloud con Azure Active Directory (Azure AD).

L'integrazione di SAP Business Object Cloud con Azure AD offre i vantaggi seguenti:

- In Azure AD è possibile controllare chi può accedere a SAP Business Object Cloud.
- È possibile connettere automaticamente gli utenti a SAP Business Object Cloud usando Single Sign-On e l'account Azure AD di un utente.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Business Object Cloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di SAP Business Object Cloud, con l'accesso Single Sign-On attivato

> [!NOTE]
> Se si testano i passaggi di questa esercitazione, è consigliabile non testarli in un ambiente di produzione.

Raccomandazioni per il test dei passaggi di questa esercitazione:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiungere SAP Business Object Cloud dalla raccolta.
2. Configurare e testare l'accesso Single Sign-On di Azure AD.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Aggiungere SAP Business Object Cloud dalla raccolta
Per configurare l'integrazione di SAP Business Object Cloud con Azure AD, nella raccolta aggiungere SAP Business Object Cloud al proprio elenco di app SaaS gestite.

Per aggiungere SAP Business Object Cloud dalla raccolta:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel menu sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pagina Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca immettere **SAP Business Object Cloud**.

    ![Casella di ricerca](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. Nel pannello dei risultati selezionare **SAP Business Object Cloud** e quindi selezionare **Aggiungi**.

    ![SAP Business Object Cloud nell'elenco risultati](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Business Object Cloud con un utente di test di nome *Britta Simon*.

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente di Azure AD in SAP Business Object Cloud. Deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Business Object Cloud.

Per stabilire la relazione di collegamento, in SAP Business Object Cloud per **Username** (Nome utente) assegnare il valore del **nome utente** in Azure AD.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Business Object Cloud, completare le attività seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#set-up-azure-ad-single-sign-on). Configura un utente per l'uso di questa funzionalità.
2. [Creare un utente test di Azure AD](#create-an-azure-ad-test-user). Testa l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di SAP Business Object Cloud](#create-an-sap-business-object-cloud-test-user). Crea una controparte di Britta Simon in SAP Business Object Cloud collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user). Configura Britta Simon per usare l'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on). Verifica se la configurazione funziona.

### <a name="set-up-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si attiva l'accesso Single Sign-On di Azure AD nel portale di Azure, quindi si configura Single Sign-On nell'applicazione SAP Business Object Cloud.

Per configurare Single Sign-On di Azure AD con SAP Business Object Cloud:

1. Nella pagina di integrazione dell'applicazione **SAP Business Object Cloud** del portale di Azure selezionare **Single Sign-On**.

    ![Selezionare Single Sign-On][4]

2. Nella pagina **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML**.
 
    ![Selezionare Accesso basato su SAML](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. In **SAP Business Object Cloud Domain and URLs** (URL e dominio SAP Business Object Cloud) completare questa procedura:

    1. Nella casella **URL di accesso** immettere un URL con il modello seguente: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. Nella casella **Identificatore** immettere un URL con il modello seguente:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![URL della pagina SAP Business Object Cloud Domain and URLs (URL e dominio SAP Business Object Cloud)](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > I valori in questi URL sono forniti solo a scopo dimostrativo. Aggiornare i valori con l'URL di accesso e l'URL dell'identificatore effettivi. Per ottenere l'URL di accesso, contattare il [team di supporto clienti di SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html). Per ottenere l'URL dell'identificatore, scaricare i metadati di SAP Business Object Cloud dalla console di amministrazione. Questo concetto verrà spiegato più avanti nell'esercitazione. 

4. In **Certificato di firma SAML** selezionare **XML metadati**, quindi salvare il file di metadati sul computer.

    ![Selezionare XML metadati](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Selezionare **Salva**.

    ![Selezionare Salva](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di SAP Business Object Cloud come amministratore.

7. Selezionare **Menu** > **Sistema** > **Amministrazione**.
    
    ![Selezionare Menu, quindi Sistema e infine Amministrazione](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. Selezionare l'icona **Modifica** (penna) nella scheda **Sicurezza**.
    
    ![Selezionare l'icona Modifica nella scheda Sicurezza](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. Per **Metodo di autenticazione** selezionare **SAML Single Sign-On (SSO)**.

    ![Selezionare SAML Single Sign-On per il metodo di autenticazione](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Per scaricare i metadati del provider di servizi (passaggio 1), selezionare **Download**. Nel file di metadati trovare e copiare il valore **entityID**. Nel portale di Azure, in **SAP Business Object Cloud Domain and URLs** (URL e dominio SAP Business Object Cloud), incollare il valore nella casella **Identificatore**.

    ![Copiare e incollare il valore di entityID](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Per caricare i metadati del provider di servizi (passaggio 2) nel file scaricato nel portale di Azure, in **Upload Identity Provider metadata** (Caricare i metadati del provider di identità) selezionare **Carica**.  

    ![In Upload Identity Provider metadata (Caricare i metadati del provider di identità) selezionare Carica](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. Nell'elenco **Attributo utente** selezionare l'attributo utente (passaggio 3) da usare per l'implementazione. Questo attributo utente esegue il mapping al provider di identità. Per immettere un attributo personalizzato nella pagina dell'utente, usare l'opzione **Custom SAML Mapping** (Mapping SAML personalizzato). oppure selezionare **Posta elettronica** o **ID UTENTE** come attributo utente. Nell'esempio è stato selezionato **Posta elettronica** perché è stato eseguito il mapping dell'attestazione dell'ID utente con l'attributo **userprincipalname** nella sezione **Attributi utente** del portale di Azure. Si ottiene così un indirizzo di posta elettronica dell'utente univoco, che viene inviato all'applicazione SAP Business Object Cloud in ogni risposta SAML con esito positivo.

    ![Selezionare Attributo utente](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Per verificare l'account con il provider di identità (passaggio 4), nella casella **Login Credential (Email)** (Credenziale di accesso - Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente. Selezionare quindi **Verifica account**. Il sistema aggiunge le credenziali di accesso all'account utente.

    ![Immettere l'indirizzo di posta elettronica e selezionare Verifica account](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Selezionare l'icona **Salva**.

    ![Icona Salva](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo avere aggiunto l'app selezionando **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On**. È possibile accedere alla documentazione incorporata nella sezione **Configurazione** nella parte inferiore della pagina. Per altre informazioni, vedere la [documentazione incorporata su Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

Per creare un utente di test in Azure AD:

1. Nel portale di Azure fare clic su **Azure Active Directory** nel menu sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Selezionare **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Per aprire la finestra di dialogo **Utente**, fare clic su **Aggiungi**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** completare questa procedura:
 
    1. Nella casella **Nome** immettere **BrittaSimon**.

    2. Nella casella **Nome utente** immettere l'indirizzo di posta elettronica dell'utente Britta Simon.

    3. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    4. Selezionare **Crea**.

        ![Finestra di dialogo Utente](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Creare un utente di Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Creare un utente di test di SAP Business Object Cloud

È necessario effettuare il provisioning degli utenti di Azure AD in SAP Business Object Cloud perché possano accedere a SAP Business Object Cloud. In SAP Business Object Cloud il provisioning è un'attività manuale.

Per effettuare il provisioning di un account utente:

1. Accedere al sito aziendale di SAP Business Object Cloud come amministratore.

2. Selezionare **Menu** > **Security** (Sicurezza)  > **Users** (Utenti).

    ![Aggiungere un dipendente](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. Nella pagina **Users** (Utenti) selezionare **+** per aggiungere i dettagli di un nuovo utente. 

    ![Pagina Aggiungi utenti (Aggiungi utenti)](./media/active-directory-saas-sapboc-tutorial/user4.png)

    Completare quindi i passaggi seguenti:

    1. Nella casella **USER ID** (ID UTENTE) immettere l'ID dell'utente, ad esempio, **Britta**.

    2. Nella casella **FIRST NAME** (NOME) immettere il nome dell'utente, ad esempio **Britta**.

    3. Nella casella **LAST NAME** (COGNOME) immettere il cognome dell'utente, ad esempio **Simon**.

    4. Nella casella **NOME VISUALIZZATO** immettere nome e cognome dell'utente, ad esempio **Britta Simon**.

    5. Nella casella **E-MAIL** (POSTA ELETTRONICA) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    6. Nella pagina **Select Roles** (Selezione ruoli) selezionare il ruolo appropriato per l'utente e quindi fare clic su **OK**.

      ![Selezionare il ruolo](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. Selezionare l'icona **Salva**.    


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si consente all'utente Britta Simon di usare l'accesso Single Sign-On di Azure AD concedendo all'account utente l'accesso a SAP Business Object Cloud.

Per assegnare Britta Simon a SAP Business Object Cloud:

1. Nel portale di Azure aprire la visualizzazione applicazioni e quindi passare alla visualizzazione directory. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SAP Business Object Cloud**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Selezionare Utenti e gruppi][202] 

4. Selezionare **Aggiungi**. Quindi nella pagina **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Pagina Aggiungi assegnazione][203]

5. Nella pagina **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti.

6. Nella pagina **Utenti e gruppi** selezionare **Seleziona**.

7. Nella pagina **Aggiungi assegnazione** selezionare **Assegna**.

![Assegnare il ruolo utente][200] 
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro SAP Business Object Cloud nel pannello di accesso, si accederà automaticamente all'applicazione SAP Business Object Cloud.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png

