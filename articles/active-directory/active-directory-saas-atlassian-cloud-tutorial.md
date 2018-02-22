---
title: 'Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: db9e9c7ae8380612bac9d0aeaaaf6df78cba523f
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud

Questa esercitazione descrive come integrare Atlassian Cloud con Azure Active Directory (Azure AD).

L'integrazione di Atlassian Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Atlassian Cloud.
- È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Atlassian Cloud con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Atlassian Cloud, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure AD.
- Per abilitare l'accesso Single Sign-On SAML (Security Assertion Markup Language) per i prodotti Atlassian Cloud, è necessario configurare Identity Manager. Altre informazioni su [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Per testare i passaggi di questa esercitazione, non è consigliabile usare un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

* Aggiunta di Atlassian Cloud dalla raccolta
* Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-atlassian-cloud-from-the-gallery"></a>Aggiungere Atlassian Cloud dalla raccolta
Per configurare l'integrazione di Atlassian Cloud con Azure AD, aggiungere Atlassian Cloud dalla raccolta al proprio elenco di app SaaS gestite seguendo questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali][2]
    
3. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.

    ![Pulsante "Nuova applicazione"][3]

4. Nella casella di ricerca digitare **Atlassian Cloud**, nell'elenco risultati selezionare **Atlassian Cloud** e quindi selezionare **Aggiungi**.

    ![Atlassian Cloud nell'elenco dei risultati](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Atlassian Cloud con un utente di test di nome *Britta Simon*.

Per il funzionamento dell'accesso Single Sign-On, Azure AD identificare l'utente di Atlassian Cloud e la controparte in Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Atlassian Cloud.

Per stabilire la relazione di collegamento, assegnare come *nome utente* di Atlassian Cloud lo stesso valore assegnato al *nome utente* di Azure AD.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Atlassian Cloud, è necessario completare i blocchi predefiniti nelle sezioni seguenti.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Atlassian Cloud.

Per configurare l'accesso Single Sign-On di Azure AD con Atlassian Cloud, seguire questa procedura:

1. Nel riquadro di integrazione dell'applicazione **Atlassian Cloud** del portale di Azure selezionare **Single Sign-On**.

    ![Configurare il collegamento Single Sign-On][4]

2. Nella finestra **Single Sign-On** selezionare **Accesso basato su SAML** nella casella **Modalità Single Sign-On**.
 
    ![Finestra Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Per configurare l'applicazione in modalità avviata da IDP, in **URL e dominio Atlassian Cloud** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. Nella casella **Identificatore** digitare **`https://auth.atlassian.com/saml/<unique ID>`**.
    
    b. Nella casella **URL di risposta** digitare **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`**.

    c. Nella casella **Stato dell'inoltro** digitare un URL con la sintassi seguente: **`https://<instancename>.atlassian.net`**.

4. Per configurare l'applicazione in modalità avviata da SP, selezionare **Mostra impostazioni URL avanzate** e quindi nella casella **URL di accesso** digitare un URL con la sintassi seguente: **`https://<instancename>.atlassian.net`**.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornarli con i valori effettivi di identificatore, URL di risposta e URL di accesso. È possibile ottenere i valori reali dalla schermata di configurazione SAML di Atlassian Cloud. I valori vengono illustrati più avanti nell'esercitazione.

5. In **Certificato di firma SAML** selezionare **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. L'applicazione Atlassian Cloud prevede di trovare un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi di token SAML. 

    Per impostazione predefinita, viene eseguito il mapping del valore **Identificatore utente** a user.userprincipalname. Modificare questo valore per poter eseguire il mapping a user.mail. È anche possibile scegliere un altro valore appropriato in base alla configurazione dell'organizzazione, ma nella maggior parte dei casi l'indirizzo di posta elettronica andrà bene.

    ![Collegamento di download del certificato](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png) 

7. Selezionare **Salva**.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. Per aprire la finestra **Configura accesso**, nella sezione **Configurazione di Atlassian Cloud** selezionare **Configura Atlassian Cloud**. 

9. Nella sezione **Riferimento rapido** copiare l'**ID di entità SAML** e l'**URL del servizio Single Sign-On SAML**. 

    ![Configurazione di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

10. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, accedere al portale di Atlassian con le credenziali di amministratore.

11. Passare a **Atlassian Site Administration** (Amministrazione del sito Atlassian)  > **Organizations & Security** (Organizzazioni e sicurezza). Se non è già stato fatto, creare e assegnare un nome all'organizzazione e quindi nel riquadro a sinistra selezionare **Domains** (Domini).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

12. Selezionare il modo in cui verificare il dominio: **DNS** o **HTTPS**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

13. Per la verifica DNS, nella finestra **Domains** (Domini) selezionare la scheda **DNS** e quindi seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Per copiare i valori per il record di testo (record TXT), selezionare **Copy** (Copia).

    b. Per aggiungere un record, andare alla pagina delle impostazioni nel DNS.

    c. Selezionare l'opzione per aggiungere un nuovo record e quindi incollare nel campo **Value** (Valore) il valore copiato nella finestra **Domains** (Domini). Il record DNS potrebbe fare riferimento a questo valore anche come **Answer** (Risposta) o **Description** (Descrizione).

    d. Il record DNS può includere anche i campi seguenti:
    
    * Nella casella **Record type** (Tipo di record) immettere **TXT**.
    * Nella casella **Name/Host/Alias** (Nome/Host/Alias) lasciare il valore predefinito (@ o vuoto).
    * Nella casella **Time to live (TTL)** (Durata - TTL) immettere **86400**.
    
    e.  Salvare il record.

14. Tornare alla finestra **Domains** (Domini) nella schermata di amministrazione dell'organizzazione e quindi selezionare **Verify domain** (Verifica dominio). Nella casella **Domain** (Dominio) digitare il nome di dominio e quindi selezionare **Verify domain** (Verifica dominio).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)  

    > [!NOTE]
    > Poiché l'applicazione delle modifiche al record TXT può richiedere fino a 72 ore, non è possibile sapere immediatamente se la verifica del dominio ha avuto esito positivo. Per visualizzare lo stato della verifica, controllare la finestra **Domains** (Domini) subito dopo avere completato questa procedura. Lo stato aggiornato verrà visualizzato come *Verified* (Verificato), come illustrato nell'immagine seguente:
    > 
    > ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)
    > 
    > 

15. Per la verifica HTTPS, nella finestra **Domains** (Domini) selezionare la scheda **HTTPS** e quindi seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a. Per scaricare il file HTML, selezionare **Download file** (Scarica file).

    b. Caricare il file HTML nella directory radice del dominio.

16. Tornare alla pagina **Domains** (Domini) nella schermata di amministrazione dell'organizzazione e selezionare **Verify domain** (Verifica dominio). Nella casella **Domain** (Dominio) della finestra **Verify domain** (Verifica dominio) digitare il **nome di dominio** e quindi selezionare **Verify domain** (Verifica dominio).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

17. Se il processo di verifica riesce a individuare il file caricato nella directory radice, lo stato del dominio viene aggiornato come *Verified* (Verificato), come illustrato qui:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Per altre informazioni, vedere [Atlassian domain verification](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) (Verifica del dominio di Atlassian).

18. Nel riquadro sinistro selezionare **SAML single sign-on** (Single Sign-On SAML). Se non è ancora stato fatto, sottoscrivere Atlassian Identity Manager.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

19. Nella finestra **Add SAML configuration** (Aggiungi configurazione SAML) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Nella casella **Identity provider Entity ID** (ID entità del provider di identità) incollare l'ID di entità SAML copiato dal portale di Azure.

    b. Nella casella **Identity provider SSO URL** (URL SSO del provider di identità) incollare l'URL del servizio Single Sign-On SAML copiato dal portale di Azure.

    c. Aprire il certificato scaricato dal portale di Azure in un file TXT, copiare il valore (senza le righe *Begin Certificate* ed *End Certificate*) e quindi incollarlo nella casella **Public X509 certificate** (Certificato X509 pubblico).
    
    d. Selezionare **Save Configuration** (Salva configurazione).
     
20. Per assicurarsi di avere configurato gli URL corretti, aggiornare le impostazioni di Azure AD seguendo questa procedura:
  
    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Nella finestra SAML copiare l'**ID identità SP** e quindi nel portale di Azure, in **URL e dominio** Atlassian Cloud, incollarlo nella casella **Identificatore**.
    
    b. Nella finestra SAML copiare l'**URL del servizio consumer di asserzione SP** e quindi nel portale di Azure, in **URL e dominio** Atlassian Cloud, incollarlo nella casella **URL di risposta**.  
        L'URL di accesso corrisponde all'URL del tenant di Atlassian Cloud. 

    > [!NOTE]
    > I clienti esistenti, dopo avere aggiornato i valori **SP Identity ID** (ID identità SP) e **SP Assertion Consumer Service URL** (URL del servizio consumer di asserzione SP) nel portale di Azure, devono selezionare **Yes, update configuration** (Sì, aggiorna la configurazione). I nuovi clienti possono ignorare questo passaggio. 
    
21. Nel portale di Azure selezionare **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Durante la configurazione dell'app, nel [portale di Azure](https://portal.azure.com) è disponibile un riepilogo delle istruzioni precedenti. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali**, è sufficiente selezionare la scheda **Single Sign-On** e quindi accedere alla documentazione incorporata nella sezione **Configurazione** nella parte inferiore della finestra. Per altre informazioni, vedere la [documentazione incorporata su Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione si crea un utente di test Britta Simon nel portale di Azure seguendo questa procedura:

   ![Creare un utente test di Azure AD][100]

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, selezionare **Utenti e gruppi** > **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Nella finestra **Tutti gli utenti** selezionare **Aggiungi**.

    ![Pulsante Aggiungi](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. Nella finestra **Utente** seguire questa procedura:

    ![Finestra Utente](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Creare un utente di test di Atlassian Cloud

Per consentire agli utenti di Azure AD di accedere ad Atlassian Cloud, effettuare il provisioning manuale degli account utente in Atlassian Cloud seguendo questa procedura:

1. Nel riquadro **Administration** (Amministrazione) selezionare **Users** (Utenti).

    ![Collegamento Users (Utenti) di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Per creare un utente in Atlassian Cloud, selezionare **Invite user** (Invita l'utente).

    ![Creare un utente di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Nella casella **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente e quindi assegnare l'accesso all'applicazione. 

    ![Creare un utente di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Per inviare un invito di posta elettronica all'utente, selezionare **Invite users** (Invita gli utenti).  
    Un invito di posta elettronica viene inviato all'utente e, dopo avere accettato l'invito, l'utente è attivo nel sistema. 

>[!NOTE] 
>È anche possibile creare gli utenti in blocco selezionando il pulsante **Bulk Create** (Creazione in blocco) nella sezione **Users** (Utenti).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione l'utente Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Atlassian Cloud. A tale scopo, seguire questa procedura:

![Assegnare il ruolo utente][200] 

1. Nel portale di Azure aprire la visualizzazione **Applicazioni**, passare alla visualizzazione della directory, selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco **Applicazioni** selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic su **Aggiungi** e quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra **Utenti e gruppi** fare clic su **Seleziona**.

7. Nella finestra **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro **Atlassian Cloud** nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Atlassian Cloud.
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

