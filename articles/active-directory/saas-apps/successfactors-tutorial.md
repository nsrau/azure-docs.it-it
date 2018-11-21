---
title: 'Esercitazione: Integrazione di Azure Active Directory con SuccessFactors | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 89224b32efaecdf7a2797b034b1beac7ad191ee5
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685226"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Esercitazione: Integrazione di Azure Active Directory con SuccessFactors

Questa esercitazione descrive come integrare SuccessFactors con Azure Active Directory (Azure AD).

L'integrazione di SuccessFactors con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SuccessFactors.
- È possibile abilitare gli utenti per l'accesso automatico a SuccessFactors (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SuccessFactors, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SuccessFactors abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SuccessFactors dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-successfactors-from-the-gallery"></a>Aggiunta di SuccessFactors dalla raccolta

Per configurare l'integrazione di SuccessFactors in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SuccessFactors dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SuccessFactors**, selezionare **SuccessFactors** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SuccessFactors nell'elenco dei risultati](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SuccessFactors con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di SuccessFactors che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SuccessFactors.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SuccessFactors, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di SuccessFactors](#creating-a-successfactors-test-user)**: per avere una controparte di Britta Simon in SuccessFactors collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SuccessFactors.

**Per configurare l'accesso Single Sign-On di Azure AD con SuccessFactors, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SuccessFactors** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di SuccessFactors](./media/successfactors-tutorial/tutorial_successfactors_url.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Contattare il [team di supporto clienti di SuccessFactors](https://www.successfactors.com/support.html) per ottenere questi valori. 

5. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Certificato (Base64)** e salvare il file di certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. Nella sezione **Configura SuccessFactors** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di SuccessFactors](common/configuresection.png)

7. In un'altra finestra del Web browser accedere al **portale di amministrazione di SuccessFactors** come amministratore.
    
8. Vedere **Sicurezza applicazione** e **Single Sign On Feature** (Funzionalità di accesso Single Sign-On). 

9. Inserire un valore qualsiasi in **Reset Token** (Reimposta token) e fare clic su **Save Token** (Salva token) per abilitare SSO SAML.
   
    ![Configurazione dell'accesso Single Sign-On sul lato app][11]

    > [!NOTE] 
    > Questo valore viene usato come opzione di attivazione o disattivazione. Se viene salvato un valore, SSO SAML è ON. Se viene salvato un valore vuoto, SSO SAML è OFF.

10. Nella schermata sottostante eseguire le azioni seguenti:
   
    ![Configurazione dell'accesso Single Sign-On sul lato app][12]
   
    a. Selezionare il pulsante di opzione **SAML v2 SSO** (SSO SAML v2)
   
    b. Impostare **SAML Asserting Party Name** (Nome dell'entità di asserzione SAML), ad esempio Autorità di certificazione SAML + Nome società.
   
    c. Nella casella di testo **URL autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.
   
    d. Selezionare **Asserzione** per **Require Mandatory Signature** (Richiedi firma obbligatoria).
   
    e. Selezionare **Abilitato** per **Enable SAML Flag** (Abilita flag SAML).
   
    f. Selezionare **No** per **Login Request Signature(SF Generated/SP/RP)** (Firma richiesta di accesso - Generata da SF/SP/RP).
   
    g. Selezionare **Browser/Post Profile** (Profilo browser/post) per **SAML Profile** (Profilo SAML).
   
    h. Selezionare **No** per **Enforce Certificate Valid Period** (Applicare periodo di validità certificato).
   
    i. Copiare il contenuto del file del certificato scaricato dal portale di Azure, copiarlo e incollarlo nella casella di testo **SAML Verifying Certificate** (Verifica certificato SAML).

    > [!NOTE] 
    > Il contenuto del certificato deve avere i tag di inizio e fine certificato.

11. Passare a SAML V2 e seguire questa procedura:
   
    ![Configurazione dell'accesso Single Sign-On sul lato app][13]
   
    a. Selezionare **Sì** per **Support SP-initiated Global Logout** (Supporto disconnessione globale avviata da SP).
   
    b. Nella casella di testo **Global Logout Service URL (LogoutRequest destination)** (URL del servizio disconnessione globale - Destinazione LogoutRequest) incollare il valore **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.
   
    c. Selezionare **No** per **Require sp must encrypt all NameID element** (Richiesta a sp di crittografare tutti gli elementi NameID).
   
    d. Selezionare **non specificato** per **NameID Format** (Formato NameID).
   
    e. Selezionare **Sì** per **Enable sp initiated login (AuthnRequest)** (Abilita accesso avviato da sp - AuthnRequest).
   
    f. Nella casella di testo **Send request as Company-Wide issuer** (Invia richiesta come autorità di certificazione a livello di azienda) incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML), copiato dal portale di Azure.

12. Eseguire questi passaggi se si vuole che i nomi utente di account di accesso utente non facciano distinzione tra maiuscole e minuscole.
   
    ![Configure Single Sign-On][29]
    
    a. Vedere **Impostazioni società** in basso.
   
    b. Selezionare la casella di controllo accanto alla **Enable Non-Case-Sensitive Username**(Abilita nome utente senza distinzione maiuscole/minuscole).
   
    c.Fare clic su **Save**(Salva).
   
    > [!NOTE] 
    > Se si prova ad abilitare questa opzione, il sistema controlla se viene creato un nome di account di accesso SAML duplicato. Ad esempio, se il cliente ha nomi utente Utente1 e utente1. L'annullamento della distinzione maiuscole/minuscole crea questi duplicati. Il sistema visualizza un messaggio di errore e non abilita la funzionalità. Il cliente deve modificare uno dei nomi utente in modo che sia scritto diversamente.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-a-successfactors-test-user"></a>Creazione di un utente test SuccessFactors

Per consentire agli utenti di Azure AD di accedere a SuccessFactors, è necessario effettuarne il provisioning in SuccessFactors.  
Nel caso di SuccessFactors, il provisioning è un'attività manuale.

Per creare utenti in SuccessFactors, è necessario contattare il [team di supporto di SuccessFactors](https://www.successfactors.com/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SuccessFactors.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco di applicazioni, selezionare **SuccessFactors**.

    ![Configure Single Sign-On](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SuccessFactors nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione SuccessFactors.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
