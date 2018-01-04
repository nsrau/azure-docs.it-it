---
title: 'Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: b09dd977cbf5c4273667167217e86bb79ac2a9d8
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS Online

Questa esercitazione descrive come integrare ArcGIS Online con Azure Active Directory (Azure AD).

L'integrazione di ArcGIS Online con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso a ArcGIS Online.
- È possibile consentire agli utenti di automaticamente ottenere firmato a ArcGIS Online (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ArcGIS Online, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per ArcGIS Online single sign-on

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ArcGIS Online dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-arcgis-online-from-the-gallery"></a>Aggiunta di ArcGIS Online dalla raccolta
Per configurare l'integrazione di ArcGIS Online in Azure AD, è necessario aggiungere ArcGIS Online dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ArcGIS Online dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **ArcGIS Online**selezionare **ArcGIS Online** dal pannello risultati quindi fare clic su **Aggiungi** pulsante per aggiungere l'applicazione.

    ![ArcGIS Online nell'elenco dei risultati](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con ArcGIS Online in base a un utente di test denominato "Laura Giussani".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di ArcGIS Online corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ArcGIS Online.

In ArcGIS Online, assegnare il valore della **nome utente** in Azure AD come valore della **Username** per stabilire la relazione di collegamento.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ArcGIS Online, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test Online ArcGIS](#create-a-arcgis-online-test-user)**  - disporre di un equivalente di Britta Simon in ArcGIS Online in cui è collegata la rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ArcGIS Online.

**Per configurare l'accesso Single Sign-On di Azure AD con ArcGIS Online, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ArcGIS Online** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. Nel **ArcGIS Online dominio e gli URL** sezione, eseguire la procedura seguente:

    ![URL e ArcGIS Online dominio single sign-on, informazioni](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.maps.arcgis.com`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Contatto [team di supporto di Client Online ArcGIS](http://support.esri.com/en/) per ottenere questi valori. 
 


4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

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
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-arcgis-online-test-user"></a>Creare un utente test ArcGIS Online

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ArcGIS Online.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad ArcGIS Online, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **ArcGIS Online**.

    ![Il collegamento ArcGIS Online nell'elenco delle applicazioni](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

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

