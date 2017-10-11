---
title: 'Esercitazione: Integrazione di Azure Active Directory con Tableau Server | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6b35609d88fbbf649e15863901d521886db2a4d6
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017

---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Esercitazione: Integrazione di Azure Active Directory con Tableau Server

Questa esercitazione descrive come integrare Tableau Server con Azure Active Directory (Azure AD).

L'integrazione di Tableau Server con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Tableau Server
- È possibile abilitare gli utenti per l'accesso automatico a Tableau Server (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Tableau Server, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Tableau Server abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Tableau Server dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-tableau-server-from-the-gallery"></a>Aggiunta di Tableau Server dalla raccolta
Per configurare l'integrazione di Tableau Server in Azure AD, è necessario aggiungere Tableau Server dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Tableau Server dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Tableau Server**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_search.png)

5. Nel pannello dei risultati selezionare **Tableau Server** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Tableau Server usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Tableau Server corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Server.

Per stabilire la relazione di collegamento, in Tableau Server assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Tableau Server, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Tableau Server](#creating-a-tableau-server-test-user)**: per avere una controparte di Britta Simon in Tableau Server collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Tableau Server.

**Per configurare l'accesso Single Sign-On di Azure AD con Tableau Server, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Tableau Server** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. Nella sezione **URL e dominio Tableau Server** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://azure.<domain name>.link`.
    
    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://azure.<domain name>.link`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > I valori precedenti non sono valori reali. Successivamente si aggiorneranno i valori con l'URL e l'identificatore effettivi indicati nella pagina di configurazione di Tableau Server. 

4. L'applicazione Tableau Server si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Lo screenshot seguente illustra un esempio relativo a questa operazione.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/3.png)
    
5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ---------------| --------------- |    
    | username | *user.displayname* |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_04.png)

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **Ok**


6. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Per configurare l'accesso SSO sull'applicazione, è necessario accedere al tenant di Tableau Server come amministratore.
   
   a. Nella finestra Tableau Server Configuration (Configurazione Tableau Server) fare clic sulla scheda **SAML** .
  
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Selezionare la casella di controllo **Use SAML for single sign-on (Usa SAML per Single Sign-On)**.
   
   c. Tableau Server return URL (URL restituito di Tableau Server): l'URL a cui accedono gli utenti di Tableau Server, ad esempio http://tableau_server. Non è consigliabile utilizzare http://localhost. Gli URL con barra finale (ad esempio http://tableau_server/) non sono supportati. Copiare l'**URL restituito di Tableau Server** e incollarlo nella casella di testo **URL di accesso** in Azure AD nella sezione **URL e dominio Tableau Server**.
   
   d. SAML entity ID (ID entità SAML): l'ID entità identifica in modo univoco l'installazione di Tableau Server nel provider di identità. Se si desidera, qui è possibile immettere di nuovo l'URL di Tableau Server, ma non deve essere l'URL di Tableau Server in uso. Copiare l'**ID di entità SAML** e incollarlo nella casella di testo **Identificatore** in Azure AD nella sezione **URL e dominio Tableau Server**.
     
   e. Fare clic su **Export Metadata File** (Esporta file di metadati) e aprire il file nell'applicazione editor di testo. Cercare l'URL del servizio consumer di asserzione con Http Post e Indice 0 e copiarlo. Incollarlo quindi nella casella di testo **URL di risposta** di Azure AD nella sezione **URL e dominio Tableau Server**.
   
   f. Individuare il file di metadati della federazione scaricato dal portale di Azure e quindi caricarlo in **SAML Idp metadata file**(File metadati IdP SAML).
   
   g. Fare clic sul pulsante **OK** nella pagina di configurazione di Tableau Server.
   
    >[!NOTE] 
    >Il cliente deve caricare eventuali certificati nella configurazione SSO SAML di Tableau Server e il passaggio verrà ignorato nel flusso SSO.
    >Per assistenza nella configurazione di SAML in Tableau Server, vedere questo articolo: [Configure SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm) (Configurazione di SAML).
    >
<CE>

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-tableau-server-test-user"></a>Creazione di un utente test di Tableau Server

Questa sezione descrive come creare un utente chiamato Britta Simon in Tableau Server. In Tableau Server è necessario eseguire il provisioning di tutti gli utenti. 

Il valore username dell'utente deve corrispondere al valore configurato nell'attributo personalizzato **username**in Azure AD. Con un mapping corretto, l'integrazione per la [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)dovrebbe funzionare.

>[!NOTE]
>Se si deve creare manualmente un utente, è necessario contattare l'amministratore di Tableau Server dell'organizzazione.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tableau Server.

![Assegna utente][200] 

**Per assegnare Britta Simon a Tableau Server, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Tableau Server**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Tableau Server nel pannello di accesso, viene effettuato automaticamente l'accesso all'applicazione Tableau Server.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png


