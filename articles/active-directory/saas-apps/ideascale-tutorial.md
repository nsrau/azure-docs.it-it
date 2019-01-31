---
title: 'Esercitazione: Integrazione di Azure Active Directory con IdeaScale | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 53538fe741a37312fa2cb2860582ae46ef430853
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167360"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Esercitazione: Integrazione di Azure Active Directory con IdeaScale

Questa esercitazione descrive come integrare IdeaScale con Azure Active Directory (Azure AD).

L'integrazione di IdeaScale con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a IdeaScale
- È possibile abilitare gli utenti per l'accesso automatico Single Sign-On a IdeaScale con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con IdeaScale sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di IdeaScale abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di IdeaScale dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-ideascale-from-the-gallery"></a>Aggiunta di IdeaScale dalla raccolta
Per configurare l'integrazione di IdeaScale in Azure AD è necessario aggiungere IdeaScale dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere IdeaScale dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **IdeaScale**.

    ![Creazione di un utente test di Azure AD](./media/ideascale-tutorial/tutorial_ideascale_search.png)

1. Nel pannello dei risultati selezionare **IdeaScale** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con IdeaScale mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di IdeaScale corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in IdeaScale.

Per stabilire la relazione di collegamento, in IdeaScale assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con IdeaScale è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di IdeaScale](#creating-an-ideascale-test-user)**: per avere una controparte di Britta Simon in IdeaScale collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione IdeaScale.

**Per configurare l'accesso Single Sign-On di Azure AD con IdeaScale, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **IdeaScale** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/ideascale-tutorial/tutorial_ideascale_samlbase.png)

1. Nella sezione **URL e dominio IdeaScale** seguire questa procedura:

    ![Configure Single Sign-On](./media/ideascale-tutorial/tutorial_ideascale_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.ideascale.com`

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di IdeaScale](https://support.ideascale.com/). 
 
1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/ideascale-tutorial/tutorial_ideascale_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/ideascale-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di IdeaScale** fare clic su **Configura IdeaScale** per aprire la finestra **Configura accesso**. Copiare i valori **Sign-Out URL (URL di disconnessione) e SAML Entity ID (ID entità SAML)** dalla **sezione Quick Reference** (Riferimento rapido).

    ![Configure Single Sign-On](./media/ideascale-tutorial/tutorial_ideascale_configure.png) 

1. In un'altra finestra del Web browser accedere al sito aziendale di IdeaScale come amministratore.

1. Passare a **Impostazioni Community**.
   
    ![Impostazioni Community](./media/ideascale-tutorial/ic790847.png "Impostazioni Community")

1. Passare a **Security (Sicurezza) \> Single Signon Settings (Impostazioni di Single Sign-O)**.
   
    ![Impostazioni di Single Sign-On](./media/ideascale-tutorial/ic790848.png "Impostazioni di Single Sign-On")

1. In **Single-Signon Type** (Tipo di accesso Single Sign-On) selezionare **SAML 2.0**.
   
    ![Single Signon Type](./media/ideascale-tutorial/ic790849.png "Single Signon Type")

1. Nella finestra di dialogo **Impostazioni di Single Sign-O** seguire questa procedura:
   
    ![Impostazioni di Single Sign-On](./media/ideascale-tutorial/ic790850.png "Impostazioni di Single Sign-On")
   
    a. Nella casella di testo **SAML IdP Entity ID** (ID entità IdP SAML) incollare il valore **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure.

    b. Copiare il contenuto del file dei metadati scaricato dal portale di Azure e incollarlo nella casella di testo **SAML IdP Metadata** (Metadati IdP SAML).

    c. Nella casella di testo **Logout Success URL** (URL disconnessione riuscita) incollare il valore di **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.

    d. Fare clic su **Salva modifiche**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/ideascale-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/ideascale-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/ideascale-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/ideascale-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-an-ideascale-test-user"></a>Creazione di un utente test di IdeaScale

Per consentire agli utenti di Azure AD di accedere a IdeaScale, è necessario eseguire il provisioning degli utenti in IdeaScale. Nel caso di IdeaScale, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **IdeaScale** come amministratore.

1. Passare a **Impostazioni Community**.
   
    ![Impostazioni Community](./media/ideascale-tutorial/ic790847.png "Impostazioni Community")

1. Passare a **Basic Settings (Impostazioni di base) \> Member Management (Gestione membri)**.

1. Fare clic su **Aggiungi membro**.
   
    ![Member Management](./media/ideascale-tutorial/ic790852.png "Member Management")

1. Nella sezione Add New Member seguire questa procedura:
   
    ![Add New Member](./media/ideascale-tutorial/ic790853.png "Add New Member")
   
    a. Nella casella di testo **Indirizzi email** digitare l'indirizzo di posta elettronica di un account di AAD valido di cui si vuole eseguire il provisioning.
   
    b. Fare clic su **Salva modifiche**. 
   
    >[!NOTE]
    >Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento per confermare l'account e attivarlo.
      
>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da IdeaScale per eseguire il provisioning degli account utente di AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a IdeaScale.

![Assegna utente][200] 

**Per assegnare Britta Simon a IdeaScale, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **IdeaScale**.

    ![Configure Single Sign-On](./media/ideascale-tutorial/tutorial_ideascale_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On


Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro IdeaScale nel pannello di accesso si accede automaticamente all'applicazione IdeaScale.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ideascale-tutorial/tutorial_general_01.png
[2]: ./media/ideascale-tutorial/tutorial_general_02.png
[3]: ./media/ideascale-tutorial/tutorial_general_03.png
[4]: ./media/ideascale-tutorial/tutorial_general_04.png

[100]: ./media/ideascale-tutorial/tutorial_general_100.png

[200]: ./media/ideascale-tutorial/tutorial_general_200.png
[201]: ./media/ideascale-tutorial/tutorial_general_201.png
[202]: ./media/ideascale-tutorial/tutorial_general_202.png
[203]: ./media/ideascale-tutorial/tutorial_general_203.png

