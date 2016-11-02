<properties
    pageTitle="Gestione del provisioning degli utenti per app aziendali nell'anteprima di Azure Active Directory | Microsoft Azure"
    description="Informazioni su come gestire il provisioning degli account utente per app aziendali usando l'anteprima di Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>


#<a name="preview:-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Anteprima: gestione del provisioning degli account utenti per app aziendali nel nuovo portale di Azure

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per gestire il provisioning e il deprovisioning automatici degli account per le applicazioni che li supportano, in particolare per le applicazioni aggiunte dalla categoria "In primo piano" della [raccolta di applicazioni di Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Questa esperienza di gestione nel portale di Azure è attualmente disponibile in anteprima pubblica e questo articolo illustra le nuove funzionalità, oltre ad alcune limitazioni temporanee applicate nel periodo di anteprima. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md)

Per altre informazioni sul provisioning automatico degli account utente e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

##<a name="finding-your-apps-in-the-new-portal"></a>Individuazione delle app nel nuovo portale

A partire dal mese di settembre 2016, tutte le applicazioni configurate per l'accesso Single Sign-On in una directory da un amministratore di directory che usa la [raccolta di applicazioni di Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) nel [portale di Azure classico](https://manage.windowsazure.com) possono essere visualizzate e gestite nel nuovo portale di Azure.

Queste applicazioni sono disponibili nella sezione **Applicazioni aziendali** del nuovo portale di Azure, a cui è possibile accedere tramite il menu **More Services** (Altri servizi) nell'area di navigazione a sinistra. Le app aziendali sono app distribuite e usate da utenti all'interno dell'organizzazione.

![Pannello Applicazioni aziendali][0]

Se si seleziona il collegamento **Tutte le applicazioni** a sinistra, viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta. Se si seleziona un'app, viene caricato il pannello delle risorse per tale app, in cui è possibile visualizzare i report per l'app e gestire diverse impostazioni.

Le impostazioni del provisioning degli account utente possono essere gestite selezionando **Provisioning** a sinistra.

![Pannello Risorsa applicazione][1]


##<a name="provisioning-modes"></a>Modalità di provisioning

Il pannello **Provisioning** inizia con un menu **Modalità**, che mostra le modalità di provisioning supportate per un'applicazione aziendale e ne consente la configurazione. Le opzioni disponibili includono:

* **Automatico** : questa opzione viene visualizzata se Azure AD supporta il provisioning e/o il deprovisioning automatico basato su API di account utente in questa applicazione. Se si seleziona questa modalità, viene visualizzata un'interfaccia che fornisce agli amministratori informazioni dettagliate sulla configurazione di Azure AD per la connessione all'API di gestione degli utenti dell'applicazione, sulla creazione di mapping di account e flussi di lavoro che definiscono il modo in cui i dati relativi agli account utente devono essere trasmessi tra Azure AD e l'app e sulla gestione del servizio di provisioning di Azure AD.

* **Manuale** : questa opzione viene visualizzata se Azure AD non supporta il provisioning automatico di account utente in questa applicazione. Questa opzione indica che i record relativi agli account utente archiviati nell'applicazione devono essere gestiti usando un processo esterno, in base alle funzionalità di gestione degli utenti e di provisioning fornite dall'applicazione, che possono includere il provisioning just-in-time SAML.


##<a name="configuring-automatic-user-account-provisioning"></a>Configurazione del provisioning automatico degli account utente

Se si seleziona l'opzione **Automatico** , viene visualizzata una schermata suddivisa in quattro sezioni:

