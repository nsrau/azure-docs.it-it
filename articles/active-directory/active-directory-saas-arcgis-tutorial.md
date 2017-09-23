---
title: 'Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: df72270ca6443b456c079b22425f1660aa522389
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online

Questa esercitazione descrive come integrare ArcGIS Online con Azure Active Directory (Azure AD).

L'integrazione di ArcGIS Online con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad ArcGIS Online
- È possibile abilitare gli utenti per l'accesso automatico ad ArcGIS Online (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with ArcGIS Online, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in ArcGIS Online.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ArcGIS Online, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di ArcGIS Online abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di ArcGIS Online dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-arcgis-online-from-the-gallery"></a>Aggiunta di ArcGIS Online dalla raccolta
Per configurare l'integrazione di ArcGIS Online in Azure AD, è necessario aggiungere ArcGIS Online dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ArcGIS Online dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **ArcGIS Online**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_search.png)

5. Nel pannello dei risultati selezionare **ArcGIS Online** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ArcGIS Online usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di ArcGIS Online corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ArcGIS Online.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente) in ArcGIS Online.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ArcGIS Online, è necessario completare le procedure di base seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di ArcGIS Online](#creating-an-arcgis-online-test-user)**: per avere una controparte di Britta Simon in ArcGIS Online collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ArcGIS Online.

**Per configurare l'accesso Single Sign-On di Azure AD con ArcGIS Online, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ArcGIS Online** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. Nella sezione **URL e dominio ArcGIS Online** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente: `https://<company>.maps.arcgis.com`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di ArcGIS Online](http://support.esri.com/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di ArcGIS come amministratore.

7. Fare clic su **EDIT SETTINGS** (Modifica impostazioni).

    ![Modificare le impostazioni](./media/active-directory-saas-arcgis-tutorial/ic784742.png "Modificare le impostazioni")

8. Fare clic su **Security**.

    ![Sicurezza](./media/active-directory-saas-arcgis-tutorial/ic784743.png "Sicurezza")

9. In **Enterprise Logins** (Accessi aziendali) fare clic su **SET IDENTITY PROVIDER** (Imposta provider di identità).

    ![Accessi aziendali](./media/active-directory-saas-arcgis-tutorial/ic784744.png "Accessi aziendali")

10. Nella pagina di configurazione **Set Identity Provider** seguire questa procedura:
   
    ![Impostare il provider di identità](./media/active-directory-saas-arcgis-tutorial/ic784745.png "Impostare il provider di identità")
   
    a. Nella casella di testo **Name** (Nome) digitare il nome della propria organizzazione.

    b. In **I metadati per il provider di identità dell'organizzazione verranno forniti mediante**, selezionare **Un File**.

    c. Per caricare il file di metadati scaricato, fare clic su **Seleziona file**.

    d. Fare clic su **SET IDENTITY PROVIDER** (Imposta provider di identità).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di Britta Simon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-arcgis-online-test-user"></a>Creazione di un utente di test di ArcGIS Online

Per consentire agli utenti di Azure AD di accedere ad ArcGIS Online, è necessario effettuarne il provisioning in ArcGIS Online.  
Nel caso di ArcGIS Online, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **ArcGIS** .

2. Fare clic su **INVITE MEMBERS** (Invita membri).
   
    ![Invitare i membri](./media/active-directory-saas-arcgis-tutorial/ic784747.png "Invitare i membri")

3. Selezionare **Add members automatically without sending an email** (Aggiungi membri automaticamente senza inviare un'e-mail) e quindi fare clic su **NEXT** (Avanti).
   
    ![Aggiungere i membri automaticamente](./media/active-directory-saas-arcgis-tutorial/ic784748.png "Aggiungere i membri automaticamente")

4. Nella finestra di dialogo **Membri** seguire questa procedura:
   
     ![Aggiungere ed esaminare](./media/active-directory-saas-arcgis-tutorial/ic784749.png "Aggiungere ed esaminare")
    
     a. Nelle caselle di testo **Email** (Posta elettronica), **First name** (Nome) e **Last name** (Cognome) digitare rispettivamente l'indirizzo di posta elettronica, il nome e il cognome relativi a un account Azure AD valido di cui si vuole effettuare il provisioning.
  
     b. Fare clic su **ADD AND REVIEW** (Aggiungi e verifica).
5. Verificare i dati immessi e quindi fare clic su **ADD MEMBERS** (Aggiungi membri).
   
    ![Aggiungere un membro](./media/active-directory-saas-arcgis-tutorial/ic784750.png "Aggiungere un membro")
        
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ArcGIS Online.

![Assegna utente][200] 

**Per assegnare Britta Simon ad ArcGIS Online, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **ArcGIS Online**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ArcGIS Online nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ArcGIS Online.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png


