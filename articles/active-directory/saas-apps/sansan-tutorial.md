---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sansan | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sansan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: c8b5a84c853a974ede77e716b77f0a5007775ef7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231213"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Esercitazione: integrazione di Azure Active Directory con Sansan

Questa esercitazione descrive come integrare Sansan con Azure Active Directory (Azure AD).

L'integrazione di Sansan con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Sansan
- È possibile abilitare gli utenti per l'accesso automatico a Sansan (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Sansan, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Sansan abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Sansan dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sansan-from-the-gallery"></a>Aggiunta di Sansan dalla raccolta
Per configurare l'integrazione di Sansan in Azure AD, è necessario aggiungere Sansan dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Sansan dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Sansan**.

    ![Creazione di un utente test di Azure AD](./media/sansan-tutorial/tutorial_sansan_search.png)

5. Nel pannello dei risultati selezionare **Sansan** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Sansan in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Sansan che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sansan.

Per stabilire la relazione di collegamento, in Sansan assegnare il valore di **nome utente** in Azure AD come valore di **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Sansan, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Sansan](#creating-a-sansan-test-user)**: per avere una controparte di Britta Simon in Sansan collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Sansan.

**Per configurare l'accesso Single Sign-On di Azure AD con Sansan, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Sansan** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. Nella sezione **URL e dominio Sansan** seguire questa procedura:

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_url.png)

    Nella casella di testo **URL di accesso** digitare un URL usando i criteri seguenti: 
    
    | Environment | URL |
    |:--- |:--- |
    | Web PC |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | App per dispositivi mobili nativa |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Impostazioni del browser per dispositivi mobili |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'URL di accesso effettivo. Per ottenere questi valori, contattare il [team di supporto client di Sansan](https://www.sansan.com/form/contact). 
     
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_general_400.png)

6. L'applicazione Sansan prevede molteplici **identificatori** e **gli URL di risposta** per supportare più ambienti (Web PC, app native per dispositivi mobili, impostazioni del browser per dispositivi mobili), che possono essere configurati tramite PowerShell script. La procedura dettagliata viene spiegata di seguito.

7. Per configurare più **identificatori** e **URL di risposta** per l'applicazione Sansan utilizzando uno script di PowerShell, seguire questa procedura:

    ![Configurare l'oggetto Single Sign-On](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Passare ora alla pagina delle **Proprietà** dell'applicazione **Sansan**, copiare l'**ID oggetto** usando il pulsante **Copia** e incollarlo nel Blocco note.

    b. L'**ID oggetto** copiato dal portale di Azure sarà usato come **ServicePrincipalObjectId** nello script di PowerShell menzionato più avanti nel tutorial. 

    c. Aprire ora un prompt dei comandi di Windows PowerShell con privilegi elevati.
    
    >[!NOTE] 
    > È necessario installare il modulo AzureAD. Usare il comando `Install-Module -Name AzureAD`. Se viene chiesto se installare un modulo NuGet o il nuovo modulo Azure Active Directory V2 PowerShell, digitare Y e premere INVIO.

    d. Eseguire `Connect-AzureAD` e accedere con un account utente Amministratore globale.

    e. Usare lo script seguente per aggiornare più URL a un'applicazione:

    ```poweshell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. Dopo il completamento dello script di PowerShell il risultato dello script sarà simile al seguente e i valori dell'URL saranno aggiornati ma non vengono riflessi nel portale di Azure. 

    ![Configurare lo script Single Sign-On](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. Nella sezione **Configurazione di Sansan** fare clic su **Configura Sansan** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Per configurare l'accesso Single Sign-On sul lato **Sansan**, è necessario inviare il **Certificato** scaricato, l'**URL di disconnessione**, l'**ID entità SAML** e l'**URL del servizio Single Sign-On SAML** al [team di supporto di Sansan](https://www.sansan.com/form/contact). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

>[!NOTE]
>Le impostazioni del browser PC sono applicabili anche alle app per dispositivi mobili e al browser per dispositivi mobili, oltre che per Web PC. 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/sansan-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/sansan-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/sansan-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/sansan-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-sansan-test-user"></a>Creazione di un utente di test di Sansan

In questa sezione viene creato un utente chiamato Britta Simon in Sansan. L'applicazione Sansan richiede che venga eseguito il provisioning dell'utente all'interno dell'applicazione prima di eseguire l'accesso Single Sign-On. 

>[!NOTE]
>Se si deve creare un utente manualmente o creare un batch di utenti, è necessario contattare il [team di supporto Sansan](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Sansan.

![Assegna utente][200] 

**Per assegnare Britta Simon a Sansan, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Sansan**.

    ![Configure Single Sign-On](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Sansan nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Sansan.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

