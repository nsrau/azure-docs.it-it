---
title: 'Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: 25a7694cefde344b34d3a46535bf09209b306593
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud

Questa esercitazione descrive come integrare Atlassian Cloud con Azure Active Directory (Azure AD).

L'integrazione di Atlassian Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Atlassian Cloud.
- È possibile abilitare gli utenti per l'accesso automatico ad Atlassian Cloud (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Atlassian Cloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Per abilitare l'accesso Single Sign-On SAML per i prodotti Atlassian Cloud è necessario configurare Identity Manager. Altre informazioni su [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Atlassian Cloud dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Aggiunta di Atlassian Cloud dalla raccolta
Per configurare l'integrazione di Atlassian Cloud in Azure AD, è necessario aggiungere Atlassian Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Atlassian Cloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Atlassian Cloud**, selezionare **Atlassian Cloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Atlassian Cloud nell'elenco dei risultati](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Atlassian Cloud con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Atlassian Cloud che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Atlassian Cloud.

Per stabilire la relazione di collegamento, in Atlassian Cloud assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Atlassian Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Atlassian Cloud](#create-an-atlassian-cloud-test-user)**: per avere una controparte di Britta Simon in Atlassian Cloud collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Atlassian Cloud.

**Per configurare l'accesso Single Sign-On di Azure AD con Atlassian Cloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Atlassian Cloud** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Nella sezione **URL e dominio Atlassian Cloud**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    a. Nella casella di testo **Identificatore** digitare l'URL: `https://id.atlassian.com/login`
    
    b. Nella casella di testo **URL di risposta** digitare l'URL: `https://id.atlassian.com/login/saml/acs`

    c. Nella casella di testo **Stato dell'inoltro** digitare un URL usando il modello seguente: `https://<instancename>.atlassian.net`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<instancename>.atlassian.net`.

    > [!NOTE] 
    > Poiché questi non sono i valori reali, È possibile ottenere questi valori dalla schermata di configurazione SAML di Atlassian Cloud, illustrata più avanti nell'esercitazione.

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di Atlassian Cloud** fare clic su **Configura Atlassian Cloud** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, accedere al portale di Atlassian usando i diritti di amministratore.

9. Passare a **Atlassian Site Administration** (Amministrazione del sito Atlassian)  > **Organizations & Security** (Organizzazioni e sicurezza). Se non si è ancora fatto, creare l'organizzazione e assegnarle un nome. Nel riquadro di spostamento a sinistra fare clic su **Domains** (Domini).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. Selezionare il modo in cui verificare il dominio: **DNS** o **HTTPS**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. Per la verifica DNS selezionare la scheda **DNS** nella pagina **Domains** (Domini) e seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Fare clic su **Copy** (Copia) per copiare il valore per il record TXT.

    b. Dal servizio DNS cercare la pagina di impostazioni per aggiungere un nuovo record.

    c. Selezionare l'opzione per aggiungere un nuovo record e incollare il valore copiato dalla pagina **Domains** (Domini) al campo **Value** (Valore). Il servizio DNS può fare riferimento a questo valore anche come **Answer** (Risposta) o **Description** (Descrizione).

    d. Il record DNS può includere anche i campi seguenti:
    
    * **Record type** (Tipo di record): immettere **TXT**
    * **Name/Host/Alias** (Nome/Host/Alias): lasciare il valore predefinito (@ o vuoto)
    * **Time to live (TTL)** (Durata TTL): immettere **86400**
    
    e.  Salvare il record.

12. Tornare alla pagina **Domains** (Domini) nella schermata di amministrazione dell'organizzazione e fare clic sul pulsante **Verify domain** (Verifica dominio). Immettere il nome del dominio nella finestra popup e fare clic sul pulsante **Verify domain** (Verifica dominio).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > L'applicazione delle modifiche al record TXT può richiedere fino a 72 ore. Non è possibile sapere immediatamente se la verifica del dominio ha avuto esito positivo. Controllare la pagina **Domains** (Domini) subito dopo aver eseguito questi passaggi per determinare lo stato della verifica. Verrà visualizzata la schermata seguente con lo stato aggiornato come **VERIFIED** (VERIFICATO).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. Per la verifica HTTPS selezionare la scheda **HTTPS** nella pagina **Domains** (Domini) e seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  Fare clic su **Download file** (Scarica file) per scaricare il file HTML.

    b.  Caricare il file HTML nella directory radice del dominio.

14. Tornare alla pagina **Domains** (Domini) nella schermata di amministrazione dell'organizzazione e fare clic sul pulsante **Verify domain** (Verifica dominio). Immettere il **nome del dominio** nella finestra popup e fare clic sul pulsante **Verify domain** (Verifica dominio).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. Se il processo di verifica riesce a individuare il file caricato nella directory radice, lo stato del dominio viene aggiornato come **Verified** (Verificato).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Per altre informazioni, fare riferimento alla [documentazione di Atlassian relativa alla verifica dei domini](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

16. Nella barra di spostamento a sinistra fare clic su **SAML single sign-on** (Single Sign-On SAML). Se non si è ancora fatto, eseguire la sottoscrizione ad Atlassian Identity Manager.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. Nella finestra di dialogo **Add SAML configuration** (Aggiungi configurazione SAML) aggiungere le impostazioni del provider di identità nel modo seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Nella casella di testo **Identity provider Entity ID** (ID entità provider di identità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    b. Nella casella di testo **Identity provider SSO URL** (URL SSO provider di identità) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    c. Nel Blocco note aprire il certificato scaricato, copiare i valori senza le righe di inizio e di fine e quindi incollarli nella casella **Public X509 certificate** (Certificato X509 pubblico).
    
    d. Per salvare le impostazioni, fare clic su **Save Configuration** (Salva configurazione).
     
18. Aggiornare le impostazioni di Azure AD per assicurarsi di avere configurato gli URL corretti.
  
    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Copiare il valore di **SP Identity ID** (ID identità SP) dalla schermata SAML e incollarlo nella casella **Identificatore** del portale di Azure, nella sezione **URL e dominio** Atlassian Cloud.
    
    b. Copiare il valore di **SP Assertion Consumer Service URL** (URL del servizio consumer di asserzione SP) dalla schermata SAML e incollarlo nella casella **URL di risposta** del portale di Azure, nella sezione **URL e dominio** Atlassian Cloud.
    
    c. L'URL di accesso corrisponde all'URL del tenant di Atlassian Cloud. 
    
19. Nel portale di Azure fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Creare un utente di test di Atlassian Cloud

Per consentire agli utenti di Azure AD di accedere ad Atlassian Cloud, è necessario effettuarne il provisioning in Atlassian Cloud. Nel caso di Atlassian Cloud il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Nella sezione relativa all'amministrazione del sito del portale di Atlassian fare clic sul pulsante **Users** (Utenti).

    ![Creare l'utente di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Fare clic sul pulsante **Invite user** (Invita utente) per creare un utente in Atlassian Cloud.

    ![Creare l'utente di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Specificare il campo **Email address** (Indirizzo di posta elettronica) per l'utente e assegnare l'accesso all'applicazione. 

    ![Creare l'utente di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Fare clic sul pulsante **Invite users** (Invita utenti). L'invito verrà inviato all'utente tramite posta elettronica. Dopo aver accettato l'invito, l'utente sarà attivo nel sistema. 

>[!NOTE] 
>È anche possibile creare gli utenti in blocco facendo clic sul pulsante **Bulk Create** (Creazione in blocco) nella sezione Users (Utenti).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Atlassian Cloud.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Atlassian Cloud, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Atlassian Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Atlassian Cloud.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

