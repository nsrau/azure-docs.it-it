---
title: 'Esercitazione: Integrazione di Azure Active Directory con SpringCM | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: edfd06a06c730597fee4569ca1ce29092b45244a
ms.contentlocale: it-it
ms.lasthandoff: 08/02/2017

---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Esercitazione: Integrazione di Azure Active Directory con SpringCM

Questa esercitazione descrive come integrare SpringCM con Azure Active Directory (Azure AD).

L'integrazione di SpringCM con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SpringCM
- È possibile abilitare gli utenti per l'accesso automatico a SpringCM (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SpringCM, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Spring CM abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SpringCM dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-springcm-from-the-gallery"></a>Aggiunta di SpringCM dalla raccolta
Per configurare l'integrazione di SpringCM in Azure AD, è necessario aggiungere SpringCM dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SpringCM dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **SpringCM**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_search.png)

5. Nel pannello dei risultati selezionare **SpringCM** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SpringCM con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SpringCM che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SpringCM.

Per stabilire la relazione di collegamento, in SpringCM assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SpringCM, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di SpringCM](#creating-a-springcm-test-user)**: per avere una controparte di Britta Simon in SpringCM collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SpringCM.

**Per configurare Single Sign-On di Azure AD con SpringCM, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SpringCM** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_samlbase.png)

3. Nella sezione **URL e dominio SpringCM** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di SpringCM](https://knowledge.springcm.com/support). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (base)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di SpringCM** fare clic su **Configura SpringCM** per aprire la finestra **Configura accesso**. Copiare i valori **SAML Entity ID (ID entità SAML) e SAML Single Sign-On Service URL (URL servizio Single Sign-On SAML)** dalla sezione **Quick Reference** (Riferimento rapido).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_configure.png)   

7. In un'altra finestra del browser Web accedere al sito aziendale di **SpringCM** come amministratore.

8. Nel menu nella parte superiore fare clic su **Vai a**, scegliere **Preferenze**, quindi nella scheda **Preferenze account** fare clic su **SAML SSO**.
   
    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/ic797051.png "SAML SSO")

9. Nella sezione Configurazione provider di identità, eseguire la procedura seguente:
   
    ![Configurazione del provider di identità](./media/active-directory-saas-spring-cm-tutorial/ic797052.png "Configurazione del provider di identità")
    
    a. Per caricare il certificato di Azure Active Directory scaricato, fare clic su **Seleziona certificato autorità di certificazione** o su **Cambia certificato autorità di certificazione**.
    
    b. Incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure nella casella di testo **Autorità di certificazione**.
    
    c. Incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **Service Provider (SP) Initiated Endpoint** (Endpoint avviato da provider di servizi).
            
    d. Selezionare **Abilitato SAML** in modo che sia **Abilitato**.

    e. Fare clic su **Salva**.
 
> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-spring-cm-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-springcm-test-user"></a>Creazione di un utente di test di SpringCM

Per consentire agli utenti di Azure Active Directory di accedere a SpringCM, è necessario eseguirne il provisioning in SpringCM. Nel caso di SpringCM, il provisioning è un'attività manuale.

>[!NOTE]
>Per altre informazioni, vedere [Creare e modificare un utente SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Per eseguire il provisioning di un account utente in SpringCM, eseguire la procedura seguente:**

1. Accedere al sito aziendale di **SpringCM** come amministratore.

2. Fare clic su **GOTO** e scegliere **RUBRICA**.
   
    ![Crea utente](./media/active-directory-saas-spring-cm-tutorial/ic797054.png "Crea utente")

3. Fare clic su **Create User**.

4. Selezionare un **Ruolo utente**.

5. Selezionare **Invia messaggio di attivazione**.

6. Digitare il nome, il cognome, il titolo e l'indirizzo di posta elettronica di un account utente di Azure Active Directory valido di cui si desidera effettuare il provisioning nelle caselle di testo correlate.

7. Aggiungere l'utente a un **Gruppo di protezione**.

8. Fare clic su **Save**.

  >[!NOTE]
  >È possibile usare qualsiasi altro strumento di creazione di account utente SpringCM o API fornita da SpringCM per eseguire il provisioning degli account utente di Azure AD.  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a SpringCM.

![Assegna utente][200] 

**Per assegnare Britta Simon a SpringCM seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SpringCM**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/tutorial_springcm_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
 
Quando si fa clic sul riquadro SpringCM nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione SpringCM.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spring-cm-tutorial/tutorial_general_203.png


