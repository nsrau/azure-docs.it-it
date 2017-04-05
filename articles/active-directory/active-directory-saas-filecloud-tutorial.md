---
title: 'Esercitazione: Integrazione di Azure Active Directory con FileCloud | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e FileCloud.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f39f0ddd-b504-4562-971f-77b88d1e75fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: b672133e0604e35d6e398fbee4db303f70158111
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Esercitazione: Integrazione di Azure Active Directory con FileCloud
Questa esercitazione descrive l'integrazione di FileCloud con Azure Active Directory (Azure AD).

L'integrazione di FileCloud con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a FileCloud
* È possibile abilitare gli utenti per l'accesso automatico a FileCloud Single Sign-On (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con FileCloud, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di FileCloud abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di FileCloud dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-filecloud-from-the-gallery"></a>Aggiungere FileCloud dalla raccolta
Per configurare l'integrazione di FileCloud in Azure AD, è necessario aggiungere FileCloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere FileCloud dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]

6. Nella casella di ricerca digitare **FileCloud**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_01.png)

7. Nel riquadro dei risultati selezionare **FileCloud**, e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con FileCloud in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di FileCloud che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FileCloud.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in FileCloud.

Per configurare e testare l'accesso SSO di Azure AD con FileCloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test FileCloud](#creating-a-filecloud-test-user)**: per avere una controparte di Britta Simon in FileCloud collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso SSO di Azure AD nel portale di Azure classico e viene configurato l'accesso SSO nell'applicazione FileCloud.

**Per configurare l'accesso Single Sign-On di Azure AD con FileCloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **FileCloud** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a FileCloud** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_03.png)

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_04.png)
  1. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.filecloudhosted.com`.
  2. Nella casella di testo **Identificatore** digitare: `https://<subdomain>.filecloudhosted.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`.
  3. Fare clic su **Avanti**.
   
    >[!NOTE]
    >È necessario aggiornare questi valori con l'URL di accesso e l'ID effettivi. Per ottenere questi valori, contattare il team di supporto di FileCloud all'indirizzo <mailto:support@codelathe.com>.
    >  

4. Nella pagina **Configura accesso Single Sign-On in FileCloud** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_05.png)
 1. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
 2. Fare clic su **Avanti**.

5. In un'altra finestra del browser Web accedere al tenant FileCloud come amministratore.

6. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni). 
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

7. Fare clic sulla scheda **SSO** nella sezione Settings (Impostazioni). 
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

8. Impostare **SAML** come **Default SSO Type** (Tipo SSO predefinito) nel riquadro **Single Sign On (SSO) Settings** (Impostazioni Single Sign-On - SSO).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

9. Nella casella di testo **IdP End Point URL** (URL endpoint IdP) immettere il valore di **ID entità** della configurazione guidata dell'applicazione di Azure AD nel riquadro **SAML Settings** (Impostazioni SAML).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

10. Aprire il file di metadati scaricato in Blocco note, copiare i contenuti negli Appunti, quindi incollarli nella casella di testo **IdP Meta Data** (Metadati IdP) nel riquadro **SAML Settings** (Impostazioni SAML).
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

11. Fare clic sul pulsante **Salva** .

12. Nel portale di Azure classico selezionare la conferma della configurazione e fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]

13. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_05.png)
 1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.  
 2. Nella casella di testo **Nome utente** digitare **BrittaSimon**. 
 3. Fare clic su **Avanti**.

6. Nella pagina **Profilo utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_06.png) 
 1. Nella casella di testo **Nome** digitare **Britta**.  
 2. Nella casella di testo **Cognome** digitare **Simon**. 
 3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**. 
 4. Nell'elenco **Ruolo** selezionare **Utente**. 
 5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_08.png) 
 1. Prendere nota del valore visualizzato in **Nuova password**.  
 2. Fare clic su **Complete**.   

### <a name="create-a-filecloud-test-user"></a>Creare un utente test di FileCloud
L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in FileCloud. FileCloud supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a FileCloud verrà creato un nuovo utente, se questo non esiste già. 

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il team di supporto di FileCloud. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso SSO di Azure concedendole l'accesso a FileCloud.

![Assegna utente][200]

**Per assegnare Britta Simon a FileCloud, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]

2. Nell'elenco delle applicazioni, selezionare **FileCloud**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_50.png)

3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro FileCloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione FileCloud.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_205.png

