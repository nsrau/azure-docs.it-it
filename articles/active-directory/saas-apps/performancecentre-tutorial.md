---
title: 'Esercitazione: Integrazione di Azure Active Directory con PerformanceCentre | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: d363f47301517e7d4f8d8f8fdf45ffd8c146279e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165167"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Esercitazione: Integrazione di Azure Active Directory con PerformanceCentre

Questa esercitazione descrive come integrare PerformanceCentre con Azure Active Directory (Azure AD).

L'integrazione di PerformanceCentre con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a PerformanceCentre
- È possibile abilitare gli utenti per l'accesso automatico a PerformanceCentre (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con PerformanceCentre, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di PerformanceCentre abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di PerformanceCentre dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-performancecentre-from-the-gallery"></a>Aggiunta di PerformanceCentre dalla raccolta
Per configurare l'integrazione di PerformanceCentre in Azure AD, è necessario aggiungere PerformanceCentre dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere PerformanceCentre dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **PerformanceCentre**.

    ![Creazione di un utente test di Azure AD](./media/performancecentre-tutorial/tutorial_performancecentre_search.png)

1. Nel pannello dei risultati selezionare **PerformanceCentre** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con PerformanceCentre in base a un utente di prova di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di PerformanceCentre che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PerformanceCentre.

Per stabilire la relazione di collegamento, in PerformanceCentre assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con PerformanceCentre, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test per PerformanceCentre](#creating-a-performancecentre-test-user)** : per avere una controparte di Britta Simon in PerformanceCentre collegata alla relativa rappresentazione in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione PerformanceCentre.

**Per configurare l'accesso Single Sign-On di Azure AD con PerformanceCentre, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **PerformanceCentre** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

1. Nella sezione **URL e dominio PerformanceCentre** seguire questa procedura:

    ![Configure Single Sign-On](./media/performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `http://companyname.performancecentre.com/saml/SSO`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di PerformanceCentre](https://www.performancecentre.com/contact-us/). 

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/performancecentre-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di PerformanceCentre** fare clic su **Configura PerformanceCentre** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido.**

    ![Configure Single Sign-On](./media/performancecentre-tutorial/tutorial_performancecentre_configure.png) 

1. Accedere al sito aziendale di **PerformanceCentre** come amministratore.

1. Nella barra di spostamento sul lato sinistro fare clic su **Configure**(Configura).
   
    ![Single Sign-On di Microsoft Azure AD][10]

1. Nella scheda sul lato sinistro fare clic su **Miscellaneous** (Varie) e quindi su **Single Sign On**.
   
    ![Single Sign-On di Microsoft Azure AD][11]

1. Per **Protocol** (Protocollo) selezionare **SAML**.
   
    ![Single Sign-On di Microsoft Azure AD][12]

1. Aprire il file di metadati nel Blocco note, copiarne il contenuto negli Appunti, incollarlo nella casella di testo **Identity Provider Metadata** (Metadati provider di identità) e quindi fare clic su **Save** (Salva).
   
    ![Single Sign-On di Microsoft Azure AD][13]

1. Verificare che i valori di **Entity Base URL** (URL base entità) e **Entity ID URL** (URL ID entità) siano corretti.
    
     ![Single Sign-On di Microsoft Azure AD][14]

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/performancecentre-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/performancecentre-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/performancecentre-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/performancecentre-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-performancecentre-test-user"></a>Creazione di un utente test per PerformanceCentre

Questa sezione descrive come creare un utente chiamato Britta Simon in PerformanceCentre.

**Per creare un utente denominato Britta Simon in PerformanceCentre, seguire questa procedura:**

1. Accedere al sito aziendale di PerformanceCentre come amministratore.

1. Nel menu a sinistra fare clic su **Interrelate** (Collega) e quindi su **Create Participant** (Crea partecipante).
   
    ![Crea utente][400]

1. Nella finestra di dialogo **Interrelate - Create Participant** (Collega - Crea partecipante) seguire questa procedura:
   
    ![Crea utente][401]
    
    a. Immettere gli attributi richiesti per Britta Simon nelle caselle di testo corrispondenti.
    
    >[!IMPORTANT]
    >L'attributo User Name di Britta in PerformanceCentre deve corrispondere al nome utente in Azure AD.
    
    b. Selezionare **Client Administrator** in **Choose Role**.
    
    c. Fare clic su **Save**. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PerformanceCentre.

![Assegna utente][200] 

**Per assegnare Britta Simon a PerformanceCentre, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **PerformanceCentre**.

    ![Configure Single Sign-On](./media/performancecentre-tutorial/tutorial_performancecentre_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro PerformanceCentre nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione PerformanceCentre.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png

