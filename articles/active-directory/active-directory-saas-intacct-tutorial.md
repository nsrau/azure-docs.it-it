---
title: 'Esercitazione: Integrazione di Azure Active Directory con Intacct | Documentazione Microsoft'
description: Informazioni su come usare Intacct con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 20db13c5ba67fcc402db5c692842719dd9a34eb1
ms.openlocfilehash: 0ea77d5f9c5b139ce6f87bc0cdd817166b03c22e
ms.lasthandoff: 03/01/2017

---

# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Esercitazione: Integrazione di Azure Active Directory con Intacct
Questa esercitazione descrive l'integrazione di Azure e Intacct.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Intacct

Al termine dell'esercitazione, gli utenti di Azure Active Directory (Azure AD) assegnati a Intacct potranno accedere all'applicazione tramite il sito aziendale di Intacct (accesso avviato dal provider di servizi) o tramite il [Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Intacct
* Configurazione dell'accesso Single Sign-On
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario")

## <a name="enable-the-application-integration-for-intacct"></a>Abilitare l'integrazione dell'applicazione per Intacct
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Intacct.

**Per abilitare l'integrazione dell'applicazione per Intacct, seguire questa procedura:**

1. Nel portale di Azure classico fare clic sull'icona di Active Directory nel riquadro sinistro.

   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applicazioni** nel menu superiore.

   ![Applicazioni](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.

   ![Aggiungere un'applicazione](./media/active-directory-saas-intacct-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-intacct-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella casella di ricerca immettere **Intacct**.

   ![Raccolta di applicazioni](./media/active-directory-saas-intacct-tutorial/IC790031.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Intacct** e quindi fare clic su **Completa** per aggiungere l'applicazione.

   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Intacct tramite il proprio account in Azure AD. Per l'autenticazione viene usata la federazione basata sul protocollo SAML.  

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base&64;. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Intacct** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la pagina **Configura accesso Single Sign-On**.

   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Intacct** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso** della pagina **Configure App URL** (Configura URL app) digitare l'URL che usa il modello "*https://Intacct.com/company*" e quindi fare clic su **Avanti**.

   ![Configurare l'URL dell'app](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Intacct** fare clic su **Scarica certificato** e quindi salvare il file del certificato nel computer.

   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Intacct come amministratore.
6. Fare clic sulla scheda **Company** (Azienda) e quindi su **Company Info** (Informazioni sull'azienda).

   ![Azienda](./media/active-directory-saas-intacct-tutorial/IC790037.png "Azienda")
7. Fare clic sulla scheda **Security** (Sicurezza) e quindi su **Edit** (Modifica).

   ![Sicurezza](./media/active-directory-saas-intacct-tutorial/IC790038.png "Sicurezza")
8. Nella sezione **Single sign on (SSO)** seguire questa procedura:

   ![Single sign on](./media/active-directory-saas-intacct-tutorial/IC790039.png "single sign on")

   1. Selezionare **Abilita Single Sign-On**.
   2. In **Identity provider type** (Tipo di provider di identità) selezionare **SAML 2.0**.
   3. Nella pagina **Configura accesso Single Sign-On in Intacct** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer URL** (URL autorità di certificazione).
   4. Nella pagina **Configura accesso Single Sign-On in Intacct** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Login URL** (URL di accesso).
   5. Creare un file con **codifica Base&64;** dal certificato scaricato. Per altre informazioni, vedere il video che spiega [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).      
   6. Aprire il certificato con codifica Base&64; nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella **Certificato**.
   7. Fare clic su **Save**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la pagina **Configura accesso Single Sign-On**.

   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurare l'accesso Single Sign-On")

## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Intacct, è necessario eseguirne il provisioning in Intacct. In Intacct il provisioning è un'attività manuale.

**Per eseguire il provisioning degli account utente, seguire questa procedura:**

1. Accedere al tenant di **Intacct**.
2. Fare clic sulla scheda **Company** (Azienda) e quindi su **Users** (Utenti).

   ![Utenti](./media/active-directory-saas-intacct-tutorial/IC790041.png "Utenti")
3. Fare clic sulla scheda **Add** (Aggiungi).

   ![Aggiungi](./media/active-directory-saas-intacct-tutorial/IC790042.png "Aggiungi")
4. Nella sezione **Informazioni utente** seguire questa procedura:

   ![Informazioni utente](./media/active-directory-saas-intacct-tutorial/IC790043.png "Informazioni utente")

   1. Nelle caselle di testo **User ID**, **Last name**, **First name**, **Email address**, **Title** e **Phone** immettere l'ID utente, il cognome, il nome, l'indirizzo di posta elettronica, la posizione e il numero di telefono di un account Azure AD di cui si vuole eseguire il provisioning nella sezione **Informazioni utente**.
   2. Selezionare i **privilegi di amministratore** di un account Azure AD di cui si vuole eseguire il provisioning.
   3. Fare clic su **Save**. Il titolare dell'account Azure AD riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

>[!NOTE]
>Per eseguire il provisioning degli account utente di Azure AD, è possibile usare altri strumenti o API per la creazione di account utente Intacct forniti da Intacct.
>

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario assegnare gli utenti di Azure AD a Intacct. Dopo l'assegnazione degli utenti, questi possono accedere all'applicazione.

**Per assegnare gli utenti a Intacct seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Intacct** fare clic su **Assegna utenti**.

   ![Assegnare utenti](./media/active-directory-saas-intacct-tutorial/IC790044.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.

   ![Sì](./media/active-directory-saas-intacct-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si imposta Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Intacct.

![Assegnare utenti][200]

**Per assegnare Britta Simon a Intacct, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory, andare ad **Applicazioni aziendali** e quindi fare clic su **Tutte le applicazioni**.

    ![Assegnare utenti][201]

2. Nell'elenco delle applicazioni selezionare **Intacct**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png)

3. Nel menu **Gestisci** fare clic su **Utenti e gruppi**.

    ![Assegnare utenti][202]

4. Fare clic sul pulsante **Aggiungi** e quindi in **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Assegnare utenti][203]

5. In **Utenti e gruppi** selezionare **Britta Simon** nell'elenco degli utenti.

6. In **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. In **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.



### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Intacct nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Intacct.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

