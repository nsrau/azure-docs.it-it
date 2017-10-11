---
title: 'Esercitazione: Integrazione di Azure Active Directory con Recognize | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Recognize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 97d85183d0307c41a3b879d440d87a6fb0c53190
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Esercitazione: Integrazione di Azure Active Directory con Recognize

Questa esercitazione descrive come integrare Recognize con Azure Active Directory (Azure AD).

L'integrazione di Recognize con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Recognize
- È possibile abilitare gli utenti per l'accesso automatico a Recognize (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Recognize, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Recognize abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese: [offerta prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Recognize dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-recognize-from-the-gallery"></a>Aggiunta di Recognize dalla raccolta
Per configurare l'integrazione di Recognize in Azure AD, è necessario aggiungere Recognize dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Recognize dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Recognize**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_search.png)

5. Nel pannello dei risultati selezionare **Recognize** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Recognize in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Recognize che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Recognize.

Per stabilire la relazione di collegamento, in Recognize assegnare il valore di **nome utente** in Azure AD come valore di **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Recognize, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Recognize](#creating-a-recognize-test-user)**: per avere una controparte di Britta Simon in Recognize collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Recognize.

**Per configurare Single Sign-On di Azure AD con Recognize, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Recognize** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_samlbase.png)

3. Nella sezione **URL e dominio Recognize** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://recognizeapp.com/<your-domain>/saml/sso`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Contattare il [team di supporto client di Recognize ](mailto:support@recognizeapp.com) per ottenere l'URL Sign-On; è possibile ottenere il valore di identificazione aprendo l'URL metadati del provider di servizi dalla sezione delle impostazioni SSO illustrata più avanti nell'esercitazione. . 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Recognize** fare clic su **Configura Recognize** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_configure.png) 

7. In un'altra finestra del browser Web accedere al tenant Recognize come amministratore.

8. In alto a destra fare clic su **Menu**. Passare a **Company Admin** (Amministrazione società).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

9. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

10. Nella sezione **SSO Settings** (Impostazioni SSO) seguire questa procedura.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
    
    a. In **Enable SSO** (Abilita SSO) selezionare **ON**.

    b. Nella casella di testo **IDP Entity ID** (ID entità IdP) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.
    
    c. Nella casella di testo **SSO Target URL** (URL di destinazione SSO) incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.
    
    d. Nella casella di testo **SLO Target URL** (URL SLO di destinazione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure. 
    
    e. Aprire il file **Certificate (Base64)** scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato**.
    
    f. Fare clic sul pulsante **Save settings** (Salva impostazioni). 

11. Accanto alla sezione **SSO Settings** (Impostazioni Single Sign-On) copiare l'URL in **Service Provider Metadata url** (URL metadati del provider di servizio).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

12. In una pagina del browser fare clic sul collegamento **Metadata URL** (URL metadati) per scaricare il documento di metadati. Quindi copiare il valore EntityDescriptor (entityID) dal file e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio Recognize** del portale di Azure.
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-recognize-test-user"></a>Creazione di un utente test di Recognize

Per consentire agli utenti di Azure AD di accedere a Recognize, è necessario eseguirne il provisioning in Recognize. Nel caso di Recognize, il provisioning è un'attività manuale.

Questa app non supporta il provisioning SCIM, ma dispone di una sincronizzazione utente alternativa che esegue il provisioning degli utenti. 

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Recognize come amministratore.

2. In alto a destra fare clic su **Menu**. Passare a **Company Admin** (Amministrazione società).

3. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).

4. Nella sezione **User Sync** (Sincronizzazione utente) seguire questa procedura.
   
   ![Nuovo utente](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Nuovo utente")
   
   a. In **Sync Enabled** (Sincronizzazione abilitata) selezionare **ON**.
   
   b. In **Choose sync provider** (Scegli provider di sincronizzazione) selezionare **Microsoft/Office 365**.
   
   c. Fare clic su **Run User Sync** (Esegui sincronizzazione utente).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Recognize.

![Assegna utente][200] 

**Per assegnare Britta Simon a Recognize, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Recognize**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Recognize nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Recognize. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png

