<properties 
	pageTitle="Come vengono associate le sottoscrizioni Azure ad Azure AD" 
	description="Questo articolo descrive la procedura di accesso a Microsoft Azure e i problemi correlati, ad esempio la relazione tra una sottoscrizione di Azure e Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
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

# Come vengono associate le sottoscrizioni Azure ad Azure AD

Questo articolo descrive la procedura di accesso a Microsoft Azure e i problemi correlati, ad esempio la relazione tra una sottoscrizione di Azure e Azure Active Directory (AD).

## Account che si possono usare per l'accesso
In questa sezione verranno descritti gli account che si possono usare per eseguire l'accesso. Sono disponibili due tipi di account: l'account Microsoft (precedentemente noto come Microsoft Live ID) e l'account aziendale o dell'istituto di istruzione, che è un account archiviato in Azure AD.

 Account Microsoft | Account Azure AD
	------------- | -------------
Sistema di identità utente gestito da Microsoft | Sistema di identità aziendale gestito da Microsoft
Autenticazione ai servizi orientati agli utenti privati, ad esempio Hotmail ed MSN | Autenticazione ai servizi orientati agli utenti aziendali, ad esempio Office 365
Gli utenti privati creano i propri account Microsoft, ad esempio quando creano un nuovo indirizzo di posta elettronica | Le aziende e le organizzazioni creano e gestiscono i propri account aziendali o dell'istituto di istruzione
Le identità vengono create e archiviate nel sistema di account Microsoft | Le identità vengono create tramite Azure o un altro servizio, ad esempio Office 365, e vengono archiviate in un'istanza di Azure AD assegnata all'organizzazione

Sebbene in origine l'accesso ad Azure fosse consentito solo agli utenti con account Microsoft, ora è invece consentito l'accesso agli utenti di *entrambi* i sistemi. A questo scopo, tutte le proprietà di Azure devono considerare Azure AD come attendibile per l'autenticazione, Azure AD deve autenticare gli utenti aziendali e creare una relazione federativa in cui Azure AD considera attendibile il sistema di identità utente degli account Microsoft per autenticare gli utenti privati. Di conseguenza, Azure AD è ora in grado di autenticare gli account Microsoft "guest" e gli account Azure AD "nativi".

Ad esempio, un utente con un account Microsoft accede al portale di gestione di Azure.

> [AZURE.NOTE]Per accedere al portale di gestione di Azure, msmith@hotmail.com deve avere una sottoscrizione di Azure. L'account deve essere un amministratore del servizio o un coamministratore della sottoscrizione.

![][1]

Poiché questo indirizzo Hotmail è un account per utenti privati, l'accesso viene autenticato dal sistema di identità utente degli account Microsoft. Il sistema di identità di Azure AD considera attendibile l'autenticazione eseguita tramite il sistema di account Microsoft e rilascia un token per accedere ai servizi di Azure.

