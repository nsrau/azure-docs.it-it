---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cisco Spark | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Cisco Spark.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 931004d458fd0cbf3e002805ca701d777ddc57c2
ms.openlocfilehash: f0f12b1667b7f45fd164fac658502c93e8afb855
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Spark
Questa esercitazione descrive come integrare Cisco Spark con Azure Active Directory (Azure AD).

L'integrazione di Cisco Spark con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Cisco Spark
* È possibile abilitare gli utenti per l'accesso automatico Single Sign-On (SSO) a Cisco Spark con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Cisco Spark, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di **Cisco Spark** abilitata per l'accesso Single Sign-On (SSO)

>[!NOTE]
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione. 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Cisco Spark dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

## <a name="add-cisco-spark-from-the-gallery"></a>Aggiungere Cisco Spark dalla raccolta
Per configurare l'integrazione di Cisco Spark in Azure AD, è necessario aggiungere Cisco Spark dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cisco Spark dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Cisco Spark**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)
7. Nel riquadro dei risultati selezionare **Cisco Spark** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con Cisco Spark con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Cisco Spark che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cisco Spark.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in Cisco Spark. Per configurare e testare l'accesso Single Sign-On di Azure AD con Cisco Spark, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Cisco Spark](#creating-a-cisco-spark-test-user)**: per avere una controparte di Britta Simon in Cisco Spark collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
Questa sezione descrive come abilitare l'accesso SSO di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Cisco Spark.

L'applicazione Cisco Spark prevede che le asserzioni SAML contengano attributi specifici. Configurare gli attributi seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla scheda **"Attributes"** (Attributi) dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Per configurare l'accesso SSO di Azure AD con Cisco Spark, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **Cisco Spark** del portale di Azure classico fare clic su **Attributi**.
   
    ![Configura accesso Single Sign-On][5]
2. Nella finestra di dialogo **Attributi token SAML** seguire questa procedura:
  1. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
  2. Nella casella di testo **Nome attributo** digitare **uid**.
  3. Nell'elenco **Valore attributo** selezionare **user.userprincipal**.
  4. Fare clic su **Complete**. Fare quindi clic su **Salva le modifiche** nella parte inferiore della pagina.
3. Nel menu in alto fare clic su **Avvio rapido**.
   
    ![Configura accesso Single Sign-On][6]
4. Nella pagina di integrazione dell'applicazione **Cisco Spark** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][7] 
5. Nella pagina **Stabilire come si desidera che gli utenti accedano a Cisco Spark** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)
6. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)
  1. Nella casella di testo URL di accesso digitare l'URL usando il modello seguente: `https://web.ciscospark.com/#/signin`.
  2. Fare clic su **Avanti**.
7. Nella pagina **Configura accesso Single Sign-On in Cisco Spark** fare clic su **Download metadati** e quindi salvare il file nel computer.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)
8. Accedere a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) con le credenziali di amministratore complete.
9. Selezionare **Settings** (Impostazioni) e quindi nella sezione **Authentication** (Autenticazione) fare clic su **Modify** (Modifica).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
9. Selezionare **Integrate a 3rd-party identity provider. (Advanced)** (Integra provider di terze parti - Avanzate) e passare alla schermata successiva.
10. Fare clic su **Download Metadata File** (Scarica file metadati) e salvare il file nel computer.
11. Nella pagina **Import Idp Metadata** (Importa metadati Idp) trascinare il file di metadati di Azure AD nella pagina o usare l'opzione di esplorazione dei file per individuare e caricare il file di metadati di Azure AD. Selezionare quindi **Require certificate signed by a certificate authority in Metadata (more secure)** (Richiedi certificato firmato da un'autorità di certificazione in metadati - più sicura) e fare clic su **Next** (Avanti). 
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)
12. Selezionare **Test SSO Connection** (Test connessione SSO) e, quando viene aperta una nuova scheda del browser, eseguire l'autenticazione con Azure AD effettuando l'accesso.
13. Tornare alla scheda **Cisco Cloud Collaboration Management** del browser. Se il test ha avuto esito positivo, selezionare l'opzione **This test was successful. Enable Single Sign-On ** (Test riuscito. Abilita Single Sign-On), quindi fare clic su **Next** (Avanti).
14. Nel portale di Azure AD classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
15. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
     ![Single Sign-On di Microsoft Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 
  1. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="create-a-cisco-spark-test-user"></a>Creare un utente test per Cisco Spark
In questa sezione viene creato un utente di nome Britta Simon in Cisco Spark. In questa sezione viene creato un utente di nome Britta Simon in Cisco Spark.

1. Accedere a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) con le credenziali di amministratore complete.
2. Fare clic su **Users** (Utenti) e quindi su **Manage Users** (Gestisci utenti).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 
3. Nella finestra **Manage User** (Gestisci utenti) selezionare **Manually add or modify users** (Aggiungi o modifica manualmente gli utenti) e fare clic su **Next** (Avanti).
4. Selezionare **Names and Email address** (Nomi e indirizzi di posta elettronica). Compilare quindi la casella di testo come indicato di seguito:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
  1. Nella casella di testo **Nome** digitare **Britta**. 
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare **britta.simon@contoso.com**.
5. Fare clic sul segno più per aggiungere Britta Simon. Quindi fare clic su **Next**.
6. Nella finestra **Add Services for Users** (Aggiungi servizi per utenti) fare clic su **Save** (Salva) e quindi su **Finish** (Fine).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso SSO di Azure, concedendole così accesso a Cisco Spark.

![Assegna utente][200] 

**Per assegnare Britta Simon a Cisco Spark, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco delle applicazioni selezionare **Cisco Spark**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cisco Spark nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cisco Spark.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png