###<a name="admin-credentials"></a>Credenziali di amministratore
In questa sezione vengono immesse le credenziali necessarie ad Azure AD per la connessione all'API di gestione degli utenti dell'applicazione. L'input necessario dipende dall'applicazione. Per informazioni sui tipi di credenziali e sui requisiti per applicazioni specifiche, vedere l' [esercitazione sulla configurazione per l'applicazione specifica](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

Se si seleziona il pulsante **Test connessione** , è possibile testare le credenziali mediante un tentativo di connessione di Azure AD all'app di provisioning dell'app con le credenziali fornite.

###<a name="mappings"></a>Mapping
In questa sezione gli amministratori possono visualizzare e modificare gli attributi utente trasmessi tra Azure AD e l'applicazione di destinazione durante il provisioning o l'aggiornamento degli account utente.

Esiste un set preconfigurato di mapping tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, quali Gruppi o Contatti. Se si seleziona uno di questi mapping nella tabella, viene visualizzato l'editor di mapping, che consente di visualizzare e personalizzare i mapping.

![Pannello Risorsa applicazione][2]

Ecco le personalizzazioni supportate durante la prima anteprima:

* Abilitazione e disabilitazione dei mapping per oggetti specifici, ad esempio l'oggetto utente di Azure AD all'oggetto utente dell'app SaaS.

* Modifica degli attributi che devono essere trasmessi dall'oggetto utente di Azure AD all'oggetto utente dell'app. Per altre informazioni sul mapping degli attributi, vedere [Informazioni sui tipi di mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).

* Filtro delle azioni di provisioning che devono essere eseguite da Azure AD sull'applicazione di destinazione. Questa è una nuova funzionalità del portale di Azure. Invece di fare in modo che Azure AD esegua la sincronizzazione completa degli oggetti, è possibile limitare le azioni eseguite. Se si seleziona solo **Aggiorna**, ad esempio, Azure AD aggiorna solo gli account utente esistenti in un'applicazione e non crea nuovi account utente. Se si seleziona solo **Crea**, Azure crea solo nuovi account utente, ma non aggiorna gli account utente esistenti. Questa funzionalità consente agli amministratori di creare diversi mapping per i flussi di lavoro di creazione e aggiornamento degli account. La funzionalità completa per la creazione di più mapping per ogni app sarà disponibile più avanti nel periodo di anteprima.

###<a name="settings"></a>Impostazioni
Questa sezione consente agli amministratori di avviare e arrestare il servizio di provisioning di Azure AD per l'applicazione selezionata, oltre a cancellare facoltativamente la cache di provisioning e riavviare il servizio.

Se il provisioning viene abilitato per la prima volta per un'applicazione, attivare il servizio impostando **Stato del provisioning** su **Sì**. Il servizio di provisioning di Azure AD esegue quindi una sincronizzazione iniziale, durante la quale legge gli utenti assegnati nella sezione **Utenti e gruppi**, esegue query nell'applicazione di destinazione alla ricerca di tali utenti e quindi esegue le azioni di provisioning definite nella sezione **Mapping** di Azure AD. Durante questo processo il servizio di provisioning archivia i dati memorizzati nella cache relativi agli account utente gestiti, in modo che gli account non gestiti all'interno dell'applicazione di destinazione non inclusi nell'ambito dell'assegnazione non siano interessati dalle operazioni di deprovisioning. Dopo la sincronizzazione iniziale, il servizio di provisioning sincronizza automaticamente gli oggetti utente e gruppo a intervalli di dieci minuti.

Se si imposta **Stato del provisioning** su **No**, il servizio di provisioning viene semplicemente sospeso. In questo stato Azure non crea, aggiorna o rimuove oggetti utente o gruppo nell'app. Se si reimposta lo stato su Sì, il servizio ripartirà dal punto in cui si era interrotto.

Se si seleziona la casella di controllo **Cancella lo stato corrente e riavvia la sincronizzazione** e si salva, il servizio di provisioning viene arrestato, i dati memorizzati nella cache sugli account gestiti da Azure AD vengono rimossi, i servizi vengono riavviati e viene eseguita di nuovo la sincronizzazione iniziale. Questa opzione consente agli amministratori di avviare di nuovo il processo di distribuzione del provisioning.

###<a name="synchronization-details"></a>Dettagli sincronizzazione
Questa sezione fornisce dettagli aggiuntivi sul funzionamento del servizio di provisioning, incluse la prima e l'ultima volta in cui il servizio di provisioning è stato eseguito nell'applicazione e il modo in cui vengono gestiti gli oggetti utente e gruppo.

Sono disponibili collegamenti al **report dell'attività di provisioning**, che fornisce un log di tutti gli utenti e di tutti i gruppi creati, aggiornati e rimossi tra Azure AD e l'applicazione di destinazione, e al **report degli errori di provisioning**, che fornisce messaggi di errore più dettagliati per oggetti utente e gruppo che non è stato possibile leggere, creare, aggiornare o rimuovere. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG



<!--HONumber=Oct16_HO2-->


