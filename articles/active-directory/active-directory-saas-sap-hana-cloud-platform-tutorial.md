---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e piattaforma SAP Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 6b4b0e3ab126e70e4b27b971b15127aae0486bed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform

Questa esercitazione descrive come integrare SAP Cloud Platform con Azure Active Directory (Azure AD).

L'integrazione di SAP Cloud Platform con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SAP Cloud Platform.
- È possibile abilitare gli utenti per l'accesso automatico a SAP Cloud Platform (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Cloud Platform, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di SAP Cloud Platform abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SAP Cloud Platform saranno in grado di eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>È necessario distribuire l'applicazione o la sottoscrizione a un'applicazione per l'account di SAP Cloud Platform per testare l'accesso Single Sign-On. In questa esercitazione, viene distribuita un'applicazione nell'account.
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP Cloud Platform dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Aggiunta di SAP Cloud Platform dalla raccolta
Per configurare l'integrazione di SAP Cloud Platform in Azure AD, è necessario aggiungere SAP Cloud Platform dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP Cloud Platform dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SAP Cloud Platform**, selezionare **SAP Cloud Platform** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAP Cloud Platform nell'elenco dei risultati](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Cloud Platform usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di SAP Cloud Platform corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud Platform.

Per stabilire la relazione di collegamento, in SAP Cloud Platform assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Cloud Platform, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di SAP Cloud Platform](#create-a-sap-cloud-platform-test-user)**: per avere una controparte di Britta Simon in SAP Cloud Platform collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP Cloud Platform.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP Cloud Platform, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SAP Cloud Platform** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. Nella sezione **URL e dominio SAP Cloud Platform** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione **SAP Cloud Platform**. Questo è l'URL specifico dell'account di una risorsa protetta dell'applicazione SAP Cloud Platform. L'URL è basato sul formato seguente: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Questo è l'URL dell'applicazione SAP Cloud Platform che richiede l'autenticazione dell'utente.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Nella casella di testo **Identifier** si fornirà il tipo di SAP Cloud Platform con un URL nel seguente formato: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Contattare il [team di supporto dei client di SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) per ottenere l'URL di accesso e l'identificatore. URL di risposta che è possibile ottenere dalla sezione di gestione trust, illustrata più avanti nell'esercitazione.
    > 
     
4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. In una diversa finestra del Web browser accedere al pannello di controllo di SAP Cloud Platform all'indirizzo `https://account.<landscape host>.ondemand.com/cockpit` (ad esempio https://account.hanatrial.ondemand.com/cockpit).

7. Scegliere la scheda **Trust** .
   
    ![Trust](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "Trust")

8. Nella sezione Trust Management, in **Local Service Provider**, eseguire la procedura seguente:

    ![Gestione dell'attendibilità](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "Gestione dell'attendibilità")
   
    a. Fare clic su **Modifica**.

    b. In **Configuration Type** selezionare **Custom**.

    c. In **Local Provider Name**lasciare il valore predefinito. Copiare questo valore e incollarlo nel campo **Identifier** campo nella configurazione di Azure Active Directory per SAP Cloud Platform.

    d. Per generare una **chiave per la firma** e una coppia di chiavi del **certificato di firma**, fare clic su **Generate Key Pair**.

    e. In **Propagazione entità** selezionare **Disabilitato**.

    f. In **Force Authentication** selezionare **Disabled**.

    g. Fare clic su **Salva**.

9. Dopo il salvataggio delle impostazioni **Local Service Provider**, eseguire le operazioni seguenti per ottenere l'URL di risposta:
   
    ![Ottenere i metadati](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "Ottenere i metadati")

    a. Scaricare il file di metadati di SAP Cloud Platform facendo clic su **Get Metadata**.

    b. Aprire il file di metadati XML SAP Cloud piattaforma scaricato e quindi individuare il tag **ns3:AssertionConsumerService** .
 
    c. Copiare il valore dell'attributo **Percorso** e incollarlo nel campo **URL di risposta** nella configurazione di Azure Active Directory per SAP Cloud Platform.

10. Fare clic sulla scheda **Trusted Identity Provider**, quindi fare clic su **Add Trusted Identity Provider**.
   
    ![Gestione dell'attendibilità](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "Gestione dell'attendibilità")
   
    >[!NOTE]
    >Per gestire l'elenco dei provider di identità attendibili, è necessario aver scelto il tipo di configurazione Personalizza nella sezione Provider di servizi locale. Per il tipo di configurazione predefinito è presente una relazione di trust implicita e non modificabile per il servizio SAP ID. Per Nessuno, non sono disponibili impostazioni di trust.
    > 
    > 

11. Fare clic sulla scheda **General**, quindi fare clic su **Browse** per caricare il file di metadati scaricati.
    
    ![Gestione dell'attendibilità](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "Gestione dell'attendibilità")
    
    >[!NOTE]
    >Dopo aver caricato il file di metadati, i valori per **URL Single Sign-On**, **URL disconnessione singola** e **Certificato di firma** vengono popolati automaticamente.
    > 
     
12. Fare clic sulla scheda **Attributes** .

13. Nella scheda **Attributes** eseguire la procedura seguente:
    
    ![Attributi](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "Attributi") 

    a. Fare clic su **Add Assertion-Based Attribute** e quindi aggiungere gli attributi basati su asserzione seguenti:
       
    | Attributo di asserzione | Attributo di entità |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >La configurazione degli attributi dipende da come le applicazioni per SCP vengono sviluppate, vale a dire quali attributi sono previsti nella risposta SAML e con quale nome (attributo di entità) accedono a questo attributo nel codice.
     > 
    
    b. L'**attributo predefinito** presente nella schermata è solo a scopo illustrativo. Non è necessario per il funzionamento dello scenario.  
 
    c. I nomi e i valori per l'**attributo di entità** illustrati nella schermata dipendono dal modo in cui viene sviluppata l'applicazione. È possibile che l'applicazione richieda mapping diversi.

###<a name="assertion-based-groups"></a>Gruppi basati su asserzione

Come passaggio facoltativo, è possibile configurare gruppi basati su asserzione per il provider di identità Azure Active Directory.

L'uso dei gruppi in SAP Cloud Platform consente di assegnare dinamicamente uno o più utenti a uno o più ruoli nelle applicazioni SAP Cloud Platform, determinate dai valori degli attributi nell'asserzione SAML 2.0. 

Ad esempio, se l'asserzione contiene l'attributo "*contract=temporary*", si potrebbe volere che tutti gli utenti interessati siano aggiunti al gruppo"*TEMPORARY*". Il gruppo "*TEMPORARY*" può contenere uno o più ruoli da uno o più applicazioni distribuite nell'account di SAP Cloud Platform.
 
Usare i gruppi basati su asserzione quando si vogliono assegnare simultaneamente diversi utenti a uno o più ruoli delle applicazioni nell'account di SAP Cloud Platform. Per assegnare un singolo utente o un numero ridotto di utenti a ruoli specifici, è consigliabile di eseguirne l'assegnazione diretta nella scheda **Authorizations** (Autorizzazioni) del pannello di controllo di SAP Cloud Platform.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Creare un utente di test di SAP Cloud Platform

Per consentire agli utenti di Azure AD di accedere a SAP Cloud Platform è necessario assegnare loro i ruoli in SAP Cloud Platform.

**Per assegnare un ruolo a un utente, seguire questa procedura:**

1. Accedere al pannello di controllo di **SAP Cloud Platform** .

2. Eseguire questi passaggi:
   
    ![Autorizzazioni](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "Autorizzazioni")
   
    a. Fare clic su **Authorization**.

    b. Fare clic sulla scheda **Utenti** .

    c. Nella casella di testo **User** digitare l'indirizzo di posta elettronica dell'utente.

    d. Fare clic su **Assign** per assegnare l'utente a un ruolo.

    e. Fare clic su **Salva**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a SAP Cloud Platform.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SAP Cloud Platform, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **SAP Cloud Platform**.

    ![Il collegamento SAP Cloud Platform nell'elenco delle applicazioni](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP Cloud Platform nel pannello di accesso, si accederà automaticamente all'applicazione SAP Cloud Platform.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

