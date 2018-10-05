---
title: 'Esercitazione: Integrazione di Azure Active Directory con FreshDesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ce302db74f831e67b576e4c0001f21473fd7f2e0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037525"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Esercitazione: Integrazione di Azure Active Directory con FreshDesk

Questa esercitazione descrive come integrare FreshDesk con Azure Active Directory (Azure AD).

L'integrazione di FreshDesk con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a FreshDesk
- È possibile abilitare gli utenti per l'accesso automatico a FreshDesk (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con FreshDesk, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di FreshDesk abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di FreshDesk dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-freshdesk-from-the-gallery"></a>Aggiunta di FreshDesk dalla raccolta

Per configurare l'integrazione di FreshDesk in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere FreshDesk dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]

3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Freshdesk**. Selezionare **FreshDesk** nel pannello dei risultati e quindi selezionare il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con FreshDesk in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di FreshDesk che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FreshDesk.

La relazione di collegamento viene stabilita assegnando il valore di **Nome utente** in Azure AD come valore dell'**indirizzo di posta elettronica** in FreshDesk.

Per configurare e testare l'accesso Single Sign-On di Azure AD con FreshDesk, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di FreshDesk](#creating-a-freshdesk-test-user)**: per avere una controparte di Britta Simon in FreshDesk collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione FreshDesk.

**Per configurare l'accesso Single Sign-On di Azure AD con FreshDesk, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **FreshDesk** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Nella sezione **URL e dominio FreshDesk** seguire questa procedura:

    ![Configure Single Sign-On](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello `https://<tenant-name>.freshdesk.com` o qualsiasi altro valore suggerito da Freshdesk.

    > [!NOTE]
    > Si noti che questo non è il valore reale. È necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto di FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg).

4. L'applicazione prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il valore predefinito dell'**ID utente** è **user.userprincipalname** ma **FreshDesk** si aspetta che venga mappato all'indirizzo di posta elettronica dell'utente. A tale scopo è possibile usare l'attributo **user.mail** dall'elenco oppure usare il valore di attributo appropriato in base alla configurazione dell'organizzazione.

    ![Configure Single Sign-On](./media/freshdesk-tutorial/tutorial_attribute.png)

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

    > [!NOTE]
    > Se si verificano problemi, fare riferimento a questo [collegamento](https://support.freshdesk.com/support/discussions/topics/317543).

6. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/freshdesk-tutorial/tutorial_general_400.png)

7. Installare **OpenSSL** nel sistema, se non è già installato.

8. Aprire il **Prompt dei comandi** ed eseguire i comandi seguenti:

    a. Immettere il valore `openssl x509 -inform DER -in FreshDesk.cer -out certificate.crt` al prompt dei comandi.

    > [!NOTE]
    > In questo comando **FreshDesk.cer** è il certificato scaricato dal portale di Azure.

    b. Immettere il valore `openssl x509 -noout -fingerprint -sha256 -inform pem -in certificate.crt` al prompt dei comandi. 
    
    > [!NOTE]
    > In questo comando **certificate.crt** è il certificato di output generato nel passaggio precedente.

    c. Copia il valore **Identificazione personale** e incollarlo nel Blocco note. Rimuovere i due punti dall'identificazione personale e ottenere il valore di identificazione personale finale.

9. Nella sezione **FreshDesk Configuration** (Configurazione di FreshDesk) fare clic su **Configure FreshDesk** (Configura FreshDesk) per aprire la finestra Configura accesso. Copiare l’URL servizio Single Sign-On SAML e l’URL Sign-Out dalla sezione **Quick Reference** (Riferimento rapido).

    ![Configure Single Sign-On](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

10. In un'altra finestra del Web browser accedere al sito aziendale di Freshdesk come amministratore.

11. Selezionare l'**icona Impostazioni** e nella sezione **Sicurezza** seguire questa procedura:

    ![Single Sign-On](./media/freshdesk-tutorial/IC776770.png "Single Sign-On")
  
    a. Per **Single Sign On (SSO)** selezionare **On**.

    b. Selezionare **SAML SSO**.

    c. Nella casella di testo **SAML Login URL** (URL di accesso SAML) incollare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.

    d. Nella casella di testo **URL disconnessione** incollare il valore di **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.

    e. Nella casella di testo **Security Certificate Fingerprint** (Impronta digitale certificato di protezione) incollare il valore **Identificazione personale** ottenuto in precedenza dopo la rimozione dei due punti.
  
    f. Fare clic su **Save**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Creazione di un utente test di Azure AD](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.

    ![Creazione di un utente test di Azure AD](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="creating-a-freshdesk-test-user"></a>Creazione di un utente test di FreshDesk

Per consentire agli utenti di Azure AD di accedere a FreshDesk, è necessario eseguirne il provisioning in FreshDesk.  
Nel caso di FreshDesk, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Freshdesk** .

2. Nel menu in alto fare clic su **Admin**.

   ![Amministratore](./media/freshdesk-tutorial/IC776772.png "Amministratore")

3. Nella scheda **General Settings** fare clic su **Agents**.
  
   ![Agents](./media/freshdesk-tutorial/IC776773.png "Agents")

4. Fare clic su **New Agent**.

    ![New Agent](./media/freshdesk-tutorial/IC776774.png "New Agent")

5. Nella finestra di dialogo Agent Information seguire questa procedura:

   ![Agent Information](./media/freshdesk-tutorial/IC776775.png "Agent Information")

   a. Nella casella di testo **Email** , digitare l’indirizzo di posta elettronica dell'account Azure AD di cui si vuole eseguire il provisioning.

   b. Nella casella di testo **Full Name** , digitare il nome dell'account Azure AD di cui si vuole eseguire il provisioning.

   c. Nella casella di testo **Title** , digitare il titolo dell'account Azure AD di cui si vuole eseguire il provisioning.

   d. Fare clic su **Save**.

    >[!NOTE]
    >Il titolare dell'account AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.
    >
    >[!NOTE]
    >È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Freshdesk per eseguire il provisioning degli account utente di AAD in FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Box.

![Assegna utente][200]

**Per assegnare Britta Simon a FreshDesk, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni, selezionare **FreshDesk**.

    ![Configure Single Sign-On](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro FreshDesk nel pannello di accesso, si apre la pagina per accedere all'applicazione FreshDesk.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png