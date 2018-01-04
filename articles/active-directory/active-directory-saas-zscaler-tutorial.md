---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: ffb413289e9bc284c8043bc867ab00a47c8e1516
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler

Questa esercitazione descrive come integrare Zscaler con Azure Active Directory (Azure AD).

L'integrazione di Zscaler con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Zscaler
- È possibile abilitare gli utenti per l'accesso automatico a Zscaler (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zscaler sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Zscaler abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Zscaler dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-zscaler-from-the-gallery"></a>Aggiunta di Zscaler dalla raccolta
Per configurare l'integrazione di Zscaler in Azure AD, è necessario aggiungere Zscaler dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Zscaler dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Zscaler**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_search.png)

5. Nel pannello dei risultati selezionare **Zscaler** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Zscaler in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Zscaler che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Zscaler.

Per stabilire la relazione di collegamento, in Zscaler assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Zscaler, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Configurazione delle impostazioni proxy](#configuring-proxy-settings)**: per configurare le impostazioni proxy in Internet Explorer
3. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente di test di Zscaler](#creating-a-zscaler-test-user)**: per avere una controparte di Britta Simon in Zscaler collegata alla rappresentazione dell'utente in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Zscaler.

**Per configurare Single Sign-On di Azure AD con Zscaler, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Zscaler** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_samlbase.png)

3. Nella sezione **URL e dominio Zscaler** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.zsclaer.net`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Zscaler](https://www.zscaler.com/company/contact). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Zscaler** fare clic su **Configura Zscaler** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Zscaler come amministratore.

8. Scegliere **Amministrazione**dal menu disponibile nella parte superiore.
   
    ![Amministrazione](./media/active-directory-saas-zscaler-tutorial/ic800206.png "Amministrazione")

9. In **Manage Administrators & Roles** (Gestisci amministratori e ruoli) fare clic su **Manage Users & Authentication** (Gestisci utenti e autenticazione).   
            
    ![Gestire utenti e autenticazione](./media/active-directory-saas-zscaler-tutorial/ic800207.png "Gestire utenti e autenticazione")

10. Nella sezione **Choose Authentication Options for your Organization** seguire questa procedura:   
                
    ![Autenticazione](./media/active-directory-saas-zscaler-tutorial/ic800208.png "Autenticazione")
   
    a. Selezionare **Authenticate using SAML Single Sign-On**.

    b. Fare clic su **Configure SAML Single Sign-On Parameters**.

11. Nella pagina della finestra di dialogo **Configure SAML Single Sign-On Parameters** (Configura parametri accesso Single Sign-On SAML) procedere come descritto di seguito e quindi fare clic su **Done** (Fine)

    ![Single Sign-On](./media/active-directory-saas-zscaler-tutorial/ic800209.png "Single Sign-On")
    
    a. Incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **URL of the SAML Portal to which users are sent for authentication** (URL del portale di SAML a cui vengono indirizzati gli utenti per l'autenticazione).
    
    b. Nella casella di testo **Attribute containing Login Name** digitare **NameID**.
    
    c. Per caricare il certificato scaricato fare clic su **Zscaler pem**.
    
    d. Selezionare **Enable SAML Auto-Provisioning**.

12. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:

    ![Amministrazione](./media/active-directory-saas-zscaler-tutorial/ic800210.png "Amministrazione")
    
    a. Fare clic su **Save**.

    b. Fare clic su **Attiva subito**.

## <a name="configuring-proxy-settings"></a>Configurazione delle impostazioni proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni proxy in Internet Explorer

1. Avviare **Internet Explorer**.

2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.   
    
     ![Opzioni Internet](./media/active-directory-saas-zscaler-tutorial/ic769492.png "Opzioni Internet")

3. Fare clic sulla scheda **Connessioni** .   
  
     ![Connessioni](./media/active-directory-saas-zscaler-tutorial/ic769493.png "Connessioni")

4. Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.

5. Nella sezione del server proxy seguire questa procedura:   
   
    ![Server proxy](./media/active-directory-saas-zscaler-tutorial/ic769494.png "Server proxy")

    a. Selezionare **Usa un server proxy per la LAN**.

    b. Nella casella di testo Indirizzo digitare **gateway.zscaler.net**.

    c. Nella casella di testo Porta digitare **80**.

    d. Selezionare **Ignora server proxy per indirizzi locali**.

    e. Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)**.

6. Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-zscaler-test-user"></a>Creazione di un utente di test di Zscaler

Per consentire agli utenti di Azure AD di accedere a Zscaler, è necessario effettuarne il provisioning in Zscaler.  
Nel caso di Zscaler, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:

1. Accedere al tenant **Zscaler** .

2. Fare clic su **Administration**.   
   
    ![Amministrazione](./media/active-directory-saas-zscaler-tutorial/ic781035.png "Amministrazione")

3. Fare clic su **User Management**.   
        
     ![Aggiungi](./media/active-directory-saas-zscaler-tutorial/ic781036.png "Aggiungi")

4. Nella scheda **Utenti** fare clic su **Aggiungi**.
      
    ![Aggiungi](./media/active-directory-saas-zscaler-tutorial/ic781037.png "Aggiungi")

5. Nella sezione Add User seguire questa procedura:
        
    ![Aggiungere un utente](./media/active-directory-saas-zscaler-tutorial/ic781038.png "Aggiungere un utente")
   
    a. Digitare **ID utente**, **nome visualizzato per l'utente**, **password**, **conferma della password** e quindi selezionare **gruppi** e **reparto** per un account AAD valido di cui si vuole eseguire il provisioning.

    b. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zscaler per eseguire il provisioning degli account utente Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Zscaler.

![Assegna utente][200] 

**Per assegnare Britta Simon a Zscaler, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Zscaler**.

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Zscaler nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Zscaler.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_203.png

