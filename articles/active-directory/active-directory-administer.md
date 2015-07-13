<properties 
	pageTitle="Amministrare la directory di Azure AD" 
	description="Argomento che illustra il significato di tenant di Azure AD e come gestire una directory di Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Amministrare la directory di Azure AD

## Che cos'è un tenant di Azure AD?

Nell'area di lavoro fisica, il termine tenant può essere definito come un gruppo o un'azienda che occupa un edificio. Un'organizzazione, ad esempio, può essere proprietaria di uno spazio adibito a ufficio in un edificio. L'edificio può trovarsi in una strada dove sono presenti numerose altre organizzazioni. L'organizzazione verrebbe considerata un tenant di tale edificio. L'edificio è un asset dell'organizzazione che fornisce sicurezza e offre l'opportunità di lavorare in un ambiente protetto. È inoltre separato dalle altre aziende nella stessa strada. Ciò garantisce che l'organizzazione e le sue risorse siano isolate dalle altre organizzazioni.

Nell'area di lavoro abilitata per il cloud, un tenant può essere definito come un client o un'organizzazione che possiede e gestisce un'istanza specifica di tale servizio cloud. Con la piattaforma delle identità fornita da Microsoft Azure, un tenant è semplicemente un'istanza dedicata di Azure Active Directory (Azure AD) che l'organizzazione riceve e di cui diventa proprietaria quando effettua l'iscrizione a un servizio cloud Microsoft, ad esempio Azure o Office 365.

Ogni directory di Azure AD è distinta e separata dalle altre directory di Azure AD. Proprio come un edificio di uffici è un asset sicuro specifico solo dell'organizzazione che vi ha sede, anche una directory di Azure AD è stata progettata per essere un asset sicuro usato solo dall'organizzazione proprietaria. L'architettura di Azure AD isola le informazioni relative all'identità e i dati dei clienti evitando che si combinino con altri. Questo significa che gli utenti e gli amministratori di una directory di Azure AD non possono accedere accidentalmente o con dolo ai dati presenti in un'altra directory.

![][1]

## Come è possibile ottenere una directory di Azure AD?

Azure AD fornisce le principali funzionalità di gestione delle identità e di directory alla base della maggior parte dei servizi cloud Microsoft, tra cui:

- Azure
- Microsoft Office 365
- Microsoft Dynamics CRM Online
- Microsoft Intune

Quando si effettua l'iscrizione a uno di questi servizi cloud Microsoft, si ottiene una directory di Azure AD. È possibile creare altre directory, se necessario. Ad esempio, è possibile usare la prima directory come directory di produzione e quindi crearne un'altra per le attività di testing o di gestione temporanea.

> [AZURE.NOTE]Dopo aver effettuato l'iscrizione al primo servizio, è consigliabile usare lo stesso account amministratore associato all'organizzazione se si effettua l'iscrizione ad altri servizi cloud Microsoft.

La prima volta che si effettua l'iscrizione a un servizio cloud Microsoft, viene chiesto di fornire i dettagli dell'organizzazione e la registrazione del nome di dominio Internet. Queste informazioni vengono quindi usate per creare una nuova istanza di directory di Azure AD per l'organizzazione. Questa stessa directory viene usata per autenticare i tentativi di accesso quando si sottoscrivono più servizi cloud Microsoft.

I servizi aggiuntivi usano in modo ottimale tutti gli account utente, i criteri e le impostazioni esistenti o l'integrazione di directory locale configurata per migliorare l'efficienza tra l'infrastruttura di identità dell'organizzazione locale e Azure AD.

Se, ad esempio, in origine è stata effettuata l'iscrizione a una sottoscrizione di Microsoft Intune e sono stati eseguiti i passaggi necessari per integrare ulteriormente l'ambiente Active Directory locale con la directory di Azure AD distribuendo la sincronizzazione della directory e/o server Single Sign-On, è possibile iscriversi a un altro servizio cloud Microsoft, ad esempio Office 365, che potrà usufruire degli stessi vantaggi dell'integrazione di directory già in uso con Microsoft Intune.

