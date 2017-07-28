---
title: 'Esercitazione: Integrazione di Azure Active Directory con Freshservice | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: d32775fa91d3a49da1ef55e57d1d38990fa09346
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Esercitazione: Integrazione di Azure Active Directory con Freshservice

Questa esercitazione descrive come integrare Freshservice con Azure Active Directory (Azure AD).

L'integrazione di Freshservice con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Freshservice
- È possibile abilitare gli utenti per l'accesso automatico Single Sign-On a Freshservice con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Freshservice sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Freshservice abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Freshservice dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-freshservice-from-the-gallery"></a>Aggiunta di Freshservice dalla raccolta
Per configurare l'integrazione di Freshservice in Azure AD è necessario aggiungere Freshservice dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Freshservice dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Freshservice**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_search.png)

5. Nel pannello dei risultati selezionare **Freshservice** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Freshservice mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Freshservice corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Freshservice.

Per stabilire la relazione di collegamento, in Freshservice assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Freshservice è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Freshservice](#creating-a-freshservice-test-user)**: per avere una controparte di Britta Simon in Freshservice collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Freshservice.

**Per configurare l'accesso Single Sign-On di Azure AD con Freshservice, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Freshservice** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_samlbase.png)

3. Nella sezione **URL e dominio Freshservice** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<democompany>.freshservice.com`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Freshservice](https://support.freshservice.com/). 
 
4. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** del certificato.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Freshservice** fare clic su **Configura Freshservice** per aprire la finestra **Configura accesso**. Copiare i valori **Sign-Out URL (URL di disconnessione) e SAML Single Sign-On Service URL (URL servizio Single Sign-On SAML)** dalla sezione **Quick Reference** (Riferimento rapido).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Freshservice come amministratore.

8. Nel menu in alto fare clic su **Admin**.
   
    ![Amministratore](./media/active-directory-saas-freshservice-tutorial/ic790814.png "Amministratore")

9. Nel **portale dei clienti** fare clic su **Security** (Sicurezza).
   
    ![Sicurezza](./media/active-directory-saas-freshservice-tutorial/ic790815.png "Sicurezza")

10. Nella sezione **Security** seguire questa procedura:
   
    ![Single Sign-On](./media/active-directory-saas-freshservice-tutorial/ic790816.png "Single Sign-On")
   
    a. Passare a **Single Sign-On**.

    b. Selezionare **SAML SSO**.

    c. Nella casella di testo **SAML Login URL** (URL di accesso SAML) incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.

    d. Nella casella di testo **URL disconnessione** incollare il valore di **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.

    e. Nella casella di testo **Security Certificate Fingerprint** (Impronta digitale certificato di protezione) incollare il valore **IDENTIFICAZIONE PERSONALE** del certificato copiato dal portale di Azure.

    f. Fare clic su **Save**
   
> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-freshservice-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-freshservice-test-user"></a>Creazione di un utente test di Freshservice

Per consentire agli utenti di Azure AD di accedere a Freshservice, è necessario eseguire il provisioning degli utenti in Freshservice. Nel caso di FreshService, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **FreshService** come amministratore.

2. Nel menu in alto fare clic su **Admin**.
   
    ![Amministratore](./media/active-directory-saas-freshservice-tutorial/ic790814.png "Amministratore")

3. Nella sezione **User Management** (Gestione utenti) fare clic su **Requesters** (Richiedenti).
   
    ![Requesters](./media/active-directory-saas-freshservice-tutorial/ic790818.png "Requesters")

4. Fare clic su **Nuovo Requester**.
   
    ![New Requesters](./media/active-directory-saas-freshservice-tutorial/ic790819.png "New Requesters")

5. Nella sezione **New Requester** seguire questa procedura:
   
    ![New Requester](./media/active-directory-saas-freshservice-tutorial/ic790820.png "New Requester")   

    a. Nelle caselle **First Name** e **Email** immettere il nome e l'indirizzo di posta elettronica di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.

    b. Fare clic su **Salva**.
   
    >[!NOTE]
    >Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
    >  

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da FreshService per eseguire il provisioning degli account utente di AAD.
>  

![Assegna utente][200] 

**Per assegnare Britta Simon a Freshservice, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Freshservice**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/tutorial_freshservice_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Freshservice nel pannello di accesso si accede automaticamente all'applicazione Freshservice.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshservice-tutorial/tutorial_general_203.png


