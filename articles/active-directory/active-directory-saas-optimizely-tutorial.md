---
title: 'Esercitazione: Integrazione di Azure Active Directory con Optimizely | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 4d6f6da6bace09fbd6ab105530a1162653675c99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Esercitazione: Integrazione di Azure Active Directory con Optimizely

Questa esercitazione descrive come integrare Optimizely con Azure Active Directory (Azure AD).

L'integrazione di Optimizely con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Optimizely
- È possibile abilitare gli utenti per l'accesso automatico a Optimizely (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Optimizely, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Optimizely abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Optimizely dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-optimizely-from-the-gallery"></a>Aggiunta di Optimizely dalla raccolta
Per configurare l'integrazione di Optimizely in Azure AD, è necessario aggiungere Optimizely dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Optimizely dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Optimizely**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_search.png)

5. Nel pannello dei risultati selezionare **Optimizely** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Optimizely mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Optimizely che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Optimizely.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in Optimizely.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Optimizely, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Optimizely](#creating-an-optimizely-test-user)**: per avere una controparte di Britta Simon in Optimizely collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Optimizely.

**Per configurare l'accesso Single Sign-On di Azure AD con Optimizely, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Optimizely** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. Nella sezione **URL e dominio Optimizely** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://app.optimizely.net/<instance name>`.

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente: `urn:auth0:optimizely:contoso`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con i valori reali di URL di accesso e Identificatore. La procedura è descritta più avanti nell'esercitazione. 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Optimizely** fare clic su **Configura Optimizely** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_configure.png) 

7. Per configurare l'accesso Single Sign-On sul lato **Optimizely** contattare l'account manager di Optimizely e trasmettere il **Certificato (Base64)** scaricato e il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML). 

8. In risposta al messaggio di posta elettronica, Optimizely fornisce l'URL di accesso (SSO avviato da SP) e i valori di Identificatore (ID entità del provider di servizi).

    a. Copiare l'**URL SSO avviato da SP** specificato da Optimizely e incollarlo nella casella di testo **URL di accesso** nella sezione **URL e dominio Optimizely** del portale di Azure. 

    b. Copiare l'**ID entità del provider di servizi** specificato da Optimizely e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio Optimizely** del portale di Azure. 

9. In una finestra del browser diversa accedere all'applicazione Optimizely.

10. Fare clic sul nome del proprio account in alto a destra e quindi su **Account Settings**(Impostazioni account).
   
    ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

11. Nella scheda Account, selezionare la casella **Enable SSO** (Abilita SSO) nella sezione **Overview** (Panoramica) di Single Sign On.
   
    ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)
    
12. Fare clic su **Save**

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di Britta Simon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-optimizely-test-user"></a>Creazione di un utente test di Optimizely

In questa sezione viene creato un utente di nome Britta Simon in Optimizely.

1. Nella home page selezionare la scheda **Collaborators** (Collaboratori).

2. Per aggiungere un nuovo collaboratore al progetto fare clic su **New Collaborator** (Nuovo collaboratore).
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. Immettere l'indirizzo di posta elettronica e assegnare un ruolo ai nuovi collaboratori. Fare clic su **Invita**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. L'utente riceve un invito tramite posta elettronica e deve eseguire l'accesso a Optimizely usando l'indirizzo di posta elettronica.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Optimizely.

![Assegna utente][200] 

**Per assegnare Britta Simon a Optimizely, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Optimizely**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Optimizely nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Optimizely. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png

