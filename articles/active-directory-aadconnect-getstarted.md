<properties 
	pageTitle="Introduzione ad Azure AD Connect" 
	description="Informazioni su come scaricare, installare e configurare la procedura guidata per Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="azure-active-directory-connect" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Introduzione ad Azure AD Connect

La documentazione seguente contiene le informazioni necessarie per iniziare a usare Azure Active Directory Connect.

## Scaricare Azure AD Connect



Per iniziare a usare Azure AD Connect, è possibile scaricare la versione più recente dalla pagina del [download di Azure AD Connect in versione di anteprima pubblica](http://connect.microsoft.com/site1164/program8612).

## Prima di installare Azure AD Connect
Prima di installare Azure AD Connect, sono necessari alcuni elementi.

- Sottoscrizione di Azure o [sottoscrizione di una versione di valutazione di Azure](http://azure.microsoft.com/pricing/free-trial/)
- Azure AD Premium o [versione di valutazione di Azure AD Premium](http://aka.ms/aadptrial)
- Un account amministratore globale di Azure AD per il tenant di Azure AD con cui si vuole eseguire l'integrazione
- Un controller di dominio o server membro di Active Directory con Windows Server 2008 o versioni successive
- Un account amministratore dell'organizzazione per Active Directory locale
- Facoltativo: un account utente di prova per verificare la sincronizzazione. 

Se si intende usare l'opzione di accesso SSO con ADFS, saranno necessari anche gli elementi seguenti:

- Credenziali di amministratore locale nei server federativi desiderati.
- Credenziali di amministratore locale in qualsiasi server di gruppi di lavoro (non inserito in un dominio) in cui si intende distribuire il ruolo Proxy applicazione Web.
- Un server Windows Server 2012 R2 per il server federativo.
- Un server Windows Server 2012 R2 per il ruolo Proxy applicazione Web.
-  Un file PFX con un certificato SSL per il nome del servizio federativo desiderato, ad esempio fs.contoso.com.
- Il computer in cui si esegue la procedura guidata deve essere in grado di connettersi a qualsiasi altro computer in cui si vuole installare ADFS o il ruolo Proxy applicazione Web tramite Gestione remota Windows.


## Installazione di Azure AD Connect

Dopo aver scaricato Azure AD Connect, eseguire le operazioni seguenti per installarlo.

1. Accedere al server in cui si vuole installare Azure AD Connect come amministratore dell'organizzazione.
2. Selezionare e fare doppio clic su AzureADConnect.msi.
3. Eseguire la procedura guidata usando le impostazioni rapide o personalizzate.
4. Facoltativo: usare l'account utente di prova per accedere a un servizio cloud come Office 365 per eseguire il test.

<center>![Azure AD Connect](./media/active-directory-aadconnect-getstarted/aadConnect_Welcome.png)</center>

## Configurazioni facoltative dei servizi di sincronizzazione
Durante l'installazione dei servizi di sincronizzazione, è possibile lasciare deselezionata l'opzione di configurazione facoltativa perché Azure AD Connect esegua la configurazione in modo automatico. Sono incluse la configurazione di un'istanza di SQL Server 2012 Express e la creazione dei gruppi appropriati e l'assegnazione delle rispettive autorizzazioni. Se si vuole modificare le impostazioni predefinite, è possibile usare la tabella di seguito per informazioni sulle opzioni di configurazione facoltative disponibili.

Configurazione facoltativa | Descrizione 
------------- | ------------- |
Nome SQL Server |Permette di specificare il nome di SQL Server e il nome dell'istanza. Scegliere questa opzione se si dispone già di un server di database che si vuole usare.
Account del servizio |Per impostazione predefinita, Azure AD Connect crea un account di servizio che verrà usato dai servizi di sincronizzazione. Il problema è che la password viene generata automaticamente e non è nota alla persona che installa Azure AD Connect. Benché nella maggior parte degli scenari questa situazione sia accettabile, se si vuole eseguire configurazioni avanzate come il controllo dell'ambito delle unità organizzative sincronizzate, sarà preferibile creare un account e scegliere personalmente la password. |
Autorizzazioni | Per impostazione predefinita, Azure AD Connect crea quattro gruppi quando vengono installati i servizi di sincronizzazione. I gruppi sono: gruppo Administrators, gruppo Operators, gruppo Browse e Gruppo reimpostazione password. Se si vuole specificare gruppi personalizzati, è possibile farlo qui.
Importa impostazioni |Usare questa opzione se si intende importare informazioni di configurazione da DirSync di Azure AD Sync.|



## Installazione rapida
La selezione di Impostazioni rapide rappresenta l'opzione predefinita e uno degli scenari più comuni. In questo caso, Azure AD Connect distribuisce la sincronizzazione con l'opzione di sincronizzazione degli hash delle password. Questo scenario è possibile solo per una singola foresta e permette agli utenti di usare la propria password locale per accedere al cloud. Se si usa l'installazione rapida, viene automaticamente avviata una sincronizzazione al termine dell'installazione. Questa opzione comporta solo sei clic per estendere la directory locale al cloud.

<center>![Installazione rapida](./media/active-directory-aadconnect-getstarted/express.png)</center>

## Installazione personalizzata

Con l'installazione personalizzata è possibile selezionare numerose opzioni diverse. La tabella seguente descrive le pagine della procedura guidata disponibili per quando si seleziona l'opzione di installazione personalizzata.

Nome pagina | Descrizione
-------------------    | ------------- | 
Accesso utente|In questa pagina è possibile scegliere se usare la sincronizzazione delle password, la federazione con ADFS o nessuna delle due opzioni.
Connessione delle directory|In questa pagina è possibile aggiungere una o più directory con cui eseguire la sincronizzazione.
Filtro sincronizzazione| In questa pagina è possibile determinare se sincronizzare tutti gli utenti e i gruppi oppure specificare un gruppo per ogni directory e sincronizzare solo questi.
Identità locali|In questa pagina è possibile specificare che gli utenti sono presenti una sola volta in tutte le directory aggiunte nella pagina Connessione delle directory oppure che sono presenti in più directory. Se gli utenti sono presenti in più directory, è necessario scegliere un attributo che li identifichi in modo univoco nelle directory. Ad esempio, l'attributo mail, ObjectSID o SAMAccountName sono tutti attributi comuni usati per identificare in modo univoco gli utenti.
Identità di Azure|In questa pagina è possibile specificare l'ancoraggio di origine che si vuole usare per la federazione delle identità.
Funzionalità facoltative|Usare la tabella seguente per una breve descrizione delle funzionalità facoltative che è possibile selezionare.

<center>![Installazione rapida](./media/active-directory-aadconnect-getstarted/of.png)</center>


Funzionalità facoltative | Descrizione
-------------------    | ------------- | 
Distribuzione ibrida di Exchange |La funzionalità Distribuzione ibrida di Exchange permette l'esistenza simultanea di cassette postali di Exchange sia in locale sia in Azure risincronizzando un set specifico di attributi da Azure AD alla directory locale.
Filtro attributi e app di Azure AD|Se si abilita questa opzione, il set di attributi sincronizzati può essere adattato a un set specifico in una pagina successiva della procedura guidata. In questo caso, vengono visualizzate due pagine di configurazione aggiuntive della procedura guidata.  
Writeback password|Se si abilita il writeback delle password, le modifiche delle password generate da Azure AD verranno riscritte nella directory locale.
Writeback utenti|Se si abilita il writeback degli utenti, gli utenti creati in Azure AD verranno riscritti nella directory locale. In questo caso, viene visualizzata un'altra pagina di configurazione della procedura guidata.  
Sincronizzazione dispositivi|Se si abilita la sincronizzazione dei dispositivi, la configurazione dei dispositivi può essere scritta in Azure AD.
Sincronizzazione attributi estensione della directory|Se si abilita questa opzione, gli attributi specificati verranno sincronizzati in Azure AD. In questo caso, viene visualizzata un'altra pagina di configurazione della procedura guidata.  

Per altre opzioni di configurazione, come la modifica della configurazione predefinita e l'uso dell'Editor regole di sincronizzazione e del provisioning dichiarativo, vedere [Gestione di Azure AD Connect](active-directory-aadconnect-manage.md)

## Componenti di supporto di Azure AD Connect

Di seguito è riportato un elenco di prerequisiti e componenti di supporto che verranno installati da Azure AD Connect nel server su cui è stato impostato Azure AD Connect. L'elenco riguarda un'istallazione di SQL Express di base. Se si sceglie di usare una versione diversa di SQL Server nella pagina Installazione dei servizi di sincronizzazione, i componenti di SQL Server 2012 elencati di seguito non verranno installati.

- Connettore di Azure Active Directory per Forefront Identity Manager
- Utilità della riga di comando di Microsoft SQL Server 2012
- Client nativo di Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Modulo di Azure Active Directory per Windows PowerShell
- Assistente per l'accesso a Microsoft Online Services per professionisti IT
- Microsoft Visual C++ 2013 Redistribution Package


**Risorse aggiuntive**

* [Usare l'infrastruttura di identità locale nel cloud](active-directory-aadconnect.md)
* [Funzionamento di Azure AD Connect](active-directory-aadconnect-howitworks.md)
* [Gestione di Azure AD Connect](active-directory-aadconnect-manage.md)
* [Azure AD Connect in MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->