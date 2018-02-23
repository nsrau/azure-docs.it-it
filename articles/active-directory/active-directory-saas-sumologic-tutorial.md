---
title: 'Esercitazione: Integrazione di Azure Active Directory con SumoLogic| Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 9b737ce50c1b52c771af275f5722bfd92b2f1491
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Esercitazione: Integrazione di Azure Active Directory con SumoLogic

Questa esercitazione descrive come integrare SumoLogic con Azure Active Directory (Azure AD).

L'integrazione di SumoLogic con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SumoLogic
- È possibile abilitare gli utenti per l'accesso automatico a SumoLogic (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con SumoLogic, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SumoLogic abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SumoLogic dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sumologic-from-the-gallery"></a>Aggiunta di SumoLogic dalla raccolta
Per configurare l'integrazione di SumoLogic in Azure AD, è necessario aggiungere SumoLogic dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SumoLogic dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **SumoLogic**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_search.png)

5. Nel pannello dei risultati selezionare **SumoLogic** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SumoLogic usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di SumoLogic corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SumoLogic.

Per stabilire la relazione di collegamento, in SumoLogic assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SumoLogic, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di SumoLogic](#creating-a-sumologic-test-user)**: per avere una controparte di Britta Simon in SumoLogic collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SumoLogic.

**Per configurare l'accesso Single Sign-On di Azure AD con SumoLogic, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SumoLogic** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_samlbase.png)

3. Nella sezione **URL e dominio SumoLogic** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenantname>.SumoLogic.com`

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SumoLogic](https://www.sumologic.com/contact-us/). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-sumologic-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di SumoLogic** fare clic su **Configura SumoLogic** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido.**

    ![Configure Single Sign-On](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di SumoLogic come amministratore.

8. Passare a **Manage (Gestisci) \> Security (Sicurezza)**.
   
    ![Gestione](./media/active-directory-saas-sumologic-tutorial/ic778556.png "Gestione")

9. Fare clic su **SAML**.
   
    ![Impostazioni di sicurezza globale](./media/active-directory-saas-sumologic-tutorial/ic778557.png "Impostazioni di sicurezza globale")

10. Dall'elenco **Select a configuration or create a new one** (Selezionare o creare una configurazione) selezionare **Azure AD**, quindi fare clic su **Configure** (Configura).
   
    ![Configurare SAML 2.0](./media/active-directory-saas-sumologic-tutorial/ic778558.png "Configurare SAML 2.0")

11. Nella finestra di dialogo **Configura SAML 2.0** , eseguire la procedura seguente:
   
    ![Configurare SAML 2.0](./media/active-directory-saas-sumologic-tutorial/ic778559.png "Configurare SAML 2.0")
   
    a. Nella casella di testo **Configuration Name** (Nome configurazione) digitare **Azure AD**. 

    b. Selezionare **Debug Mode**.

    c. Nella casella di testo **Issuer** (Autorità emittente) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure. 

    d. Nella casella di testo **Authn Request URL** (URL richiesta di autenticazione) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    e. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificate X.509** .

    f. Per **Email Attribute** (Attributo e-mail), selezionare **Use SAML subject** (Usa oggetto SAML).  

    g. Selezionare **SP initiated Login Configuration**.

    h. Nella casella di testo **Login Path** (Percorso di accesso) digitare **Azure** e fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sumologic-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sumologic-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sumologic-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sumologic-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-sumologic-test-user"></a>Creazione di un utente di test di SumoLogic

Per consentire agli utenti di Azure AD di accedere a SumoLogic, è necessario effettuarne il provisioning in SumoLogic.  

* Nel caso di SumoLogic, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **SumoLogic** .

2. Passare a **Manage (Gestisci) \> Users (Utenti)**.
   
    ![Utenti](./media/active-directory-saas-sumologic-tutorial/ic778561.png "Utenti")

3. Fare clic su **Aggiungi**.
   
    ![Utenti](./media/active-directory-saas-sumologic-tutorial/ic778562.png "Utenti")

4. Nella finestra di dialogo **New User** , eseguire la procedura seguente:
   
    ![Nuovo utente](./media/active-directory-saas-sumologic-tutorial/ic778563.png "Nuovo utente") 
 
    a. Digitare le informazioni correlate dell'account Azure AD di cui si vuole effettuare il provisioning nelle caselle di testo **First Name** (Nome), **Last Name** (Cognome) e **Email** (Posta elettronica).
  
    b. Selezionare un ruolo.
  
    c. Per **Status** (Stato) selezionare **Active** (Attivo).
  
    d. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento di creazione di account utente SumoLogic o qualsiasi API fornita da SumoLogic per eseguire il provisioning degli account utente di Azure AD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SumoLogic.

![Assegna utente][200] 

**Per assegnare Britta Simon a SumoLogic, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SumoLogic**.

    ![Configure Single Sign-On](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SumoLogic nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SumoLogic.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_203.png