Per altre informazioni sull'integrazione della directory locale con Azure AD, vedere [Integrazione di directory](active-directory-aadconnect.md).

### Associare una directory di Azure AD a una nuova sottoscrizione di Azure

È possibile associare una nuova sottoscrizione di Azure alla stessa directory che autentica l'accesso per una sottoscrizione esistente di Office 365 o Microsoft Intune. Accedere al portale di gestione di Azure usando l'account aziendale o dell'istituto di istruzione. Il portale di gestione restituisce un messaggio che indica che non è stato possibile trovare sottoscrizioni per tale account. Selezionare **Iscrizione ad Azure** e la directory sarà disponibile per l'amministrazione all'interno del portale. Per altre informazioni, vedere [Gestire la directory per la sottoscrizione di Office 365 in Azure](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure).

Per un video sulle domande comuni sull'uso di Azure AD, vedere la pagina relativa alle [domande comuni su iscrizione, accesso e uso di Azure Active Directory](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions).

### Creare una directory di Azure AD effettuando l'iscrizione a un servizio cloud Microsoft come organizzazione

Se non si dispone ancora di una sottoscrizione a un servizio cloud Microsoft, usare uno dei collegamenti seguenti per iscriversi. Con l'iscrizione al primo servizio verrà automaticamente creata una directory di Azure AD.

