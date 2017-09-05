---
title: 'Esercitazione: Integrazione di Azure Active Directory con Syncplicity | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 1321fa71bcd625d6ea754432bfb402d3919e38f3
ms.contentlocale: it-it
ms.lasthandoff: 06/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Esercitazione: Integrazione di Azure Active Directory con Syncplicity

Questa esercitazione descrive come integrare Syncplicity con Azure Active Directory (Azure AD).

L'integrazione di Syncplicity con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Syncplicity
- È possibile abilitare gli utenti per l'accesso automatico a Syncplicity (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Syncplicity, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Syncplicity abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Syncplicity dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-syncplicity-from-the-gallery"></a>Aggiunta di Syncplicity dalla raccolta
Per configurare l'integrazione di Syncplicity in Azure AD, è necessario aggiungere Syncplicity dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Syncplicity dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Syncplicity**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_search.png)

5. Nel pannello dei risultati selezionare **Syncplicity** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Syncplicity usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Syncplicity corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Syncplicity.

Per stabilire la relazione di collegamento, in Syncplicity assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Syncplicity, è necessario completare le procedure di base seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Syncplicity](#creating-a-syncplicity-test-user)**: per avere una controparte di Britta Simon in Syncplicity collegata alla relativa rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Syncplicity.

**Per configurare l'accesso Single Sign-On di Azure AD con Syncplicity, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Syncplicity** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. Nella sezione **URL e dominio Syncplicity** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.syncplicity.com`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Syncplicity](https://www.syncplicity.com/contact-us). 
 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Syncplicity** fare clic su **Configura Syncplicity** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. Accedere al tenant **Syncplicity**.

8. Nel menu nella parte superiore fare clic su **admin** (amministrazione), selezionare **settings** (impostazioni) e quindi fare clic su **Custom domain and single sign-on** (Dominio personalizzato e Single Sign-On).
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769545.png "Syncplicity")

9. Nella pagina finestra di dialogo **Single Sign-On (SSO)** , eseguire la procedura seguente:
   
    ![Accesso Single Sign-On \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. Nella casella di testo **Custom Domain** , digitare il nome del dominio.
  
    b. Selezionare **Enabled** (Abilitato) come **Single Sign-On Status** (Stato Single Sign-On).

    c. Nella casella di testo **Entity Id** (ID entità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    d. Nella casella di testo **Sign-in page URL** (URL pagina di accesso) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    e. Nella casella di testo **Logout page URL** (URL pagina di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    f. In **Identity Provider Certificate** (Certificato provider di identità) fare clic su **Choose file** (Scegli file) e quindi caricare il certificato scaricato dal portale di Azure. 

    g. Fare clic su **SAVE CHANGES** (Salva modifiche).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-syncplicity-test-user"></a>Creazione di un utente di test di Syncplicity
Per essere in grado di effettuare l’accesso, è necessario effettuare il provisioning degli utenti AAD all’applicazione Syncplicity. In questa sezione viene descritto come creare gli account utente AAD in Syncplicity.

**Per eseguire il provisioning di un account utente a Syncplicity, eseguire la procedura seguente:**

1. Accedere al tenant **Syncplicity** (ad esempio: `https://company.Syncplicity.com`).

2. Fare clic su **admin** (Amministrazione) e selezionare **user accounts** (Account utente).

3. Fare clic su **ADD A USER** (Aggiungi utente).
   
    ![Gestione utenti](./media/active-directory-saas-syncplicity-tutorial/ic769764.png "Gestione utenti")

4. Digitare gli **indirizzi di posta elettronica** di un account AAD di cui si vuole effettuare il provisioning, selezionare **User** (Utente) come **Role** (Ruolo) e quindi fare clic su **NEXT** (Avanti).
   
    ![Informazioni sull'account](./media/active-directory-saas-syncplicity-tutorial/ic769765.png "Informazioni sull'account")
   
    >[!NOTE]
    >Il titolare dell'account AAD riceverà un messaggio di posta elettronica con un collegamento per confermare e attivare l'account. 
    > 

5. Selezionare un gruppo nell'azienda a cui aggiungere il nuovo utente e quindi fare clic su **NEXT** (Avanti).
   
    ![Appartenenza al gruppo](./media/active-directory-saas-syncplicity-tutorial/ic769772.png "Appartenenza al gruppo")
   
    >[!NOTE]
    >Se non sono elencati gruppi, fare clic su **NEXT** (Avanti). 
    > 

6. Selezionare le cartelle a cui si vuole applicare il controllo di Syncplicity nel computer dell'utente e quindi fare clic su **NEXT** (Avanti).
   
    ![Cartelle di Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769773.png "Cartelle di Syncplicity")

>[!NOTE]
>È possibile usare qualsiasi altro strumento di creazione account utente o API fornita da Syncplicity per eseguire il provisioning degli account utente di AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Syncplicity.

![Assegna utente][200] 

**Per assegnare Britta Simon a Syncplicity, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Syncplicity**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Syncplicity nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Syncplicity.
## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_203.png


