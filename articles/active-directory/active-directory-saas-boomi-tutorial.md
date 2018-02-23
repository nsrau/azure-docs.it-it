---
title: 'Esercitazione: Integrazione di Azure Active Directory con Boomi | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 6d1af05f40d6e57b2f6128261828791be7e516c7
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Esercitazione: Integrazione di Azure Active Directory con Boomi

Questa esercitazione descrive come integrare Boomi con Azure Active Directory (Azure AD).

L'integrazione di Boomi con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Boomi.
- È possibile abilitare gli utenti per l'accesso automatico a Boomi (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Boomi, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Boomi abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Boomi dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-boomi-from-the-gallery"></a>Aggiunta di Boomi dalla raccolta
Per configurare l'integrazione di Boomi in Azure AD, è necessario aggiungere Boomi dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Boomi dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Boomi** selezionare **Boomi** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Boomi nell'elenco dei risultati](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Boomi in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Boomi che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Boomi.

Per stabilire la relazione di collegamento, in Boomi assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Boomi, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Boomi](#create-a-boomi-test-user)**: per avere una controparte di Britta Simon in Boomi collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Boomi.

**Per configurare l'accesso Single Sign-On di Azure AD con Boomi, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Boomi** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_samlbase.png)

3. Nella sezione **URL e dominio Boomi** eseguire i passaggi descritti di seguito:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Boomi](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://platform.boomi.com/`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE] 
    > Il valore di URL di risposta non è reale. è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere tale valore, contattare il [team di supporto di Boomi](https://boomi.com/company/contact/).
 
4. L'applicazione Boomi prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_attribute.png)

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** per ogni riga illustrata nella tabella seguente, seguire questa procedura:

    | Nome attributo | Valore attributo |
    | -------------- | --------------- |
    | FEDERATION_ID | user.mail |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_officespace_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

6. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

8. Nella sezione **Configurazione di Boomi** fare clic su **Configura Boomi** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configurazione di Boomi](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_configure.png) 

9. In un'altra finestra del Web browser accedere al sito aziendale di Boomi come amministratore. 

10. Passare a **Nome società** e scegliere **Configurazione**.

11. Fare clic sulla scheda **Opzioni SSO** e seguire questa procedura.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. Selezionare la casella di controllo **Abilita Single Sign-On SAML**.

    b. Fare clic su **Importa** per caricare il certificato scaricato da Azure AD in **Identity Provider Certificate** (Certificato del Provider di identità).
    
    c. Nella casella di testo **URL di accesso provider di identità** inserire il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    d. Come **Federation Id Location** selezionare il pulsante di opzione **Federation Id is in FEDERATION_ID Attribute element**. 

    e. Fare clic sul pulsante **Salva** .

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-boomi-test-user"></a>Creare un utente test di Boomi

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Boomi.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Boomi, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Boomi**.

    ![Collegamento di Boomi nell'elenco delle applicazioni](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Boomi nel pannello di accesso, si accederà automaticamente all'applicazione Boomi.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

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