- [Microsoft Azure](https://account.windowsazure.com/organization)
- [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
- [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### Gestire una directory predefinita di cui Azure ha eseguito il provisioning

Attualmente viene creata automaticamente una directory quando si esegue l'iscrizione ad Azure e la sottoscrizione viene associata a tale directory. Tuttavia, se l'iscrizione ad Azure è stata effettuata prima di ottobre 2013, non è stata creata automaticamente alcuna directory. In questo caso, Azure potrebbe aver "recuperato le informazioni" per l'account effettuando, per tale account, il provisioning di una directory predefinita. La sottoscrizione è stata quindi associata a tale directory predefinita.

Il recupero delle informazioni delle directory è stato eseguito nel mese di ottobre 2013 come parte di un miglioramento generale del modello di sicurezza di Azure. In questo modo, è possibile offrire funzionalità di identità dell'organizzazione a tutti i clienti Azure e garantire che l'accesso a tutte le risorse di Azure avvenga nel contesto di un utente nella directory. Non è possibile usare Azure senza una directory. A tale scopo, gli utenti che si sono iscritti prima del 7 luglio 2013, ma che non disponevano di una directory, hanno dovuto crearne una. Se la directory era già stata creata, la sottoscrizione è stata associata a tale directory.

L'uso di Azure AD non comporta costi aggiuntivi. La directory è una risorsa gratuita. È disponibile un ulteriore livello, Azure Active Directory Premium, che viene concesso in licenza separatamente e offre funzionalità aggiuntive, ad esempio informazioni personalizzate distintive di una società e reimpostazione della password self-service.

Per modificare il nome visualizzato della directory, fare clic sulla directory nel portale e quindi su **Configura**. Come illustrato più avanti in questo argomento, è possibile aggiungere una nuova directory o eliminarne una non più necessaria. Per associare la sottoscrizione a una directory diversa, fare clic sull'estensione **Impostazioni** nel pannello di navigazione a sinistra e, nella parte inferiore della pagina **Sottoscrizioni**, fare clic su **Modifica directory**. È anche possibile creare un dominio personalizzato usando un nome DNS registrato anziché il dominio predefinito *.onmicrosoft.com, che è preferibile usare con un servizio come SharePoint Online.

## Come gestire i dati di directory

Un amministratore di una o più sottoscrizioni di servizi cloud Microsoft può usare il portale di gestione di Azure, il portale degli account di Microsoft Intune o l'interfaccia di amministrazione di Office 365 per gestire i dati di directory dell'organizzazione. È anche possibile scaricare ed eseguire i cmdlet del [Modulo di Microsoft Azure Active Directory per Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) per gestire i dati archiviati in Azure AD.

Da uno di questi portali (o cmdlet), è possibile:

- Creare e gestire account di gruppi e utenti
- Gestire i servizi cloud correlati sottoscritti dall'organizzazione
- Configurare l'integrazione locale con il servizio di directory

Il portale di gestione di Azure, l'interfaccia di amministrazione di Office 365, il portale degli account di Microsoft Intune e i cmdlet di Azure AD leggono e scrivono tutti in una singola istanza condivisa di Azure AD associata alla directory dell'organizzazione, come illustrato nella figura seguente. In questo modo, i portali (o i cmdlet) funzionano come interfaccia front-end che effettua il pull dei dati della directory e/o li modifica.

![][2]

Questi portali degli account e i cmdlet di PowerShell per Azure AD associati usati per gestire utenti e gruppi sono creati basandosi sulla piattaforma Azure AD.

Quando si apporta una modifica ai dati dell'organizzazione usando uno dei portali (o cmdlet) dopo avere effettuato l'accesso nel contesto di uno di questi servizi, la modifica viene visualizzata anche negli altri portali all'accesso successivo nel contesto di tale servizio perché i dati sono condivisi tra i servizi cloud Microsoft sottoscritti. Se, ad esempio, è stata usata l'interfaccia di amministrazione di Office 365 per impedire a un utente di effettuare l'accesso, tale azione impedirà l'accesso dell'utente a qualsiasi altro servizio attualmente sottoscritto dall'organizzazione. Se si recupera lo stesso account utente nel contesto del portale degli account di Microsoft Intune, sarà possibile vedere che l'utente è bloccato.

## Come è possibile aggiungere e gestire più directory?

È possibile aggiungere una directory di Azure AD nel portale di gestione di Azure. Selezionare l'estensione **Active Directory** a sinistra e fare clic su **Aggiungi**.

È possibile gestire ogni directory come una risorsa completamente indipendente. Ciascuna directory è infatti un peer con funzionalità complete e indipendente dal punto di vista logico dalle altre directory gestite. Non vi è alcuna relazione padre-figlio tra le directory. Questa indipendenza tra le directory include l'indipendenza delle risorse, l'indipendenza amministrativa e l'indipendenza della sincronizzazione.

- **Indipendenza delle risorse**. Se si crea o si elimina una risorsa in una directory, ciò non influisce sulle risorse contenute in un'altra directory, con l'eccezione parziale degli utenti esterni, come spiegato più avanti. Se si usa un dominio personalizzato "contoso.com" con una directory, non è possibile usarlo con altre directory. 
- **Indipendenza amministrativa**. Se un utente non amministratore della directory "Contoso" crea una directory di test denominata "Test", si verifica quanto segue: 
    - ◦Strumento di sincronizzazione directory, per sincronizzare i dati con una singola foresta AD. 
    - ◦Gli amministratori della directory "Contoso" non dispongono di privilegi amministrativi diretti per la directory "Test" se tali privilegi non vengono loro concessi specificamente da un amministratore di "Test". Gli amministratori di "Contoso" possono controllare l'accesso alla directory "Test" grazie al controllo di cui dispongono sull'account utente che ha creato "Test". 

    Se inoltre si cambia, ovvero si aggiunge o si rimuove, un ruolo di amministratore per un utente in una directory, la modifica non incide sul ruolo di amministratore che l'utente può avere in un'altra directory.


- **Indipendenza della sincronizzazione**. È possibile configurare ogni implementazione di Azure AD in modo indipendente per sincronizzare i dati da una singola istanza di uno degli elementi seguenti:
    - ◦Strumento di sincronizzazione directory, per sincronizzare i dati con una singola foresta AD
    - Connettore di Azure Active Directory per Forefront Identity Manager, per sincronizzare i dati con una o più foreste locali e/o origini dati non AD. 

Si noti, inoltre, che a differenza di altre risorse di Azure, le proprie directory non sono risorse figlio di una sottoscrizione di Azure. Se pertanto si annulla o si lascia scadere la propria sottoscrizione di Azure, sarà comunque possibile accedere ai dati delle directory mediante Azure AD PowerShell, l'API Graph di Azure o altre interfacce come l'interfaccia di amministrazione di Office 365. È anche possibile associare un'altra sottoscrizione alla directory.

## Come è possibile eliminare una directory di Azure AD?
Un amministratore globale può eliminare una directory di Azure AD dal portale. Quando una directory viene eliminata, vengono eliminate anche tutte le risorse in essa contenute. Prima di procedere all'eliminazione, è quindi opportuno verificare che la directory non sia più necessaria.

> [AZURE.NOTE]Se l'utente ha eseguito l'accesso con un account aziendale o dell'istituto di istruzione, non deve tentare di eliminare la propria home directory. Ad esempio, se l'utente ha eseguito l'accesso come joe@contoso.onmicrosoft.com, non può eliminare la directory che ha contoso.onmicrosoft.com come dominio predefinito.

### Condizioni da soddisfare per eliminare una directory di Azure AD

Azure AD richiede che vengano soddisfatte determinate condizioni per eliminare una directory. In questo modo, si riduce il rischio che l'eliminazione di una directory possa incidere negativamente sugli utenti o sulle applicazioni, ad esempio sulla possibilità degli utenti di eseguire l'accesso a Office 365 o di accedere alle risorse in Azure. Se, ad esempio, una directory di una sottoscrizione venisse eliminata accidentalmente, gli utenti non potrebbero accedere alle risorse di Azure per tale sottoscrizione.

Viene verificato che siano soddisfatte le condizioni seguenti:

- L'unico utente presente nella directory deve essere l'amministratore globale che eliminerà la directory. Tutti gli altri utenti devono essere eliminati prima che possa essere eliminata la directory. Se gli utenti vengono sincronizzati dall'ambiente locale, sarà necessario disattivare la sincronizzazione e gli utenti devono essere eliminati nella directory cloud mediante il portale di gestione o il modulo di Azure per Windows PowerShell. Non è necessario eliminare gruppi o contatti, ad esempio i contatti aggiunti dall'interfaccia di amministrazione di Office 365.
- La directory non può contenere applicazioni. Tutte le applicazioni devono essere eliminate prima che possa essere eliminata la directory. 
- Alla directory non possono essere associate sottoscrizioni per i Microsoft Online Services, ad esempio Microsoft Azure, Office 365 o Azure AD Premium. Se, ad esempio, in Azure è stata creata una directory predefinita, non è possibile eliminare la directory se la propria sottoscrizione di Azure si basa ancora su di essa per l'autenticazione. Analogamente, non è possibile eliminare una directory se la sottoscrizione di un altro utente è associata a tale directory. Per associare la sottoscrizione a una directory diversa, accedere al portale di gestione di Azure e fare clic su **Impostazioni** nel pannello di navigazione a sinistra. Nella parte inferiore della pagina **Sottoscrizioni** fare quindi clic su **Modifica directory**. Per altre informazioni sulle sottoscrizioni Azure, vedere [Associazione delle sottoscrizioni di Azure ad Azure AD](active-directory-how-subscriptions-associated-directory.md). 

    > [AZURE.NOTE]Se l'utente ha eseguito l'accesso con un account aziendale o dell'istituto di istruzione, non deve tentare di eliminare la propria home directory. Ad esempio, se l'utente ha eseguito l'accesso come joe@contoso.onmicrosoft.com, non può eliminare la directory che ha contoso.onmicrosoft.com come dominio predefinito.

- Alla directory non possono essere collegati provider di Multi-Factor Authentication.


## Risorse aggiuntive

- [Forum di Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Forum di Azure Multi-Factor Authentication](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
- [Gestire Azure AD con Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
- [Assegnazione dei ruoli di amministratore in Azure AD](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png


 

<!---HONumber=62-->