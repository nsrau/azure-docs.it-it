---
title: 'Esercitazione: Integrazione di Azure Active Directory con BetterWorks | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BetterWorks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 85c815218bca322bcbfd25b5a10b9eaa17ea4adf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Esercitazione: Integrazione di Azure Active Directory con BetterWorks

Questa esercitazione descrive come integrare BetterWorks con Azure Active Directory (Azure AD).

L'integrazione di BetterWorks con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a BetterWorks
- È possibile abilitare gli utenti per l'accesso automatico a BetterWorks (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con BetterWorks, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di BetterWorks abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di BetterWorks dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-betterworks-from-the-gallery"></a>Aggiunta di BetterWorks dalla raccolta
Per configurare l'integrazione di BetterWorks in Azure AD, è necessario aggiungere BetterWorks dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere BetterWorks dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **BetterWorks**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_search.png)

5. Nel pannello dei risultati selezionare **BetterWorks** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con BetterWorks usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di BetterWorks corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BetterWorks.

Per stabilire la relazione di collegamento, in BetterWorks assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con BetterWorks, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di BetterWorks](#creating-a-betterworks-test-user)**: per avere una controparte di Britta Simon in BetterWorks collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione BetterWorks.

**Per configurare l'accesso Single Sign-On di Azure AD con BetterWorks, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **BetterWorks** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_samlbase.png)

3. Per configurare l'applicazione in **modalità avviata da IDP**, nella sezione **URL e dominio BetterWorks** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://app.betterworks.com/saml2/metadata/`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://app.betterworks.com/saml2/acs/`

4. Per configurare l'applicazione in **modalità avviata da SP**, nella sezione **URL e dominio BetterWorks** seguire questa procedura:
    
    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_url1.png)

    a. Fare clic su **Mostra impostazioni URL avanzate**.

    b. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://app.betterworks.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta, l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di BetterWorks](mailto:support@betterworks.com).
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_certificate.png)  

5. L'applicazione BetterWorks si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **Attribute** (Attributo) dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione. 

    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_attribute.png)

6. Nella finestra di dialogo **Attributi token SAML** seguire questa procedura per ciascuna riga della tabella:
 
   | Nome attributo | Valore attributo |
   | -------------- |  ------------ |
   | saml_token     | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

   a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_officespace_05.png)

   b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga. 

   c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
   d. Fare clic su **OK**.

7. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_general_400.png)

8. Per configurare l'accesso Single Sign-On sul lato **BetterWorks**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di BetterWorks](mailto:support@betterworks.com).


> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-betterworks-test-user"></a>Creazione di un utente di test per BetterWorks

In questa sezione viene creato un utente di nome Britta Simon in BetterWorks. Collaborare con il [team di supporto di BetterWorks](mailto:support@betterworks.com) per aggiungere gli utenti alla piattaforma BetterWorks.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BetterWorks.

![Assegna utente][200] 

**Per assegnare Britta Simon a BetterWorks, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **BetterWorks**.

    ![Configure Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro BetterWorks nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione BetterWorks.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png

