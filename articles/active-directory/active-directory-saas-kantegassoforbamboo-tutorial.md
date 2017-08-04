---
title: 'Esercitazione: integrazione di Azure Active Directory con Kantega SSO for Bamboo | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kantega SSO for Bamboo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: cc259bb6f9bdb2293b6935e45e2df52b9fee6873
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Esercitazione: integrazione di Azure Active Directory con Kantega SSO for Bamboo

Questa esercitazione descrive come integrare Kantega SSO for Bamboo con Azure Active Directory (Azure AD).

L'integrazione di Kantega SSO for Bamboo con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Kantega SSO for Bamboo
- È possibile abilitare gli utenti per l'accesso automatico a Kantega SSO for Bamboo, ovvero per il Single Sign-On con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Kantega SSO for Bamboo, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Kantega SSO for Bamboo abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Kantega SSO for Bamboo dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Aggiunta di Kantega SSO for Bamboo dalla raccolta
Per configurare l'integrazione di Kantega SSO for Bamboo in Azure AD, è necessario aggiungere Kantega SSO for Bamboo dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Kantega SSO for Bamboo dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Kantega SSO for Bamboo**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_search.png)

5. Nel pannello dei risultati selezionare **Kantega SSO for Bamboo** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Kantega SSO for Bamboo mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di Kantega SSO for Bamboo corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kantega SSO for Bamboo.

Per stabilire la relazione di collegamento, in Kantega SSO for Bamboo assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kantega SSO for Bamboo, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Kantega SSO for Bamboo](#creating-a-kantega-sso-for-bamboo-test-user)**: per avere una controparte di Britta Simon in Kantega SSO for Bamboo collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Kantega SSO for Bamboo.

**Per configurare l'accesso Single Sign-On di Azure AD con Kantega SSO for Bamboo, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Kantega SSO for Bamboo** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_samlbase.png)

3. In modalità avviata **IDP** nella sezione **Kantega SSO for Bamboo Domain and URLs** (URL e dominio Kantega SSO for Bamboo) eseguire l'operazione seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url1.png)
    
    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. In modalità avviata **SP** selezionare **Mostra impostazioni URL avanzate** ed eseguire l'operazione seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url2.png)
    
    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Questi valori vengono ricevuti durante la configurazione del plug-in Bamboo descritta più avanti nell'esercitazione.

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_400.png)
    
7. In un'altra finestra del Web browser accedere al server locale Bamboo come amministratore.

8. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon1.png)

9. Nella sezione della scheda Add-ons (Componenti aggiuntivi) fare clic su **Find new add-ons** (Trova nuovi componenti aggiuntivi). Cercare **Kantega SSO for Bamboo (SAML & Kerberos)** e fare clic su **Install** (Installa) per installare il nuovo plug-in di SAML.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon2.png)

10. Viene avviata l'installazione del plug-in.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon21.png)

11. Al termine dell'installazione, Fare clic su **Close**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon33.png)

12. Fare clic su **Manage**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon34.png)
    
13. Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.    

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon3.png)

14. Nella sezione **SAML** Selezionare **Azure Active Directory (Azure AD)** dall'elenco a discesa **Add identity provider** (Aggiungi provider di identità).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon4.png)

15. Selezionare il livello di sottoscrizione **Basic** (Di base).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon5.png)

16. Nella sezione **App properties** (Proprietà app) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon6.png)

    a. Copiare il valore di **App ID URI** (URI ID app) e usarlo come **Identificatore, URL di risposta e URL di accesso** nella sezione **Kantega SSO for Bamboo Domain and URLs** (URL e dominio Kantega SSO for Bamboo) nel portale di Azure.

    b. Fare clic su **Avanti**.

17. Nella sezione **Metadata import** (Importazione metadati) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon7.png)

    a. Selezionare **Metadata file on my computer** (File metadati in questo computer) e caricare il file di metadati scaricato dal portale di Azure.

    b. Fare clic su **Avanti**.

18. Nella sezione **Name and SSO location** (Nome e percorso SSO) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon8.png)

    a. Aggiungere il nome del provider di identità nella casella di testo **Identity provider name** (Nome provider di identità), ad esempio Azure AD.

    b. Fare clic su **Avanti**.

19. Verificare il certificato di firma e fare clic su **Next** (Avanti).  

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon9.png)

20. Nella sezione **Bamboo user accounts** (Account utente Bamboo) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon10.png)

    a. Selezionare **Create users in Bamboo's internal Directory if needed** (Crea utenti nella directory interna di Bamboo se necessario) e immettere il nome appropriato del gruppo per gli utenti (è possibile specificare più gruppi separati da virgola).

    b. Fare clic su **Avanti**.

21. Fare clic su **Fine**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon11.png)

22. Nella sezione **Known domains for Azure AD** (Domini noti per Azure AD) seguire questa procedura: 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon12.png)

    a. Selezionare **Known domains** (Domini noti) dal pannello sinistro della pagina.

    b. Immettere il nome di dominio nella casella di testo **Known domains** (Domini noti).

    c. Fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-kantega-sso-for-bamboo-test-user"></a>Creazione di un utente test di Kantega SSO for Bamboo

Per consentire agli utenti di Azure AD di accedere a Bamboo, è necessario effettuarne il provisioning in Bamboo. In Kantega SSO for Bamboo il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server locale Bamboo come amministratore.

2. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Aggiungere un dipendente](./media/active-directory-saas-kantegassoforbamboo-tutorial/user1.png) 

3. Fare clic su **Users**. Nella sezione **Add User** (Aggiungi utente) seguire questa procedura:

    ![Aggiungere un dipendente](./media/active-directory-saas-kantegassoforbamboo-tutorial/user2.png) 

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.
    
    b. Nella casella di testo **Password** digitare la password dell'utente.

    c. Nella casella di testo **Confirm Password** (Conferma password) digitare di nuovo la password dell'utente.
    
    d. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio Britta Simon.
    
    e. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.
    
    f. Fare clic su **Salva**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Kantega SSO for Bamboo.

![Assegna utente][200] 

**Per assegnare Britta Simon a Kantega SSO for Bamboo, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Kantega SSO for Bamboo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Se si fa clic sul riquadro Kantega SSO for Bamboo nel pannello di accesso, si accede automaticamente all'applicazione Kantega SSO for Bamboo.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_203.png


