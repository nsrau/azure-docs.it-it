---
title: 'Esercitazione: Integrazione di Azure Active Directory con Qlik Sense Enterprise | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 3fd6bcce24ca4a1170bd8a9301dff1a02d638773
ms.contentlocale: it-it
ms.lasthandoff: 07/27/2017

---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con Qlik Sense Enterprise

Questa esercitazione descrive come integrare Qlik Sense Enterprise con Azure Active Directory (Azure AD).

L'integrazione di Qlik Sense Enterprise con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Qlik Sense Enterprise.
- È possibile abilitare gli utenti per l'accesso automatico a Qlik Sense Enterprise (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Qlik Sense Enterprise, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Qlik Sense Enterprise abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si ha un ambiente di valutazione di Azure AD, è possibile ottenere una versione di valutazione di un mese qui: [versione di valutazione](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Qlik Sense Enterprise dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Aggiunta di Qlik Sense Enterprise dalla raccolta
Per configurare l'integrazione di Qlik Sense Enterprise in Azure AD, è necessario aggiungere Qlik Sense Enterprise dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Qlik Sense Enterprise dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Qlik Sense Enterprise**, selezionare **Qlik Sense Enterprise** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Qlik Sense Enterprise nell'elenco risultati](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Qlik Sense Enterprise con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Qlik Sense Enterprise che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Qlik Sense Enterprise.

Per stabilire la relazione di collegamento, in Qlik Sense Enterprise assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Qlik Sense Enterprise, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Qlik Sense Enterprise](#create-a-qlik-sense-enterprise-test-user)**: per avere una controparte di Britta Simon in Qlik Sense Enterprise collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Qlik Sense Enterprise.

**Per configurare l'accesso Single Sign-On di Azure AD con Qlik Sense Enterprise, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Qlik Sense Enterprise** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Nella sezione **URL e dominio Qlik Sense Enterprise** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Qlik Sense Enterprise](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`.
    
    > [!NOTE]
    > Si noti la barra finale nell'URI. È obbligatoria.
    
    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con identificatore e URL di accesso effettivi, illustrati più avanti in questa esercitazione, oppure contattare il [team di supporto clienti di Qlik Sense Enterprise](https://www.qlik.com/us/services/support) per ottenere questi valori. 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Preparare il file XML di metadati della federazione per il caricamento sul server Qlik Sense.
   
    > [!NOTE]
    > Prima di caricare i metadati IdP sul server Qlik Sense, è necessario modificare il file rimuovendo informazioni in modo da garantire il corretto funzionamento tra Azure AD e il server Qlik Sense.
    
    ![Qlik Sense][qs24]
   
    a. Aprire il file FederationMetaData.xml scaricato dal portale di Azure in un editor di testo.
   
    b. Cercare il valore **RoleDescriptor**.  Ci sono quattro voci (due coppie di tag di elementi di apertura e di chiusura).
   
    c. Eliminare dal file i tag RoleDescriptor e tutte le informazioni in essi racchiuse.
   
    d. Salvare il file e tenerlo a disposizione per usarlo più avanti in questo documento.

7. Passare a Qlik Sense Qlik Management Console (QMC) come utente autorizzato a creare configurazioni di proxy virtuali.

8. In QMC fare clic sulla voce di menu **Virtual Proxies** (Proxy virtuali).
   
    ![Qlik Sense][qs6] 

9. Nella parte inferiore dello schermo fare clic sul pulsante **Create new** (Crea nuovo).
   
    ![Qlik Sense][qs7]

10. Viene visualizzata la schermata per la modifica del proxy virtuale.  Sul lato destro della schermata è disponibile un menu per la visualizzazione delle opzioni di configurazione.
   
    ![Qlik Sense][qs9]

11. Dopo aver selezionato l'opzione di menu Identification (Identificazione), immettere le informazioni di identificazione per la configurazione del proxy virtuale di Azure.
    
    ![Qlik Sense][qs8]  
    
    a. Il campo **Description** (Descrizione) contiene un nome descrittivo per la configurazione del proxy virtuale.  Immettere un valore per la descrizione.
    
    b. Il campo **Prefix** (Prefisso) identifica l'endpoint proxy virtuale per la connessione a Qlik Sense con l'accesso Single Sign-On di Azure AD.  Immettere un nome di prefisso univoco per il proxy virtuale.
    
    c. **Session inactivity timeout (minutes)** (Timeout di inattività sessione - minuti) indica il timeout per le connessioni tramite il proxy virtuale.
    
    d. **Session cookie header name** (Nome intestazione cookie sessione) è il nome del cookie in cui è archiviato l'identificatore della sessione di Qlik Sense ricevuto da un utente dopo il completamento dell'autenticazione.  Il nome deve essere univoco.

12. Fare clic sull'opzione di menu Authentication (Autenticazione) per renderla visibile.  Verrà visualizzata la schermata Authentication (Autenticazione).
    
    ![Qlik Sense][qs10]
    
    a. L'elenco a discesa **Anonymous access mode** (Modalità accesso anonimo) determina se gli utenti anonimi potranno accedere a Qlik Sense tramite il proxy virtuale.  L'opzione predefinita è No anonymous user (Nessun utente anonimo).
    
    b. L'elenco a discesa **Authentication method** (Metodo di autenticazione) determina lo schema di autenticazione che verrà usato dal proxy virtuale.  Selezionare SAML nell'elenco a discesa.  Verranno di conseguenza visualizzate altre opzioni.
    
    c. Nel campo **SAML host URI** (URI host SAML) specificare il nome host che verrà immesso dagli utenti per accedere a Qlik Sense tramite questo proxy virtuale SAML.  Il nome host è l'URI del server Qlik Sense.
    
    d. In **SAML entity ID**(ID entità SAML) immettere lo stesso valore inserito nel campo SAML host URI (URI host SAML).
    
    e. **SAML IdP metadata** (Metadati IdP SAML) è il campo modificato nella precedente sezione relativa alla **modifica dei metadati della federazione della configurazione di Azure AD**.  **Prima di caricare i metadati IdP, è necessario modificare il file** rimuovendo informazioni in modo da garantire il corretto funzionamento tra Azure AD e il server Qlik Sense.  **Se il file non è ancora stato modificato, vedere le istruzioni riportate sopra.**  Se il file è stato modificato, fare clic sul pulsante Browse (Sfoglia) e selezionare il file di metadati modificato per caricarlo nella configurazione del proxy virtuale.
    
    f. Immettere il nome dell'attributo o il riferimento dello schema per l'attributo SAML che rappresenta l'**ID utente** inviato da Azure AD al server Qlik Sense.  Le informazioni relative al riferimento dello schema sono disponibili nelle schermate dell'app in Azure in seguito alla configurazione.  Per usare il nome di attributo, `enter http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Immettere il valore della **directory utente** che verrà associata agli utenti quando eseguiranno l'autenticazione al server Qlik Sense tramite Azure AD.  I valori hardcoded devono essere racchiusi tra **parentesi quadre []**.  Per usare un attributo inviato nell'asserzione SAML di Azure AD, immettere il nome dell'attributo in questa casella di testo **senza** parentesi quadre.
    
    h. **SAML signing algorithm** (Algoritmo di firma SAML) imposta la firma del certificato del provider di servizi (in questo caso, il server Qlik Sense) per la configurazione del proxy virtuale.  Se il server Qlik Sense usa un certificato attendibile generato con Microsoft Enhanced RSA and AES Cryptographic Provider, modificare l'algoritmo di firma SAML impostandolo su **SHA-256**.
    
    i. La sezione SAML attribute mapping (Mapping attributi SAML) consente di inviare a Qlik Sense attributi aggiuntivi, come i gruppi, da usare nelle regole di sicurezza.

13. Fare clic sull'opzione di menu **LOAD BALANCING** (BILANCIAMENTO DEL CARICO) per renderla visibile.  Verrà visualizzata la schermata Load balancing (Bilanciamento del carico).
    
    ![Qlik Sense][qs11]

14. Fare clic sul pulsante **Add new server node** (Aggiungi nuovo nodo server), selezionare uno o più nodi motore a cui Qlik Sense invierà le sessioni ai fini del bilanciamento del carico e fare clic su **Add** (Aggiungi).
    
    ![Qlik Sense][qs12]

15. Fare clic sull'opzione di menu Advanced (Avanzate) per renderla visibile. Verrà visualizzata la schermata Advanced (Avanzate).
    
    ![Qlik Sense][qs13]
    
    Host white list (Elenco host consentiti) identifica i nomi host accettati per la connessione al server Qlik Sense.  **Immettere il nome host che gli utenti specificheranno per la connessione al server Qlik Sense.** Il nome host è lo stesso valore specificato in SAML host URI (URI host SAML) senza https://.

16. Fare clic sul pulsante **Apply** (Applica).
    
    ![Qlik Sense][qs14]

17. Fare clic su OK per accettare il messaggio di avviso che indica che i proxy collegati al proxy virtuale verranno riavviati.
    
    ![Qlik Sense][qs15]

18. Sul lato destro della schermata verrà visualizzato il menu Associated items (Elementi associati).  Fare clic sull'opzione di menu **Proxies** (Proxy).
    
    ![Qlik Sense][qs16]

19. Verrà visualizza la schermata relativa ai proxy.  Fare clic sul pulsante **Link** (Collega) in basso per collegare un proxy al proxy virtuale.
    
    ![Qlik Sense][qs17]

20. Selezionare il nodo proxy che supporterà la connessione proxy virtuale e fare clic sul pulsante **Link** (Collega).  Al termine del collegamento, il proxy verrà incluso nell'elenco dei proxy associati.
    
    ![Qlik Sense][qs18]
  
    ![Qlik Sense][qs19]

21. Dopo circa 5-10 secondi verrà visualizzato il messaggio per l'aggiornamento di QMC.  Fare clic sul pulsante **Refresh QMC** (Aggiorna QMC).
    
    ![Qlik Sense][qs20]

22. Dopo l'aggiornamento di QMC, fare clic sulla voce di menu **Virtual proxies** (Proxy virtuali). La nuova voce del proxy virtuale SAML sarà elencata nella tabella visualizzata.  Fare clic sulla voce del proxy virtuale.
    
    ![Qlik Sense][qs51]

23. Nella parte inferiore della schermata verrà attivato il pulsante Download SP metadata (Scarica metadati SP).  Fare clic sul pulsante **Download SP metadata** (Scarica metadati SP) per salvare i metadati in un file.
    
    ![Qlik Sense][qs52]

24. Aprire il file di metadati SP.  Osservare le voci **entityID** e **AssertionConsumerService**.  Questi valori sono uguali ai valori **Identificatore** e **URL di accesso** nella configurazione dell'applicazione di Azure AD. Incollare questi valori nella sezione **URL e dominio Qlik Sense Enterprise** nella configurazione dell'applicazione di Azure AD se i valori non corrispondono. Sarà quindi necessario sostituire i valori nella configurazione guidata dell'app Azure AD.
    
    ![Qlik Sense][qs53]

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

   ![Pulsante Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

   ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente**, fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

   ![Pulsante Aggiungi](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

   ![Finestra di dialogo Utente](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. Nella casella **Nome** digitare **BrittaSimon**.

   b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

   c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

   d. Fare clic su **Crea**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Creare un utente di test di Qlik Sense Enterprise

In questa sezione viene creato un utente chiamato Britta Simon in Qlik Sense Enterprise. Collaborare con il [team di supporto clienti di Qlik Sense Enterprise](https://www.qlik.com/us/services/support) per aggiungere gli utenti nella piattaforma Qlik Sense Enterprise. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Qlik Sense Enterprise.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Qlik Sense Enterprise, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Qlik Sense Enterprise**.

    ![Collegamento di Qlik Sense Enterprise nell'elenco delle applicazioni](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Qlik Sense Enterprise nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Qlik Sense Enterprise. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png


