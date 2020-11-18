---
title: 'Esercitazione: Integrazione di Azure Active Directory con Printix | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Printix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: dfde9bbbeb7f6b349ecbdc4c2da605d39a0708da
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357879"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Esercitazione: Integrazione di Azure Active Directory con Printix

Questa esercitazione descrive come integrare Printix con Azure Active Directory (Azure AD).

L'integrazione di Printix con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Printix
- È possibile abilitare gli utenti per l'accesso automatico a Printix (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Printix sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Printix abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Printix dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-printix-from-the-gallery"></a>Aggiunta di Printix dalla raccolta
Per configurare l'integrazione di Printix in Azure AD è necessario aggiungere Printix dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Printix dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Screenshot che mostra l'opzione Applicazioni aziendali selezionata nella sezione Gestisci del portale di Azure e l'opzione Tutte le applicazioni selezionata.][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Screenshot che mostra l'opzione Nuova applicazione selezionata.][3]

1. Nella casella di ricerca digitare **Printix**.

    ![Screenshot che mostra la ricerca di Printix nella finestra di dialogo Aggiungi dalla raccolta.](./media/printix-tutorial/tutorial_printix_search.png)

1. Nel pannello dei risultati selezionare **Printix** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Screenshot che mostra l'opzione Printix selezionata.](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Printix con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Printix che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Printix.

Per stabilire la relazione di collegamento, in Printix assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Printix è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di Printix](#creating-a-printix-test-user)**: per avere una controparte di Britta Simon in Printix collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Printix.

**Per configurare l'accesso Single Sign-On di Azure AD con Printix, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Printix** del portale di Azure fare clic su **Single Sign-On**.

    ![Screenshot che mostra l'opzione Single Sign-On selezionata nella sezione Gestisci del portale di Azure.][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Screenshot che mostra la modalità di accesso basata su SAML selezionata.](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. Nella sezione **URL e dominio Printix** seguire questa procedura:

    ![Screenshot che mostra la sezione Printix Domain and URLs in cui è possibile specificare un URL per l'accesso.](./media/printix-tutorial/tutorial_printix_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.printix.net`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Printix](mailto:support@printix.net). 
 
1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Screenshot che mostra il riquadro Certificato di firma SAML in cui è possibile scaricare un certificato.](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Screenshot che mostra il pulsante Save.](./media/printix-tutorial/tutorial_general_400.png)

1. Accedere al tenant di Printix come amministratore.

1. Nel menu in alto, fare clic sull'icona nell'angolo superiore destro e selezionare "**Authentication**" (Autenticazione).
   
    ![Screenshot che mostra l'opzione Authentication selezionata nel menu.](./media/printix-tutorial/tutorial_printix_06.png)

1. Nella scheda **Setup** (Configurazione) selezionare **Enable Azure/Office 365 authentication** (Abilita autenticazione Azure/Office 365)
   
    ![Screenshot che mostra la pagina Printix.net in cui è possibile selezionare Enable/Office 365 authentication.](./media/printix-tutorial/tutorial_printix_07.png)

1. Nella scheda **Azure**, immettere l'URL dei metadati della federazione nella casella di testo "**Federation metadata document**" (Documento metadati federazione). 

    Allegare il file dei metadati con estensione xml scaricato da Azure AD e inviarlo al [team di supporto Printix](mailto:support@printix.net). Il team carica il file con estensione xml e metterà a disposizione un URL di metadati di federazione.
   
    ![Screenshot che mostra la pagina Printix.net in cui è possibile specificare un documento di metadati di federazione.](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Fare clic sul pulsante "**Test**" e quindi su "**OK**", se il test ha avuto esito positivo.
   
     Dopo aver fatto clic sul pulsante **test** verrà visualizzata la pagina di Azure Active Directory. In questo caso, "esito positivo" significa che dopo aver immesso le credenziali dell'account di test di Azure verrà visualizzato un messaggio "Settings tested OK" (Il test delle impostazioni è riuscito). Fare clic sul pulsante **OK**.
   
    ![Screenshot che mostra i risultati del test.](./media/printix-tutorial/tutorial_printix_09.png)

1. Fare clic sul pulsante **Save** (Salva) nella pagina "**Authentication**" (Autenticazione).


> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure AD
Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Screenshot che mostra un nome e un nome utente da creare.](./media/printix-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Screenshot che mostra l'icona Azure AD nel portale di Azure.](./media/printix-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Screenshot che mostra l'opzione Utenti e gruppi selezionata nel menu Gestisci, con l'opzione Tutti gli utenti selezionata.](./media/printix-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Screenshot che mostra la finestra di dialogo Utente in cui è possibile immettere i valori indicati.](./media/printix-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-printix-test-user"></a>Creazione di un utente test di Printix

Questa sezione descrive come creare un utente chiamato Britta Simon in Printix. Printix supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Printix viene creato un nuovo utente, se non esiste già. 

> [!NOTE]
> Per creare un utente manualmente è necessario contattare il [team di supporto di Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione, Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Printix.

![Screenshot che mostra un utente con l'accesso predefinito.][200] 

**Per assegnare Britta Simon a Printix, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Screenshot che mostra l'opzione Applicazioni aziendali selezionata nella sezione Gestisci, con l'opzione Tutte le applicazioni selezionata.][201] 

1. Nell'elenco di applicazioni selezionare **Printix**.

    ![Screenshot che mostra l'elenco di applicazioni in cui selezionare Printix.](./media/printix-tutorial/tutorial_printix_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Screenshot che mostra l'opzione Utenti e gruppi selezionata nel menu Gestisci.][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Screenshot che mostra il pulsante Aggiungi e la pagina Aggiungi assegnazione in cui è possibile selezionare Utenti e gruppi.][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Printix nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Printix.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