## Relazione tra una sottoscrizione di Azure e Azure AD

Ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. Per visualizzare qual è la directory considerata attendibile dalla propria sottoscrizione, passare alla scheda Impostazioni. È possibile [modificare le impostazioni della sottoscrizione](https://msdn.microsoft.com/library/azure/dn584083.aspx) per cambiare la directory attendibile.

Questa relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione tra la sottoscrizione e tutte le altre risorse in Azure, ad esempio siti Web, database e così via, le quali sono da considerarsi più come risorse figlio di una sottoscrizione. Se la sottoscrizione scade, non sarà più possibile accedere a tutte queste altre risorse associate alla sottoscrizione anche se la directory rimane in Azure, quindi sarà possibile associarvi un'altra sottoscrizione e continuare a gestire gli utenti della directory.

Analogamente, l'estensione Azure AD disponibile nella sottoscrizione non funziona come le altre estensioni nel portale di gestione di Azure. L'ambito delle altre estensioni nel portale di gestione è limitato alla sottoscrizione di Azure. Quanto visualizzato nell'estensione AD non varia a seconda della sottoscrizione: vengono visualizzate solo le directory in base all'utente che ha eseguito l'accesso.

Tutti gli utenti dispongono di una singola home directory che li autentica, ma poi possono essere utenti guest in altre directory. Nell'estensione AD verranno visualizzate tutte le directory di cui è membro il proprio account utente. Le directory di cui l'account non è membro non saranno visualizzate. Una directory può rilasciare token per gli account aziendali o dell'istituto di istruzione in Azure AD o per gli utenti con account Microsoft, perché Azure AD è federato con il sistema di account Microsoft.

Questo diagramma mostra la sottoscrizione per Michael Smith dopo che ha eseguito l'accesso usando un account aziendale di Contoso.

![][2]

## Come gestire una sottoscrizione e una directory
I ruoli amministrativi per una sottoscrizione di Azure consentono di gestire le risorse associate alla sottoscrizione stessa. Questi ruoli e le procedure consigliate per gestire la sottoscrizione sono descritti in [Gestire account, sottoscrizioni e ruoli amministrativi](https://msdn.microsoft.com/library/azure/hh531793.aspx).

Per impostazione predefinita, al momento dell'iscrizione all'utente viene assegnato il ruolo di amministratore del servizio. Se altri utenti devono accedere ai servizi tramite la stessa sottoscrizione, è possibile aggiungerli come coamministratori. L'amministratore del servizio e i coamministratori possono essere costituiti da account Microsoft o account aziendali o dell'istituto di istruzione della directory a cui è associata la sottoscrizione di Azure.

In Azure AD è invece disponibile un set di ruoli amministrativi diverso per gestire la directory e le funzionalità relative alle identità. Ad esempio, l'amministratore globale di una directory può aggiungere utenti e gruppi alla directory o richiedere l'autenticazione a più fattori per gli utenti. All'utente che crea una directory viene assegnato il ruolo di amministratore globale, che, a sua volta, potrà assegnare ruoli di amministratore ad altri utenti.

Come per gli amministratori della sottoscrizione, i ruoli amministrativi di Azure AD possono essere costituiti da account Microsoft o account aziendali o dell'istituto di istruzione. I ruoli amministrativi di Azure AD vengono anche usati da altri servizi, ad esempio Office 365 e Microsoft Intune. Per altre informazioni, vedere [Assegnazione dei ruoli di amministratore](https://msdn.microsoft.com/library/azure/dn468213.aspx).

Il punto importante da tenere presente è che gli amministratori della sottoscrizione di Azure e gli amministratori della directory di AD sono due concetti distinti. Gli amministratori della sottoscrizione di Azure possono gestire le risorse in Azure e visualizzare l'estensione Active Directory nel portale di gestione, poiché il portale di gestione è una risorsa di Azure. Gli amministratori della directory possono gestire le proprietà nella directory.

Una persona può rivestire entrambi i ruoli, ma non è obbligatorio. A un utente può essere assegnato il ruolo di amministratore globale della directory ma non quello di amministratore del servizio o di coamministratore di una sottoscrizione di Azure. Senza il ruolo di amministratore della sottoscrizione, l'utente non potrà accedere al portale di gestione e svolgere solo attività di amministrazione della directory usando altri strumenti, ad esempio Azure AD PowerShell o l'interfaccia di amministrazione di Office 365.

### Perché non è possibile gestire la directory con l'account utente corrente?

È possibile che un utente tenti di accedere al portale di gestione con un account aziendale o dell'istituto di istruzione prima di aver creato la sottoscrizione di Azure. In questo caso, l'utente visualizzerà un messaggio che indica che non sono presenti sottoscrizioni per l'account. Nel messaggio sarà incluso un collegamento per creare una sottoscrizione di valutazione gratuita.

Al termine della procedura di iscrizione, l'utente visualizzerà la directory dell'organizzazione nel portale di gestione, ma non potrà gestirla, ovvero non potrà aggiungere utenti o modificare proprietà degli eventuali utenti esistenti, perché l'utente non è un amministratore globale della directory. La sottoscrizione consente all'utente di usare il portale di gestione e visualizzare l'estensione Active Directory, ma per gestire la directory sono necessarie le autorizzazioni aggiuntive di un amministratore globale.

## Uso dell'account aziendale o dell'istituto di istruzione per gestire una sottoscrizione di Azure creata con un account Microsoft

Per questo scenario è consigliabile [iscriversi ad Azure come organizzazione](sign-up-organization.md) e usare un account aziendale o dell'istituto di istruzione per gestire le risorse in Azure, in quanto questi account possono essere gestiti in modo centralizzato dall'organizzazione che li ha rilasciati, dispongono di un maggior numero di funzionalità rispetto agli account Microsoft e vengono direttamente autenticati da Azure AD. Lo stesso account consente di accedere ad altri Microsoft Online Services offerti ad aziende e organizzazioni, ad esempio Office 365 o Microsoft Intune. Se si dispone già di un account per accedere a queste altre proprietà, è probabile che lo stesso account verrà usato con Azure. In quel caso, sarà necessario che la sottoscrizione di Azure consideri attendibile l'istanza di Active Directory esistente che supporta tali proprietà.

Rispetto agli account Microsoft, gli account aziendali o dell'istituto di istruzione offrono un maggior numero di opzioni di gestione. Ad esempio, un amministratore può reimpostare la password di un account aziendale o dell'istituto di istruzione o richiedere l'autenticazione a più fattori per l'account.

O ancora, consentire a un utente dell'organizzazione di gestire le risorse associate a una sottoscrizione di Azure per un account utente Microsoft. Per altre informazioni su come eseguire la transizione per consentire la gestione delle sottoscrizioni e delle directory ad account diversi, vedere [Gestire la directory per la sottoscrizione Office 365 in Azure](#manage-the-directory-for-your-office-365-subscription-in-azure).


## Come eseguire l'accesso se l'indirizzo di posta aziendale è stato usato per creare un account Microsoft

Se in passato è stato creato un account utente Microsoft usando l'indirizzo di posta elettronica aziendale come identificatore utente, verrà visualizzata una pagina in cui viene richiesto di selezionare il nome utente dal sistema di account di Microsoft Azure o dal sistema di account Microsoft.

![][3]

Sono presenti account utente con lo stesso nome, uno in Azure AD e l'altro nel sistema di account utente Microsoft. Selezionare l'account associato alla sottoscrizione di Azure che si vuole usare. Se viene visualizzato un errore che indica che non esiste una sottoscrizione per l'utente, è probabile che sia stata selezionata l'opzione non corretta. Disconnettersi e riprovare. Per altre informazioni sugli errori che possono impedire l'accesso, vedere la discussione relativa alla [risoluzione degli errori derivanti dall'impossibilità di trovare sottoscrizioni associate all'account](https://social.msdn.microsoft.com/Forums/it-it/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement).

## Gestire la directory per la sottoscrizione di Office 365 in Azure

Si supponga di aver effettuato l'iscrizione a Office 365 prima dell'iscrizione ad Azure e ora si vuole gestire la directory per la sottoscrizione di Office 365 nel portale di gestione. Esistono due modi per farlo, a seconda che si sia effettuata l'iscrizione ad Azure o meno.

### Non ho una sottoscrizione di Azure

In questo caso, sarà sufficiente [iscriversi ad Azure](sign-up-organization.md) usando lo stesso account aziendale o dell'istituto di istruzione che si usa per accedere a Office 365. Le informazioni pertinenti dell'account Office 365 verranno precompilate nel modulo di iscrizione per Azure. All'account verrà assegnato il ruolo di amministratore del servizio della sottoscrizione.

### Ho effettuato l'iscrizione ad Azure usando l'account Microsoft

Se per l'iscrizione a Office 365 è stato usato un account aziendale o dell'istituto di istruzione e successivamente l'iscrizione ad Azure è stata effettuata con un account Microsoft, ora sono presenti due directory: una per l'account aziendale o dell'istituto di istruzione e una directory predefinita che è stata creata al momento dell'iscrizione ad Azure.

Per gestire entrambe le directory nel portale di gestione, procedere come indicato di seguito.

> [AZURE.NOTE]Per completare questa procedura, è necessario eseguire l'accesso con un account Microsoft. Se l'utente ha eseguito l'accesso con un account aziendale o dell'istituto di istruzione, l'opzione **Utilizza directory esistente** non è disponibile, in quanto gli account aziendali o dell'istituto di istruzione possono essere autenticati solo dalla relativa home directory, ovvero la directory in cui tale account è archiviato e che è di proprietà dell'azienda o dell'istituto di istruzione.

1. Accedere al portale di gestione con il proprio account Microsoft.
2. Fare clic su **Nuovo** > **Servizi app** > **Active Directory** > **Directory** > **Creazione personalizzata**.
3. Fare clic su **Utilizza directory esistente**, selezionare **È possibile uscire ora** e quindi fare clic sul segno di spunta per completare l'azione.
4. Accedere al portale di gestione usando l'account a cui è assegnato il ruolo di amministratore globale della directory aziendale o dell'istituto di istruzione.
5. Quando viene visualizzare la richiesta **Usare la directory Contoso con Azure?**, fare clic su **continua**.
6. Fare clic su **Esci ora**.
7. Accedere di nuovo al portale di gestione con il proprio account Microsoft. Entrambe le directory verranno visualizzate nell'estensione Active Directory.


## Passaggi successivi
[Iscriversi ad Azure come organizzazione](sign-up-organization.md)


<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

 

<!---HONumber=62-->