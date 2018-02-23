---
title: 'Esercitazione: Integrazione di Azure Active Directory con ITRP | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: aacfdbf9d644017c242feec90c97cd34cbd713e5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Esercitazione: Integrazione di Azure Active Directory con ITRP

Questa esercitazione descrive come integrare ITRP con Azure Active Directory (Azure AD).

L'integrazione di ITRP con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a ITRP
- È possibile abilitare gli utenti per l'accesso automatico a ITRP (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con ITRP, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di ITRP abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ITRP dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-itrp-from-the-gallery"></a>Aggiunta di ITRP dalla raccolta
Per configurare l'integrazione di ITRP in Azure AD, è necessario aggiungere ITRP dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ITRP dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **ITRP**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_search.png)

5. Nel pannello dei risultati selezionare **ITRP** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ITRP usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di ITRP corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ITRP.

Per stabilire la relazione di collegamento, in ITRP assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con ITRP, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di ITRP](#creating-an-itrp-test-user)**: per avere una controparte di Britta Simon in ITRP collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ITRP.

**Per configurare l'accesso Single Sign-On di Azure AD con ITRP, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ITRP** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_samlbase.png)

3. Nella sezione **URL e dominio ITRP** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenant-name>.itrp.com`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<tenant-name>.itrp.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ITRP](https://www.itrp.com/support). 
 
4. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** del certificato.

    ![Configure Single Sign-On](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-itrp-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di ITRP** fare clic su **Configura ITRP** per aprire la finestra **Configura accesso**. Copiare l'**URL del servizio Single Sign-On SAML e l'URL di disconnessione** dalla **sezione di riferimento rapido**.

    ![Configure Single Sign-On](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di ITRP come amministratore.

8. Nel barra degli strumenti in alto fare clic su **Impostazioni**.
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/ic775570.png "ITRP")

8. Nel riquadro di navigazione sinistro selezionare **Single Sign-On**.
   
    ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/ic775571.png "Single Sign-On")

9. Nella sezione di configurazione per Single Sign-On seguire questa procedura:
   
    ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/ic775572.png "Single Sign-On")
    
    ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/ic775573.png "Single Sign-On")   

    a. Fare clic su **Abilita**.

    b. Nella casella di testo **Remote Log Out URL** (URL di disconnessione remota) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    c. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    Nella casella di testo **Certificate Fingerprint** (Impronta digitale certificato) incollare il valore **Identificazione personale** del certificato copiato dal portale di Azure. 
      
10. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-itrp-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-itrp-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-itrp-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-itrp-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-itrp-test-user"></a>Creazione di un utente di test di ITRP

Per consentire agli utenti di Azure AD di accedere a ITRP, è necessario effettuarne il provisioning in ITRP.  

Nel caso di ITRP, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **ITRP** .

2. Nel barra degli strumenti in alto fare clic su **Records**.
   
    ![Amministratore](./media/active-directory-saas-itrp-tutorial/ic775575.png "Amministratore")

3. Dal menu popup selezionare **Persone**.
   
    ![Persone](./media/active-directory-saas-itrp-tutorial/ic775587.png "Persone")

4. Fare clic su **Aggiungi nuova persona** ("+").
   
    ![Amministratore](./media/active-directory-saas-itrp-tutorial/ic775576.png "Amministratore")

5. Nella finestra di dialogo Add New Person seguire questa procedura:
   
    ![Utente](./media/active-directory-saas-itrp-tutorial/ic775577.png "Utente") 
      
    a. Nelle caselle di testo **Name** ed **Email** digitare il nome e l'indirizzo di posta elettronica di un account AAD valido di cui si vuole eseguire il provisioning.

    b. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ITRP per eseguire il provisioning degli account utente di AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ITRP.

![Assegna utente][200] 

**Per assegnare Britta Simon a ITRP, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **ITRP**.

    ![Configure Single Sign-On](./media/active-directory-saas-itrp-tutorial/tutorial_itrp_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ITRP nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ITRP.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-itrp-tutorial/tutorial_general_203.png

