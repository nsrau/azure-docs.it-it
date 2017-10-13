---
title: 'Esercitazione: Integrazione di Azure Active Directory con Boomi | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 1121d22beddf73fd2109a4b410422f76dd37478e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Esercitazione: Integrazione di Azure Active Directory con Boomi

Questa esercitazione descrive come integrare Boomi con Azure Active Directory (Azure AD).

L'integrazione di Boomi con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Boomi
- È possibile abilitare gli utenti per l'accesso automatico a Boomi (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Boomi, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Boomi abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Boomi dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-boomi-from-the-gallery"></a>Aggiunta di Boomi dalla raccolta
Per configurare l'integrazione di Boomi in Azure AD, è necessario aggiungere Boomi dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Boomi dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Boomi**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_search.png)

5. Nel pannello dei risultati selezionare **Boomi** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Boomi in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Boomi che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Boomi.

Per stabilire la relazione di collegamento, in Boomi assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Boomi, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Boomi](#creating-a-boomi-test-user)**: per avere una controparte di Britta Simon in Boomi collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Boomi.

**Per configurare l'accesso Single Sign-On di Azure AD con Boomi, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Boomi** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_samlbase.png)

3. Nella sezione **URL e dominio Boomi** eseguire i passaggi descritti di seguito:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://platform.boomi.com/sso/<accountname>/saml`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://platform.boomi.com/sso/<accountname>/saml`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere tali valori, [contattare il team di supporto di Boomi](https://boomi.com/company/contact/).

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_certificate.png)

4. L'applicazione Boomi prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_attribute.png)

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** per ogni riga illustrata nella tabella seguente, seguire questa procedura:

    | Nome attributo | Valore attributo |
    | -------------- | --------------- |
    | FEDERATION_ID | user.mail |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_04.png)
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di Boomi** fare clic su **Configura Boomi** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_configure.png) 

8. In un'altra finestra del Web browser accedere al sito aziendale di Boomi come amministratore. 

9. Passare a **Nome società** e scegliere **Configurazione**.

10. Fare clic sulla scheda **Opzioni SSO** e seguire questa procedura.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. Selezionare la casella di controllo **Abilita Single Sign-On SAML**.

    b. Fare clic su **Importa** per caricare il certificato scaricato da Azure AD in **Identity Provider Certificate** (Certificato del Provider di identità).
    
    c. Nella casella di testo **URL di accesso provider di identità** inserire il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    d. Come **Federation Id Location** selezionare il pulsante di opzione **Federation Id is in FEDERATION_ID Attribute element**. 

    e. Fare clic sul pulsante **Salva** .

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-boomi-test-user"></a>Creazione di un utente test di Boomi

Per consentire agli utenti di Azure AD di accedere a Boomi, è necessario eseguirne il provisioning in Boomi. Nel caso di Boomi, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di Boomi come amministratore.

2. Dopo aver effettuato l'accesso, passare a **Gestione utenti** e scegliere **Utenti**.

    ![Utenti](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Utenti")

3. Fare clic sull'icona **+**. Si aprirà la finestra di dialogo **Add/Maintain User Roles** (Aggiungi/Gestisci ruoli utente).

    ![Utenti](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Utenti")

    ![Utenti](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Utenti")

    a. Nella casella di testo **Indirizzo di posta elettronica utente** digitare l'indirizzo di posta elettronica dell'utente, ad esempio BrittaSimon@contoso.com.
    
    b. Digitare il nome dell'utente, ad esempio Britta, nella casella di testo **Nome**.

    c. Digitare il nome dell'utente, ad esempio Simon, nella casella di testo **Cognome**.
    
    d. Immettere l'**ID federazione** dell'utente. Ogni utente deve avere un ID federazione che lo identifichi in modo univoco all'interno dell'account.
    
    e. Assegnare il ruolo **Utente standard** all'utente. Non assegnare il ruolo Amministratore perché altrimenti l'utente avrebbe l'accesso normale ad AtomSphere, nonché l'accesso Single Sign-on.
    
    f. Fare clic su **OK**.
    
    > [!NOTE]
    > L'utente non riceverà un messaggio di notifica di benvenuto contenente una password che può essere utilizzata per accedere all'account di AtomSphere perché la password viene gestita tramite il provider di identità. È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Boomi per eseguire il provisioning degli account utente di AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Boomi.

![Assegna utente][200] 

**Per assegnare Britta Simon a Boomi, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Boomi**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Boomi nel pannello di accesso, si accederà automaticamente all'applicazione Boomi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png

