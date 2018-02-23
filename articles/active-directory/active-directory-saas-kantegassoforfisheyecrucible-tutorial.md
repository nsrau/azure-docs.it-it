---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kantega SSO for FishEye/Crucible | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kantega SSO for FishEye/Crucible.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: cbed5303a605579c595a2230c2e18193f93d4366
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Esercitazione: Integrazione di Azure Active Directory con Kantega SSO for FishEye/Crucible

Questa esercitazione descrive come integrare Kantega SSO for FishEye/Crucible con Azure Active Directory (Azure AD).

L'integrazione di Kantega SSO for FishEye/Crucible con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Kantega SSO for FishEye/Crucible
- È possibile abilitare gli utenti per l'accesso automatico a Kantega SSO for FishEye/Crucible (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Kantega SSO for FishEye/Crucible, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Kantega SSO for FishEye/Crucible abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Kantega SSO for FishEye/Crucible dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Aggiunta di Kantega SSO for FishEye/Crucible dalla raccolta
Per configurare l'integrazione di Kantega SSO for FishEye/Crucible in Azure AD, è necessario aggiungere Kantega SSO for FishEye/Crucible dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Kantega SSO for FishEye/Crucible dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Kantega SSO for FishEye/Crucible**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_search.png)

5. Nel pannello dei risultati selezionare **Kantega SSO for FishEye/Crucible** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Kantega SSO for FishEye/Crucible mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di Kantega SSO for FishEye/Crucible corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kantega SSO for FishEye/Crucible.

Per stabilire la relazione di collegamento, in Kantega SSO for FishEye/Crucible assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kantega SSO for FishEye/Crucible, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Kantega SSO for FishEye/Crucible](#creating-a-kantega-sso-for-fisheyecrucible-test-user)**: per avere una controparte di Britta Simon in Kantega SSO for FishEye/Crucible collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Kantega SSO for FishEye/Crucible.

**Per configurare l'accesso Single Sign-On di Azure AD con Kantega SSO for FishEye/Crucible, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Kantega SSO for FishEye/Crucible** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_samlbase.png)

3. In modalità avviata **IDP** nella sezione **URL e dominio Kantega SSO for FishEye/Crucible** eseguire l'operazione seguente:

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_url1.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. In modalità avviata **SP** selezionare **Mostra impostazioni URL avanzate** ed eseguire l'operazione seguente:

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_url2.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Questi valori vengono ricevuti durante la configurazione del plug-in FishEye/Crucible descritto più avanti nell'esercitazione.

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_400.png)
    
7. In un'altra finestra del Web browser accedere al server locale FishEye/Crucible come amministratore.

8. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon1.png)

9. Nella sezione System Settings (Impostazioni di sistema) fare clic su **Find new add-ons** (Trova nuovi componenti aggiuntivi). 

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/add-on2.png)

10. Cercare **Kantega SSO for Crucible** e fare clic su **Install** (Installa) per installare il nuovo plug-in di SAML.

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon2.png)

11. Viene avviata l'installazione del plug-in. 

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon33.png)

12. Al termine dell'installazione, Fare clic su **Close**.

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon34.png)

13. Fare clic su **Manage**.

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon35.png)

14. Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.    

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon3.png)

15. Nella sezione **SAML** Selezionare **Azure Active Directory (Azure AD)** dall'elenco a discesa **Add identity provider** (Aggiungi provider di identità).

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon4.png)

16. Selezionare il livello di sottoscrizione **Basic** (Di base).

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon5.png)

17. Nella sezione **App properties** (Proprietà app) seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Copiare il valore **URI ID app** e usarlo come **Identificatore, URL di risposta e URL di accesso** nella sezione **URL e dominio Kantega SSO for FishEye/Crucible** nel portale di Azure.

    b. Fare clic su **Avanti**.

18. Nella sezione **Metadata import** (Importazione metadati) seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Selezionare **Metadata file on my computer** (File metadati in questo computer) e caricare il file di metadati scaricato dal portale di Azure.

    b. Fare clic su **Avanti**.

19. Nella sezione **Name and SSO location** (Nome e percorso SSO) seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Aggiungere il nome del provider di identità nella casella di testo **Identity provider name** (Nome provider di identità), ad esempio Azure AD.

    b. Fare clic su **Avanti**.

20. Verificare il certificato di firma e fare clic su **Next** (Avanti).  

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon9.png)

21. Nella sezione **FishEye user accounts** (Account utente FishEye) seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Selezionare **Create users in FishEye's internal Directory if needed** (Crea utenti nella directory interna di FishEye se necessario) e immettere il nome appropriato del gruppo per gli utenti (è possibile specificare più gruppi separati da virgola).

    b. Fare clic su **Avanti**.

22. Fare clic su **Fine**.

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon11.png)

23. Nella sezione **Known domains for Azure AD** (Domini noti per Azure AD) seguire questa procedura: 

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. Selezionare **Known domains** (Domini noti) dal pannello sinistro della pagina.

    b. Immettere il nome di dominio nella casella di testo **Known domains** (Domini noti).

    c. Fare clic su **Save**.  

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-kantega-sso-for-fisheyecrucible-test-user"></a>Creazione di un utente test di Kantega SSO for FishEye/Crucible

Per consentire agli utenti di Azure AD di accedere a FishEye/Crucible, è necessario eseguire il provisioning degli utenti in FishEye/Crucible. In Kantega SSO for FishEye/Crucible il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server locale Crucible come amministratore.

2. Passare il puntatore del mouse sull'ingranaggio e fare clic su **Users** (Utenti).

    ![Aggiungere un dipendente](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user1.png) 

3. Nella scheda **Users** (Utenti) fare clic su **Add user** (Aggiungi utente).

    ![Aggiungere un dipendente](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user2.png)

4. Nella pagina della finestra di dialogo **Add New User** (Aggiungi nuovo utente) seguire questa procedura:

    ![Aggiungere un dipendente](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user3.png) 

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.
    
    b. Nella casella di testo **Display Name** (Nome visualizzato) digitare il nome visualizzato dell'utente, ad esempio Britta Simon.
    
    c. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    d. Nella casella di testo **Password** digitare la password dell'utente.  

    e. Nella casella di testo **Confirm Password** (Conferma password) digitare di nuovo la password dell'utente.

    f. Fare clic su **Aggiungi**.   

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Kantega SSO for FishEye/Crucible.

![Assegna utente][200] 

**Per assegnare Britta Simon a Kantega SSO for FishEye/Crucible, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Kantega SSO for FishEye/Crucible**.

    ![Configure Single Sign-On](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Kantega SSO for FishEye/Crucible nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Kantega SSO for FishEye/Crucible.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_203.png

