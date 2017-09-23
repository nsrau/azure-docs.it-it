---
title: 'Esercitazione: Integrazione di Azure Active Directory con Picturepark | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 1c009aa1fdd3140a4466cf762b6c9687e74ce4c7
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Esercitazione: Integrazione di Azure Active Directory con Picturepark

Questa esercitazione descrive come integrare Picturepark con Azure Active Directory (Azure AD).

L'integrazione di Picturepark con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Picturepark
- È possibile abilitare gli utenti per l'accesso automatico a Picturepark (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Picturepark, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Picturepark abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Picturepark dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-picturepark-from-the-gallery"></a>Aggiunta di Picturepark dalla raccolta
Per configurare l'integrazione di Picturepark in Azure AD, è necessario aggiungere Picturepark dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Picturepark dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Picturepark**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_search.png)

5. Nel pannello dei risultati selezionare **Picturepark** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Picturepark in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di Picturepark corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Picturepark.

Per stabilire la relazione di collegamento, in Picturepark assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Picturepark, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Picturepark](#creating-a-picturepark-test-user)**: per avere una controparte di Britta Simon in Picturepark collegata alla relativa rappresentazione in Azure AD dell'utente.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Picturepark.

**Per configurare l'accesso Single Sign-On di Azure AD con Picturepark, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Picturepark** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. Nella sezione **URL e dominio Picturepark** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.picturepark.com`.

    b. Nella casella di testo **Identificatore** digitare un URL usando il modello seguente: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Picturepark](https://picturepark.com/about/contact/). 
 
4. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** del certificato.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Picturepark** fare clic su **Configura Picturepark** per visualizzare la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Picturepark come amministratore.

8. Nella barra degli strumenti in alto fare clic su **Strumenti di amministrazione**, quindi fare clic su **Console di gestione**.
   
    ![Console di gestione](./media/active-directory-saas-picturepark-tutorial/ic795062.png "Console di gestione")

9. Fare clic su **Autenticazione**, quindi su **Provider di identità**.
   
    ![Autenticazione](./media/active-directory-saas-picturepark-tutorial/ic795063.png "Autenticazione")

10. Nella sezione **Configurazione provider di identità** , eseguire questa procedura:
   
    ![Configurazione del provider di identità](./media/active-directory-saas-picturepark-tutorial/ic795064.png "Configurazione del provider di identità")
   
    a. Fare clic su **Aggiungi**.
  
    b. Digitare un nome per la configurazione.
   
    c. Selezionare **Imposta come predefinito**.
   
    d. Nella casella di testo **URI autorità emittente** incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
   
    e. Nella casella di testo **Trusted Issuer Thumb Print** (Identificazione personale autorità emittente attendibile) incollare il valore di **Identificazione personale** copiato dalla sezione **Certificato di firma SAML**. 

11. Fare clic su **JoinDefaultUsersGroup**.

12. Per impostare l'attributo **EmailAddress** nella casella di testo **Claim** (Attestazione), digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` e fare clic su **Salva**.

      ![Configurazione](./media/active-directory-saas-picturepark-tutorial/ic795065.png "Configurazione")

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-picturepark-test-user"></a>Creazione di un utente test di Picturepark

Per consentire agli utenti di Azure AD di accedere a Picturepark, è necessario eseguirne il provisioning in Picturepark. Nel caso di Picturepark, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Picturepark** .

2. Nella barra degli strumenti in alto fare clic su **Strumenti di amministrazione**, quindi su **Utenti**.
   
    ![Utenti](./media/active-directory-saas-picturepark-tutorial/ic795067.png "Utenti")

3. Nella scheda **Panoramica utenti** fare clic su **Nuovo**.
   
    ![Gestione degli utenti](./media/active-directory-saas-picturepark-tutorial/ic795068.png "Gestione degli utenti")

4. Nella finestra di dialogo **Crea utente** eseguire i passaggi seguenti per un utente di Azure Active Directory valido di cui si vuole eseguire il provisioning:
   
    ![Crea utente](./media/active-directory-saas-picturepark-tutorial/ic795069.png "Crea utente")
   
    a. Nella casella di testo **Indirizzo di posta elettronica** digitare l'**indirizzo di posta elettronica** dell'utente **BrittaSimon@contoso.com**.  
   
    b. Nelle caselle di testo **Password** e **Conferma password** digitare la **password** di BrittaSimon. 
   
    c. Nella casella di testo **Nome** digitare il **nome** dell'utente **Britta**. 
   
    d. Nella casella di testo **Cognome** digitare il **cognome** dell'utente **Simon**.
   
    e. Nella casella di testo **Società** digitare il **nome della società** dell'utente. 
   
    f. Nella casella di testo **Paese** selezionare il **paese** dell'utente.
  
    g. Nella casella **CAP** digitare il **CAP** della città.
   
    h. Nella casella di testo **Città** digitare il **nome della città** dell'utente.

    i. Selezionare una **Language**.
   
    j. Fare clic su **Crea**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerta da Picturepark per eseguire il provisioning degli account utente di Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Picturepark.

![Assegna utente][200] 

**Per assegnare Britta Simon a Picturepark, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Picturepark**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Picturepark nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Picturepark. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_203.png


