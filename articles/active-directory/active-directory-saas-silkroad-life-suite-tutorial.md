---
title: 'Esercitazione: Integrazione di Azure Active Directory con SilkRoad Life Suite | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e SilkRoad Life Suite.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Esercitazione: Integrazione di Azure Active Directory con SilkRoad Life Suite
Questa esercitazione descrive l'integrazione di SilkRoad Life Suite con Azure Active Directory (Azure AD). 

L'integrazione di SilkRoad Life Suite con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a SilkRoad Life Suite. 
* È possibile abilitare gli utenti per l'accesso automatico a SilkRoad Life Suite Single Sign-On (SSO) con i propri account Azure AD.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con SilkRoad Life Suite, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di SilkRoad Life Suite abilitata per l'accesso SSO

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SilkRoad Life Suite dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-silkroad-life-suite-from-the-gallery"></a>Aggiungere SilkRoad Life Suite dalla raccolta
Per configurare l'integrazione di SilkRoad Life Suite in Azure AD, è necessario aggiungere SilkRoad Life Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SilkRoad Life Suite dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]

6. Nella casella di ricerca digitare **SilkRoad Life Suite**.
   
    ![Applicazioni][5]

7. Nel riquadro dei risultati selezionare **SilkRoad Life Suite** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Applicazioni][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso SSO di Azure AD con SilkRoad Life Suite in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di SilkRoad Life Suite che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SilkRoad Life Suite.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in SilkRoad Life Suite.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SilkRoad Life Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)** : per avere una controparte di Britta Simon in SilkRoad Life Suite collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso SSO di Azure AD nel portale di Azure classico e configurare l'accesso SSO nell'applicazione SilkRoad Life Suite.

**Per configurare Single Sign-On di Azure AD con SilkRoad Life Suite, seguire questa procedura:**

1. Accedere al sito aziendale di SilkRoad come amministratore. 

  >[!NOTE] 
  > Per ottenere l'accesso all'applicazione SilkRoad Life Suite Authentication per configurare la federazione con Microsoft Azure AD, contattare il supporto SilkRoad o il rappresentante dei servizi SilkRoad.
  > 

2. Passare a **Service Provider** (Provider di servizi) e quindi fare clic su **Federation Details** (Dettagli federazione). 
   
    ![Single Sign-On di Microsoft Azure AD][10] 

3. Fare clic su **Download Federation Metadata**(Scarica metadati federazione) e quindi salvare il file di metadati nel computer.
   
    ![Single Sign-On di Microsoft Azure AD][11] 

4. Nella pagina di integrazione dell'applicazione **SilkRoad Life Suite** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 

5. Nella pagina **Stabilire come si desidera che gli utenti accedano a SilkRoad Life Suite** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][7] 

6. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Single Sign-On di Microsoft Azure AD][8]   
 1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere al sito SilkRoad Life Suite, ad esempio *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*.  
 2. Aprire il file dei metadati **Silkroad** scaricato. 
 3. Trovare il tag **AssertionConsumerService** e quindi copiare l'attributo **Location**.         
   
    ![Single Sign-On di Microsoft Azure AD][21] 
 4. Incollare il valore nella casella di testo **URL di risposta** .  
 5. Fare clic su **Avanti**.

6. Nella pagina **Configura accesso Single Sign-On in SilkRoad Life Suite** seguire questa procedura:
   
    ![Single Sign-On di Microsoft Azure AD][9]  
 1. Fare clic su Download certificato e quindi salvare il file nel computer.  
 2. Fare clic su **Avanti**.

7. Nell'applicazione **SilkRoad**, fare clic su **Authentication Sources** (Origini autenticazione).
   
    ![Single Sign-On di Microsoft Azure AD][12] 

8. Fare clic su **Add Authentication Source**(Aggiungi origine autenticazione). 
   
    ![Accesso Single Sign-On di Azure AD][13] 

9. Nella sezione **Add Authentication Source** (Aggiungi origine autenticazione) seguire questa procedura: 
   
    ![Single Sign-On di Microsoft Azure AD][14]  
 1. In **Option 2 - Metadata File** (Opzione 2 - File di metadati) fare clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato.  
 2. Fare clic su **Create Identity Provider using File Data**.

10. Nella sezione **Authentication Sources** (Origini autenticazione) fare clic su **Edit** (Modifica). 
    
     ![Single Sign-On di Microsoft Azure AD][15] 

11. Nella finestra di dialogo **Edit Authentication Source** (Modifica origine autenticazione) seguire questa procedura: 
    
     ![Single Sign-On di Microsoft Azure AD][16] 
 1. In **Enabled** (Attivo) selezionare **Yes** (Sì).   
 2. Nella casella di testo **IdP Description** digitare una descrizione per la configurazione, ad esempio *Azure AD SSO*.  
 3. Nella casella di testo **IdP Name** digitare un nome specifico per la configurazione, ad esempio *Azure SP*.  
 4. Fare clic su **Save**.

12. Disabilitare tutte le altre origini di autenticazione. 
    
     ![Single Sign-On di Microsoft Azure AD][17]

13. Nella pagina **Conferma Single Sign-On** del portale di Azure classico fare clic su **Avanti**.  
    
     ![Single Sign-On di Microsoft Azure AD][18]

14. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
    
     ![Single Sign-On di Microsoft Azure AD][19]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.  
 2. Nella casella di testo **Nome utente** digitare **BrittaSimon**. 
 3. Fare clic su **Avanti**.

6. Nella pagina **Profilo utente** seguire questa procedura: 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. Nella casella di testo **Nome** digitare **Britta**.    
 2. Nella casella di testo **Cognome** digitare **Simon**. 
 3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**. 
 4. Nell'elenco **Ruolo** selezionare **Utente**.
 5. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. Prendere nota del valore visualizzato in **Nuova password**. 
 2. Fare clic su **Completa**.   

### <a name="create-a-silkroad-life-suite-test-user"></a>Creare un utente test di SilkRoad Life Suite
Questa sezione descrive come creare un utente chiamato Britta Simon in SilkRoad Life Suite. Britta deve avere un ID SSO, a volte definito *AuthParam*, che corrisponde al valore **emailaddress** in Azure AD.

**Per creare un utente test denominato Britta Simon in SilkRoad Life Suite, seguire questa procedura:**

- Chiedere al team di supporto di SilkRoad Life Suite di creare un utente con un valore per l'attributo **SSO ID** equivalente all'**indirizzo di posta elettronica**di Britta Simon in Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon all'uso dell'accesso SSO di Azure concedendole l'accesso a SilkRoad Life Suite.

![Assegna utente][200] 

**Per assegnare Britta Simon a SilkRoad Life Suite, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SilkRoad Life Suite**.
   
    ![Assegna utente][202] 

3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro SilkRoad Life Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SilkRoad Life Suite.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png






