<properties
	pageTitle="Esercitazione: Integrazione di Azure Active Directory con Qlik Sense Enterprise | Microsoft Azure"
	description="Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Qlik Sense Enterprise."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="jeedes"/>


# Esercitazione: Integrazione di Azure Active Directory con Qlik Sense Enterprise

Questa esercitazione descrive come integrare Qlik Sense Enterprise con Azure Active Directory (Azure AD).

L'integrazione di Qlik Sense Enterprise con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Qlik Sense Enterprise
- È possibile abilitare gli utenti per l'accesso automatico a Qlik Sense Enterprise (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti

Per configurare l'integrazione di Azure AD con Qlik Sense Enterprise, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Qlik Sense Enterprise abilitata per l'accesso Single Sign-On


> [AZURE.NOTE] Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Qlik Sense Enterprise dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## Aggiunta di Qlik Sense Enterprise dalla raccolta
Per configurare l'integrazione di Qlik Sense Enterprise in Azure AD, è necessario aggiungere Qlik Sense Enterprise dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Qlik Sense Enterprise dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

	![Applicazioni][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

	![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

	![Applicazioni][4]

6. Nella casella di ricerca digitare **Qlik Sense Enterprise**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. Nel riquadro dei risultati selezionare **Qlik Sense Enterprise** e quindi fare clic su **Completa** per aggiungere l'applicazione.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Qlik Sense Enterprise con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Qlik Sense Enterprise che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Qlik Sense Enterprise.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Qlik Sense Enterprise.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Qlik Sense Enterprise, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)**: per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Qlik Sense Enterprise](#creating-a-qliksense-enterprise-test-user)**: per avere una controparte di Britta Simon in Qlik Sense Enterprise collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale classico e viene configurato l'accesso Single Sign-On nell'applicazione Qlik Sense Enterprise.


**Per configurare l'accesso Single Sign-On di Azure AD con Qlik Sense Enterprise, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Qlik Sense Enterprise** nel portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
	 
	![Configura accesso Single Sign-On][6]

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Qlik Sense Enterprise** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png)

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Qlik Sense Enterprise adottando il modello seguente: **https://\<nome host completo Qlik Sense>:443/<prefisso proxy virtuale>/samlauthn/**.
	
	> [AZURE.NOTE] Si noti la barra finale nell'URI. È obbligatoria.

	b. Fare clic su **Avanti**.
 
4. Nella pagina **Configura accesso Single Sign-On in Qlik Sense Enterprise** seguire questa procedura:

	![Configura accesso Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    a. Fare clic su **Scarica metadati** e quindi salvare il file nel computer. Sarà necessario modificare questo file di metadati prima del caricamento sul server Qlik Sense.

    b. Fare clic su **Next**.

5. Preparare il file XML di metadati della federazione per il caricamento sul server Qlik Sense.

	> [AZURE.NOTE] Prima di caricare i metadati IdP sul server Qlik Sense, è necessario modificare il file rimuovendo informazioni in modo da garantire il corretto funzionamento tra Azure AD e il server Qlik Sense.

	![Qlik Sense][qs24]

	a. Aprire il file FederationMetaData.xml scaricato da Azure in un editor di testo.

	b. Cercare il valore **RoleDescriptor**. Saranno presenti quattro voci (due coppie di tag di elemento di apertura e di chiusura).

	c. Eliminare dal file i tag RoleDescriptor e tutte le informazioni in essi racchiuse.

	d. Salvare il file e tenerlo a disposizione per usarlo più avanti in questo documento.

6. Passare a Qlik Sense Qlik Management Console (QMC) come utente autorizzato a creare configurazioni di proxy virtuali.

7. In QMC fare clic sulla voce di menu Virtual Proxy (Proxy virtuale).

	![Qlik Sense][qs6]

8. Nella parte inferiore della schermata fare clic sul pulsante Create new (Crea nuovo).

	![Qlik Sense][qs7]

9. Viene visualizzata la schermata per la modifica del proxy virtuale. Sul lato destro della schermata è disponibile un menu per la visualizzazione delle opzioni di configurazione.

	![Qlik Sense][qs9]

10. Dopo aver selezionato l'opzione di menu Identification (Identificazione), immettere le informazioni di identificazione per la configurazione del proxy virtuale di Azure.

	![Qlik Sense][qs8]
	
	a. Il campo Description (Descrizione) contiene un nome descrittivo per la configurazione del proxy virtuale. Immettere un valore per la descrizione.
	
	b. Il campo Prefix (Prefisso) identifica l'endpoint proxy virtuale per connettersi a Qlik Sense con l'accesso Single Sign-On di Azure AD. Immettere un nome di prefisso univoco per il proxy virtuale.

	c. Session inactivity timeout (minutes) (Timeout di inattività sessione - minuti) indica il timeout per le connessioni tramite il proxy virtuale.

	d. Session cookie header name (Nome intestazione cookie sessione) contiene il nome del cookie in cui è archiviato l'identificatore della sessione di Qlik Sense ricevuto da un utente dopo il completamento dell'autenticazione. Il nome deve essere univoco.

11. Fare clic sull'opzione di menu Authentication (Autenticazione) per renderla visibile. Verrà visualizzata la schermata Authentication (Autenticazione).

	![Qlik Sense][qs10]

	a. L'elenco a discesa **Anonymous access mode** (Modalità accesso anonimo) determina se gli utenti anonimi potranno accedere a Qlik Sense tramite il proxy virtuale. L'opzione predefinita è No anonymous user (Nessun utente anonimo).

	b. L'elenco a discesa **Authentication method** (Metodo di autenticazione) determina lo schema di autenticazione che verrà usato dal proxy virtuale. Selezionare SAML nell'elenco a discesa. Verranno di conseguenza visualizzate altre opzioni.

	c. Nel campo **SAML host URI** (URI host SAML) specificare il nome host che verrà immesso dagli utenti per accedere a Qlik Sense tramite questo proxy virtuale SAML. Il nome host è l'URI del server Qlik Sense.

	d. In **SAML entity ID** (ID entità SAML) immettere lo stesso valore inserito nel campo SAML host URI (URI host SAML).

	e. **SAML IdP metadata** (Metadati IdP SAML) è il campo modificato nella precedente sezione relativa alla **modifica dei metadati della federazione della configurazione di Azure AD**. **Prima di caricare i metadati IdP, è necessario modificare il file** rimuovendo informazioni in modo da garantire il corretto funzionamento tra Azure AD e il server Qlik Sense. **Se il file non è ancora stato modificato, vedere le istruzioni riportate sopra.** Se il file è stato modificato, fare clic sul pulsante Browse (Sfoglia) e selezionare il file di metadati modificato per caricarlo nella configurazione del proxy virtuale.

	f. Immettere il nome dell'attributo o il riferimento dello schema per l'attributo SAML che rappresenta l'**ID utente** che Azure AD invierà al server Qlik Sense. Le informazioni relative al riferimento dello schema sono disponibili nelle schermate dell'app in Azure in seguito alla configurazione. Per usare il nome dell'attributo, immettere http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

	g. Immettere il valore della **directory utente** che verrà associata agli utenti quando eseguiranno l'autenticazione al server Qlik Sense tramite Azure AD. I valori hardcoded devono essere racchiusi tra **parentesi quadre**. Per usare un attributo inviato nell'asserzione SAML di Azure AD, immettere il nome dell'attributo in questa casella di testo **senza** parentesi quadre.

	h. **SAML signing algorithm** (Algoritmo di firma SAML) imposta la firma del certificato del provider di servizi (in questo caso, il server Qlik Sense) per la configurazione del proxy virtuale. Se il server Qlik Sense usa un certificato attendibile generato con Microsoft Enhanced RSA and AES Cryptographic Provider, modificare l'algoritmo di firma SAML impostandolo su **SHA-256**.

	i. La sezione SAML attribute mapping (Mapping attributi SAML) consente di inviare a Qlik Sense attributi aggiuntivi, come i gruppi, da usare nelle regole di sicurezza.

12. Fare clic sull'opzione di menu Load balancing (Bilanciamento del carico) per renderla visibile. Verrà visualizzata la schermata Load balancing (Bilanciamento del carico).

	![Qlik Sense][qs11]

13. Fare clic sul pulsante Add new server node (Aggiungi nuovo nodo server), selezionare uno o più nodi motore a cui Qlik Sense invierà le sessioni ai fini del bilanciamento del carico e fare clic su Add (Aggiungi).

	![Qlik Sense][qs12]

14. Fare clic sull'opzione di menu Advanced (Avanzate) per renderla visibile. Verrà visualizzata la schermata Advanced (Avanzate).

	![Qlik Sense][qs13]

	a. Host white list (Elenco host consentiti) identifica i nomi host accettati per la connessione al server Qlik Sense. **Immettere il nome host che gli utenti specificheranno per la connessione al server Qlik Sense.** Il nome host è lo stesso valore specificato in SAML host URI (URI host SAML) senza https://.

15. Fare clic sul pulsante Apply (Applica).

	![Qlik Sense][qs14]

16. Fare clic su OK per accettare il messaggio di avviso che indica che i proxy collegati al proxy virtuale verranno riavviati.

	![Qlik Sense][qs15]

17. Sul lato destro della schermata verrà visualizzato il menu Associated items (Elementi associati). Fare clic sull'opzione di menu Proxies (Proxy).

	![Qlik Sense][qs16]

18. Verrà visualizza la schermata relativa ai proxy. Fare clic sul pulsante Link (Collega) in basso per collegare un proxy al proxy virtuale.

	![Qlik Sense][qs17]

19. Selezionare il nodo proxy che supporterà la connessione proxy virtuale e fare clic sul pulsante Link (Collega). Al termine del collegamento, il proxy verrà incluso nell'elenco dei proxy associati.

	![Qlik Sense][qs18] ![Qlik Sense][qs19]

20. Dopo circa 5-10 secondi verrà visualizzato il messaggio per l'aggiornamento di QMC. Fare clic sul pulsante Refresh QMC (Aggiorna QMC).

	![Qlik Sense][qs20]

21. Dopo l'aggiornamento di QMC, fare clic sulla voce di menu Virtual proxies (Proxy virtuali). La nuova voce del proxy virtuale SAML sarà elencata nella tabella visualizzata. Fare clic sulla voce del proxy virtuale.

	![Qlik Sense][qs51]

22. Nella parte inferiore della schermata verrà attivato il pulsante Download SP metadata (Scarica metadati SP). Fare clic sul pulsante Download SP metadata (Scarica metadati SP) per salvare i metadati in un file.

	![Qlik Sense][qs52]

23. Aprire il file di metadati SP. Osservare le voci **entityID** e **AssertionConsumerService**. Questi valori sono uguali ai valori **Identificatore** e **URL di accesso** nella configurazione dell'applicazione di Azure AD. Se non corrispondono, è consigliabile modificarli nella configurazione guidata dell'app di Azure AD.

	![Qlik Sense][qs53]

24. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
	
	![Accesso Single Sign-On di Azure AD][10]

25. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
 
	![Accesso Single Sign-On di Azure AD][11]


### Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.


![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png)

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

5. Nella pagina **Informazioni sull'utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png)

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Next**.

6.  Nella pagina **Profilo utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png)

    a. Nella casella di testo **Nome** digitare **Britta**.

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo**, selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png)

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

	![Creazione di un utente test di Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png)

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.


### Creazione di un utente test di Qlik Sense Enterprise

In questa sezione viene creato un utente chiamato Britta Simon in Qlik Sense Enterprise. Collaborare con il team di supporto di Qlik Sense Enterprise per aggiungere gli utenti nella piattaforma Qlik Sense Enterprise.


### Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Qlik Sense Enterprise.

![Assegna utente][200]

**Per assegnare Britta Simon a Qlik Sense Enterprise, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

	![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Qlik Sense Enterprise**.

	![Configura accesso Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png)

3. Scegliere **Utenti** dal menu in alto.

	![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna** sulla barra degli strumenti in basso.

	![Assegna utente][205]


## Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Qlik Sense Enterprise nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Qlik Sense Enterprise.


## Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

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
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

<!---HONumber=AcomDC_0907_2016-->