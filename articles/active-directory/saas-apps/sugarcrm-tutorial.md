---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sugar CRM | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a00c9cb0ce1bd8a6f36070e81df1185ef23a307
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201771"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Esercitazione: Integrazione di Azure Active Directory con Sugar CRM

Questa esercitazione descrive come integrare Sugar CRM con Azure Active Directory (Azure AD).

L'integrazione di Sugar CRM con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Sugar CRM
- È possibile abilitare gli utenti per l'accesso automatico a Sugar CRM (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Sugar CRM, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Sugar CRM abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Sugar CRM dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sugar-crm-from-the-gallery"></a>Aggiunta di Sugar CRM dalla raccolta
Per configurare l'integrazione di Sugar CRM in Azure AD, è necessario aggiungere Sugar CRM dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Sugar CRM dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Sugar CRM**.

    ![Creazione di un utente test di Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

1. Nel pannello dei risultati selezionare **Sugar CRM** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Sugar CRM usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Sugar CRM corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sugar CRM.

Per stabilire la relazione di collegamento, in Sugar CRM assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Sugar CRM, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente di test di Sugar CRM](#creating-a-sugar-crm-test-user)**: per avere una controparte di Britta Simon in Sugar CRM collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Sugar CRM.

**Per configurare l'accesso Single Sign-On di Azure AD con Sugar CRM, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Sugar CRM** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

1. Nella sezione **URL e dominio Sugar CRM** seguire questa procedura:

    ![Configure Single Sign-On](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Sugar CRM](https://support.sugarcrm.com/). 
 
1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/sugarcrm-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Sugar CRM** fare clic su **Configura Sugar CRM** per aprire la finestra **Configura accesso**. Copiare i valori **Sign-Out URL (URL di disconnessione) e SAML Single Sign-On Service URL (URL servizio Single Sign-On SAML)** dalla sezione **Quick Reference** (Riferimento rapido).

    ![Configure Single Sign-On](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

1. In un'altra finestra del Web browser accedere al sito aziendale di Sugar CRM come amministratore.

1. Passare alla pagina **Admin**.
   
    ![Amministratore](./media/sugarcrm-tutorial/ic795888.png "Amministratore")

1. Nella sezione **Amministrazione**, fare clic su **Gestione password**.
   
    ![Amministrazione](./media/sugarcrm-tutorial/ic795889.png "Amministrazione")

1. Selezionare **Abilita autenticazione SAML**.
   
    ![Amministrazione](./media/sugarcrm-tutorial/ic795890.png "Amministrazione")

1. Nella sezione **SAML Authentication** seguire questa procedura:
   
    ![Autenticazione SAML](./media/sugarcrm-tutorial/ic795891.png "Autenticazione SAML")  
 
    a. Nella casella di testo **Login URL** (URL di accesso) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
  
    b. Nella casella di testo **SLO URL** (URL SLO) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.
  
    c. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificate X.509** .
  
    d. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/sugarcrm-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/sugarcrm-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/sugarcrm-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-sugar-crm-test-user"></a>Creazione di un utente di test di Sugar CRM

Per consentire agli utenti di Azure AD di accedere a Sugar CRM, è necessario effettuarne il provisioning in Sugar CRM.

Nel caso di Sugar CRM, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Sugar CRM** come amministratore.

1. Passare alla pagina **Admin**.
   
    ![Amministratore](./media/sugarcrm-tutorial/ic795888.png "Amministratore")

1. Nella sezione **Administration** (Amministrazione) fare clic su **User Management** (Gestione utenti).
   
    ![Amministrazione](./media/sugarcrm-tutorial/ic795893.png "Amministrazione")

1. Passare a **Users \> Create New User** (Utenti > Crea nuovo utente).
   
    ![Creazione di un nuovo utente](./media/sugarcrm-tutorial/ic795894.png "Creazione di un nuovo utente")

1. Nella scheda **Profilo utente** eseguire la procedura seguente:
   
    ![Nuovo utente](./media/sugarcrm-tutorial/ic795895.png "Nuovo utente")

    a. Digitare il **nome utente**, il **cognome** e l'**indirizzo e-mail** di un utente di Azure Active Directory valido nelle caselle di testo correlate.
  
1. Per **Status** (Stato) selezionare **Active** (Attivo).

1. Nella scheda Password, eseguire la procedura seguente:
   
    ![Nuovo utente](./media/sugarcrm-tutorial/ic795896.png "Nuovo utente")

    a. Digitare la password nella casella di controllo correlata.

    b. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento di creazione di account utente Sugar CRM o qualsiasi API fornita da Sugar CRM per eseguire il provisioning degli account utente di Azure AD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Sugar CRM.

![Assegna utente][200] 

**Per assegnare Britta Simon a Sugar CRM, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Sugar CRM**.

    ![Configure Single Sign-On](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Sugar CRM nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Sugar CRM.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png

