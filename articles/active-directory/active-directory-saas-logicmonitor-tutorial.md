---
title: 'Esercitazione: Integrazione di Azure Active Directory con LogicMonitor | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: e49960cac868f80af3e9165a9f75e49be87515f4
ms.contentlocale: it-it
ms.lasthandoff: 06/23/2017

---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Esercitazione: Integrazione di Azure Active Directory con LogicMonitor

Questa esercitazione descrive come integrare LogicMonitor con Azure Active Directory (Azure AD).

L'integrazione di LogicMonitor con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a LogicMonitor
- È possibile abilitare gli utenti per l'accesso automatico a LogicMonitor (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con LogicMonitor, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di LogicMonitor abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di LogicMonitor dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-logicmonitor-from-the-gallery"></a>Aggiunta di LogicMonitor dalla raccolta
Per configurare l'integrazione di LogicMonitor in Azure AD, è necessario aggiungere LogicMonitor dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere LogicMonitor dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **LogicMonitor**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_search.png)

5. Nel pannello dei risultati selezionare **LogicMonitor** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con LogicMonitor usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di LogicMonitor che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in LogicMonitor.

Per stabilire la relazione di collegamento, in LogicMonitor assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con LogicMonitor, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di LogicMonitor](#creating-a-logicmonitor-test-user)**: per avere una controparte di Britta Simon in LogicMonitor collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione LogicMonitor.

**Per configurare l'accesso Single Sign-On di Azure AD con LogicMonitor, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **LogicMonitor** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

3. Nella sezione **URL e dominio LogicMonitor** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.logicmonitor.com`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.logicmonitor.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di LogicMonitor](https://www.logicmonitor.com/contact/). 
 


4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_400.png)

6. Accedere al sito aziendale di **LogicMonitor** come amministratore.

7. Nel menu in alto fare clic su **Impostazioni**.
   
   ![Impostazioni](./media/active-directory-saas-logicmonitor-tutorial/ic790052.png "Impostazioni")

8. Nella barra di spostamento sul lato sinistro fare clic su **Single Sign-On**
   
   ![Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/ic790053.png "Single Sign-On")

9. Nella sezione **Impostazioni Single Sign-on (SSO)** eseguire questa procedura:
   
   ![Impostazioni di Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/ic790054.png "Impostazioni di Single Sign-On")
   
   a. Selezionare **Attiva Single Sign-On**.

   b. In **Default Role Assignment** (Assegnazione ruolo predefinito) selezionare **readonly**.
   
   c. Aprire il file di metadati scaricato in Blocco note, quindi incollare il contenuto del file nella casella di testo **Metadati del provider di identità** .
   
   d. Fare clic su **Salva modifiche**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-logicmonitor-test-user"></a>Creazione di un utente test di LogicMonitor

Per consentire agli utenti di AAD di accedere, è necessario eseguirne il provisioning nell’applicazione LogicMonitor utilizzando i relativi nomi utente di Azure Active Directory.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di LogicMonitor come amministratore.

2. Nel menu in alto fare clic su **Settings** (Impostazioni) e quindi fare clic su **Roles and Users** (Ruoli e utenti).
   
   ![Ruoli e utenti](./media/active-directory-saas-logicmonitor-tutorial/ic790056.png "Ruoli e utenti")

3. Fare clic su **Aggiungi**.

4. Nella sezione **Aggiungi un account** eseguire la procedura seguente:
   
   ![Aggiungere un account](./media/active-directory-saas-logicmonitor-tutorial/ic790057.png "Aggiungere un account")
   
   a. Nelle caselle di testo **Username** (Nome utente), **Email** (Indirizzo di posta elettronica), **Password** e **Retype password** (Conferma password) digitare i valori dell'utente di Azure Active Directory di cui si vuole eseguire il provisioning.
   
   b. Selezionare **Roles** (Ruoli), **View Permissions** (Visualizza autorizzazioni) e **Status** (Stato).
   
   c. Fare clic su **Submit**.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di LogicMonitor o le API fornite da LogicMonitor per eseguire il provisioning degli account utente di Azure Active Directory. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a LogicMonitor.

![Assegna utente][200] 

**Per assegnare Britta Simon a LogicMonitor, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **LogicMonitor**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
 
Quando si fa clic sul riquadro LogicMonitor nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione LogicMonitor.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_203.png


