---
title: 'Esercitazione: Integrazione di Azure Active Directory integration con E Sales Manager Remix | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>Esercitazione: Integrazione di Azure Active Directory integration con E Sales Manager Remix

Questa esercitazione descrive come integrare E Sales Manager Remix con Azure Active Directory (Azure AD).

L'integrazione di E Sales Manager Remix con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a E Sales Manager Remix.
- È possibile abilitare gli utenti per l'accesso automatico a E Sales Manager Remix (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con E Sales Manager Remix, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di E Sales Manager Remix abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di E Sales Manager Remix dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>Aggiunta di E Sales Manager Remix dalla raccolta
Per configurare l'integrazione di E Sales Manager Remix in Azure AD, è necessario aggiungere E Sales Manager Remix dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere E Sales Manager Remix dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **E Sales Manager Remix**, selezionare **E Sales Manager Remix** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![E Sales Manager Remix nell'elenco dei risultati](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con E Sales Manager Remix con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di E Sales Manager Remix che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in E Sales Manager Remix.

Per configurare e testare l'accesso Single Sign-On di Azure AD con E Sales Manager Remix, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di E Sales Manager Remix](#create-an-e-sales-manager-remix-test-user)**: per avere una controparte di Britta Simon in E Sales Manager Remix collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione E Sales Manager Remix.

**Per configurare Single Sign-On di Azure AD con E Sales Manager Remix, seguire questa procedura:**

1. Nella pagina di integrazione di dell'applicazione **E Sales Manager Remix** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. Nella sezione **URL e dominio E Sales Manager Remix Domain** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<Server-Based-URL>/<sub-domain>/esales-pc`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<Server-Based-URL>/<sub-domain>/`

    c. Copiare il valore **Identificatore** nel Blocco note. Il valore Identificatore verrà usato più avanti nell'esercitazione.
    
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di E Sales Manager Remix Client](mailto:esupport@softbrain.co.jp).

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Selezionare **Visualizza e modifica tutti gli altri attributi utente** e quindi fare clic sull'attributo **emailaddress**.
    
    ![Configurazione di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. Copiare il valore di **Spazio dei nomi** e **Nome** dell'attestazione dalla casella di testo. Generare il valore nel modello seguente - `<Namespace>/<Name>`. Questo valore verrà usato più avanti nell'esercitazione.

    ![Configurazione di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. Nella sezione **Configurazione di E Sales Manager Remix** fare clic su **Configura E Sales Manager Remix** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configurazione di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. Accedere all'applicazione E Sales Manager Remix come amministratore.

10. Selezionare **To Administrator Menu** (A menu amministratore) nel menu in alto a destra.

    ![Configurazione di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. Selezionare **System settings**>**Cooperation with external system** (Impostazioni sistema > Cooperazione con sistema esterno)

    ![Configurazione di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. Selezionare **SAML**

    ![Configurazione di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. Nella sezione **SAML Authentication** (Autenticazione SAML) seguire questa procedura:

    ![Configurazione di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Selezionare **PC version** (Versione PC)
    
    b. Selezionare **email** nell'elenco a discesa nella sezione Collaboration item (Elemento collaborazione).

    c. Nella casella di testo Collaboration item (Elemento collaborazione) incollare il **valore di attestazione** copiato dal portale di Azure, ad esempio `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Nella casella di testo **Issuer (entity ID)** (Emittente - ID entità) incollare il valore di **Identificatore** copiato dal portale di Azure nella sezione **URL e dominio E Sales Manager Remix**.

    e. Per caricare il **certificato** scaricato dal portale di Azure, selezionare **File selection** (Selezione file).

    f. Nella casella di testo **ID provider login URL** (URL accesso provider ID) incollare il valore di **URL servizio Single Sign-On SAML** copiato dal portale di Azure.

    g. Nella casella di testo **Identity Provider Logout URL** (URL disconnessione provider identità) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    h. Fare clic su **Setting complete** (Impostazione completata)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Creare un utente di test in E Sales Manager Remix

1. Accedere all'applicazione E Sales Manager Remix come amministratore.

2. Selezionare **To Administrator Menu** (A menu amministratore) nel menu in alto a destra.

    ![Configurazione di E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Selezionare **Your company settings**>**Maintenance of departments and employees** (Impostazioni azienda > Gestione reparti e dipendenti) e quindi selezionare **Employees registered** (Dipendente registrato).

    ![Utente](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. Nella sezione **New employee registration** (Registrazione nuovo dipendente) seguire questa procedura:
    
    ![Utente](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. Nella casella di testo **Employee Name** (Nome dipendente) digitare il nome dell'utente, ad esempio Britta.

    b. Compilare tutti i rispettivi campi obbligatori con le informazioni utente.
    
    c. Se si abilita SAML, l'amministratore non sarà in grado di accedere tramite la schermata di accesso, quindi concedere i privilegi di accesso amministratore all'utente selezionando la casella di controllo **Admin Login** (Accesso amministratore)

    d. Fare clic su **Registration** (Registrazione)

5. In futuro, se si intende accedere come amministratore, è necessario farlo come utente a cui è stata concessa l'autorizzazione di amministratore e quindi fare clic su **To Administrator Menu** (A menu amministratore) nel menu in alto a destra.

    ![Utente](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a E Sales Manager Remix.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a E Sales Manager Remix, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **E Sales Manager Remix**.

    ![Collegamento E Sales Manager Remix nell'elenco di applicazioni](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro E Sales Manager Remix nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione E Sales Manager Remix.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

