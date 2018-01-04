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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud

Questa esercitazione descrive come integrare Atlassian Cloud con Azure Active Directory (Azure AD).

L'integrazione di Atlassian Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Atlassian Cloud.
- È possibile abilitare gli utenti possano accedere automaticamente (single sign-on) Atlassian cloud con gli account di Azure AD.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per ulteriori informazioni su software come una servizio (SaaS) integrazione dell'applicazione con Azure AD, vedere [novità di accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Atlassian Cloud, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure AD.
- Per abilitare Security Assertion Markup Language (SAML) single sign-on per i prodotti di Atlassian Cloud, è necessario configurare Gestione delle identità. Altre informazioni, vedere [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Quando si testano i passaggi in questa esercitazione, è consigliabile non utilizzare un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

* Aggiunta di Atlassian Cloud dalla raccolta
* Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-atlassian-cloud-from-the-gallery"></a>Aggiungere Atlassian Cloud dalla raccolta
Per configurare l'integrazione di Atlassian Cloud con Azure AD, aggiungere Atlassian Cloud dalla raccolta all'elenco di App SaaS gestite tramite le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com) fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali][2]
    
3. Per aggiungere un'applicazione, selezionare **nuova applicazione**.

    ![Pulsante "Nuova applicazione"][3]

4. Nella casella di ricerca, digitare **Atlassian Cloud**, nell'elenco dei risultati, selezionare **Atlassian Cloud**, quindi selezionare **Aggiungi**.

    ![Atlassian Cloud nell'elenco dei risultati](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con Atlassian Cloud, in base a un utente di test denominato *Britta Simon*.

Per single sign-on a funzionare, Azure AD deve identificare l'utente Atlassian Cloud e la relativa controparte in Azure AD. In altre parole, è necessario stabilire una relazione di collegamento tra un utente AD Azure e l'utente correlata Atlassian cloud.

Per stabilire la relazione di collegamento, assegnare come Atlassian Cloud *Username* lo stesso valore assegnato a Azure AD *nome utente*.

Per configurare e testare Azure AD single sign-on con Atlassian Cloud, è necessario completare i blocchi predefiniti nelle sezioni seguenti.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Atlassian Cloud.

Per configurare Azure AD single sign-on con Atlassian Cloud, eseguire le operazioni seguenti:

1. Nel portale di Azure, nel **Atlassian Cloud** riquadro integrazione dell'applicazione, seleziona **Single sign-on**.

    ![Configurare il collegamento Single Sign-On][4]

2. Nel **Single sign-on** finestra, nel **modalità Single Sign-on** , quindi selezionare **basato su SAML Sign-on**.
 
    ![Finestra del servizio Single sign-on](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Per configurare l'applicazione in modalità avviata da IDP in **Atlassian Cloud dominio e gli URL**, eseguire le operazioni seguenti:

    ![Dominio Atlassian Cloud e informazioni URL single sign-on](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. Nel **identificatore** digitare  **`https://auth.atlassian.com/saml/<unique ID>`** .
    
    b. Nel **URL di risposta** digitare  **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`** .

    c. Nel **stato inoltro** casella, digitare un URL con la sintassi seguente:  **`https://<instancename>.atlassian.net`** .

4. Per configurare l'applicazione in modalità SP initiated, selezionare il **Mostra URL impostazioni avanzate** quindi il **URL di accesso** casella, digitare un URL con la sintassi seguente:  **`https://<instancename>.atlassian.net`**  .

    ![Dominio Atlassian Cloud e informazioni URL single sign-on](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornarli con identificatore effettivo, l'URL di risposta e valori di URL di accesso. È possibile ottenere i valori reali dalla schermata Configurazione SAML del Cloud Atlassian. I valori viene descritto più avanti nell'esercitazione.

5. In **certificato di firma SAML**selezionare **Certificate(Base64)**e quindi salvare il file del certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. L'applicazione Atlassian Cloud prevede di trovare le asserzioni SAML in un formato specifico, è necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del Token SAML. 

    Per impostazione predefinita, il **identificatore utente** valore viene mappato a User. Modificare questo valore per eseguire il mapping a user.mail. È anche possibile scegliere qualsiasi altro valore appropriato in base alla configurazione dell'organizzazione, ma nella maggior parte dei casi, posta elettronica dovrebbe funzionare.

    ![Collegamento di download del certificato](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png) 

7. Selezionare **Salva**.

    ![Configura accesso single sign-on pulsante Salva](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. Per aprire il **Configura sign-on** finestra, nel **configurazione Cloud Atlassian** selezionare **configurare Atlassian Cloud**. 

9. Nel **riferimento rapido** sezione, copiare il **ID entità SAML** e **SAML Single Sign-On Service URL**. 

    ![Configurazione del Cloud di Atlassian](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

10. Per ottenere SSO configurato per l'applicazione, accedere al portale di Atlassian con credenziali di amministratore.

11. Passare a **amministrazione sito Atlassian** > **organizzazioni e sicurezza**. Se non già stato fatto, creare e assegnare un nome dell'organizzazione e quindi, nel riquadro a sinistra, selezionare **domini**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

12. Selezionare la modalità che si desidera verificare il dominio: **DNS** o **HTTPS**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

13. Per la verifica di DNS, nel **domini** finestra, seleziona il **DNS** scheda e quindi eseguire le operazioni seguenti:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Per copiare il valore per il record di testo (record TXT), selezionare **copia**.

    b. Per aggiungere un record, passare alla pagina delle impostazioni nel sistema DNS.

    c. Selezionare l'opzione per aggiungere un nuovo record e quindi incollare il valore copiato nel **domini** finestra per il **valore** campo. Il record DNS può indicare anche come **risposta** o **descrizione**.

    d. Il record DNS può includere anche i campi seguenti:
    
    * Nel **tipo di Record** immettere **TXT**.
    * Nel **nomeAlias/Host/** , lasciare il valore predefinito (@ o vuoto).
    * Nel **durata (TTL)** immettere **86400**.
    
    e.  Salvare il record.

14. Restituito per il **domini** finestra Amministrazione dell'organizzazione e quindi selezionare **verifica dominio**. Nel **dominio** , digitare il nome di dominio e quindi selezionare **verifica dominio**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)  

    > [!NOTE]
    > Poiché può richiedere fino a 72 ore rendere effettive le modifiche di record TXT, si sapranno immediatamente se è stata completata la verifica del dominio. Per visualizzare lo stato di verifica, controllare il **domini** finestra subito dopo aver completato questa procedura. Verrà visualizzato lo stato aggiornato come *verificato*, come illustrato nella figura seguente:
    > 
    > ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)
    > 
    > 

15. Per la verifica di HTTPS, nel **domini** finestra, seleziona il **HTTPS** scheda e quindi eseguire le operazioni seguenti:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a. Per scaricare il file HTML, selezionare **scaricare il file**.

    b. Caricare il file HTML nella directory radice del dominio.

16. Restituito per il **domini** pagina Amministrazione organizzazione e selezionare **verifica dominio**. Nel **verifica dominio** finestra, nel **dominio** , digitare il **nome di dominio**e quindi selezionare **verifica dominio**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

17. Se il processo di verifica è possibile individuare il file caricato nella directory principale, lo stato del dominio viene aggiornato a *verificato*, come illustrato di seguito:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Per ulteriori informazioni, vedere [verifica del dominio Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

18. Nel riquadro a sinistra, selezionare **SAML single sign-on**. Se non già stato fatto, eseguire la sottoscrizione a Atlassian Identity Manager.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

19. Nel **configurazione SAML aggiungere** finestra, eseguire le operazioni seguenti:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Nel **provider di identità, ID entità** incollare l'ID entità SAML copiato dal portale di Azure.

    b. Nel **Identity provider URL SSO** incollare l'URL servizio single sign-on SAML copiato dal portale di Azure.

    c. Aprire il certificato scaricato dal portale di Azure in un file con estensione txt, copiare il valore (senza il *certificato iniziare* e *certificato finale* righe) e quindi incollarlo nella **X509 pubblico certificato** casella.
    
    d. Selezionare **salvare configurazione**.
     
20. Per assicurarsi che siano impostati degli URL corretti, aggiornare le impostazioni di Azure AD effettuando le seguenti operazioni:
  
    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Nella finestra SAML, copiare il **ID identità SP** e quindi, nel portale di Azure nel Atlassian Cloud **dominio e gli URL**, incollarlo nel **identificatore** casella.
    
    b. Nella finestra SAML, copiare il **URL del servizio Consumer di asserzione SP** e quindi, nel portale di Azure nel Atlassian Cloud **dominio e gli URL**, incollarlo nel **URL di risposta** casella.  
        L'URL di accesso è l'URL del tenant del Atlassian Cloud. 

    > [!NOTE]
    > Se si è un cliente esistente, dopo aver aggiornato il **ID identità SP** e **URL del servizio Consumer di asserzione SP** valori nel portale di Azure, selezionare **Sì, Aggiorna configurazione**. Se si è un nuovo cliente, è possibile ignorare questo passaggio. 
    
21. Nel portale di Azure, selezionare **salvare**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Come si sta configurando l'app, è possibile leggere una versione concisa delle istruzioni precedente nel [portale di Azure](https://portal.azure.com). Dopo aver aggiunto questa app dal **Active Directory** > **applicazioni aziendali** sezione, selezionare il **Single Sign-On** scheda e quindi accedere incorporato documentazione nel **configurazione** sezione nella parte inferiore della finestra. Per altre informazioni, vedere la [documentazione incorporata su Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione si crea un utente di test Britta Simon nel portale di Azure seguendo questa procedura:

   ![Creare un utente test di Azure AD][100]

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, selezionare **Utenti e gruppi** > **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Nel **tutti gli utenti** selezionare **Aggiungi**.

    ![Pulsante Aggiungi](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. Nel **utente** finestra, eseguire le operazioni seguenti:

    ![Finestra dell'utente](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Creare un utente di test di Atlassian Cloud

Per consentire agli utenti di Azure AD di accedere al Atlassian Cloud, eseguire il provisioning di account utente manualmente nel Cloud di Atlassian effettuando le operazioni seguenti:

1. Nel **amministrazione** riquadro, selezionare **utenti**.

    ![Il collegamento di Atlassian gli utenti del Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Per creare un utente nel Atlassian Cloud, selezionare **invito utente**.

    ![Creare un utente Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Nel **indirizzo di posta elettronica** casella, immettere l'indirizzo di posta elettronica dell'utente e quindi assegnare l'accesso all'applicazione. 

    ![Creare un utente Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Per inviare un invito tramite posta elettronica all'utente, selezionare **invitare gli utenti**.  
    Un invito tramite posta elettronica viene inviato all'utente e, dopo aver accettato l'invito, l'utente è attivo nel sistema. 

>[!NOTE] 
>È possibile anche eseguire bulk-creare utenti selezionando il **Bulk creare** pulsante il **utenti** sezione.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare utente Britta Simon per utilizzare single sign-on Azure concedendo l'accesso al Atlassian Cloud. A tale scopo, seguire questa procedura:

![Assegnare il ruolo utente][200] 

1. Nel portale di Azure, aprire il **applicazioni** consente di visualizzare, passare alla visualizzazione directory e quindi selezionare **applicazioni aziendali** > **tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nel **applicazioni** elenco, selezionare **Atlassian Cloud**.

    ![Collegamento di Atlassian Cloud nell'elenco delle applicazioni](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic su **Aggiungi** e quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nel **utenti e gruppi** finestra, nel **utenti** elenco, selezionare **Britta Simon**.

6. Nel **utenti e gruppi** selezionare **selezionare**.

7. Nel **Aggiungi** selezionare **assegnare**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il **Atlassian Cloud** riquadro nel Pannello di accesso, è necessario eseguire l'accesso automaticamente all'applicazione Atlassian Cloud.
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

