<properties 
	pageTitle="Procedure di aggiornamento di Windows Universal Apps SDK" 
	description="Procedure di aggiornamento di Windows Universal Apps SDK per Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="piyushjo" />

#Procedure di aggiornamento di Windows Universal Apps SDK

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Se ad esempio si esegue la migrazione dalla versione 0.10.1 alla 0.11.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 0.9.0 alla 0.10.1" e quindi la procedura per la migrazione "dalla 0.10.1 alla 0.11.0".

##Dalla versione 2.0.0 alla 3.0.0

### Risorse
Questo passaggio riguarda solo le risorse personalizzate. Se sono state personalizzate le risorse fornite dall'SDK (html, immagini, sovrimpressioni) è necessario eseguirne il backup prima dell'aggiornamento e riapplicare la personalizzazione alle risorse aggiornate.

##Dalla versione 1.1.1 alla 2.0.0

La sezione seguente illustra come eseguire la migrazione di un'integrazione dell'SDK dal servizio Capptain offerto da Capptain SAS a un'app basata su Azure Mobile Engagement.

> [Azure.IMPORTANT]Capptain e Mobile Engagement sono servizi diversi e la procedura seguente illustra solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON comporta la migrazione dei dati dai server di Capptain ai server di Mobile Engagement

Se si esegue la migrazione da una versione precedente, consultare il sito web Capptain per eseguire prima la migrazione a 1.1.1, quindi applicare la procedura seguente

### Pacchetto NuGet

Sostituire **Capptain.WindowsPhone** con il pacchetto NuGet **MicrosoftAzure.MobileEngagement**.

### Applicazione di Mobile Engagement

L'SDK usa il termine `Engagement`. È necessario aggiornare il progetto per tenere conto di questa modifica.

È necessario disinstallare il pacchetto nuget corrente di Capptain. Si consideri che verranno rimosse tutte le modifiche nella cartella Risorse di Capptain. Se si desidera mantenere tali file, eseguirne una copia.

Successivamente, installare il nuovo pacchetto NuGet di Microsoft Azure Engagement nel progetto. È possibile trovarlo direttamente sul [sito Web di NuGet] o qui nell'indice. Questa operazione sostituisce tutti i file di risorse usati da Engagement e aggiunge la nuova DLL di Engagement ai riferimenti del progetto.

È necessario eliminare i riferimenti del progetto rimuovendo i riferimenti DLL di Capptain. Se non si effettua questa operazione, la versione di Capptain creerà un conflitto e si verificheranno errori.

Se sono state personalizzate risorse Capptain, copiare il contenuto dei file precedenti e incollarlo in nuovi file di progetto. Si noti che è necessario aggiornare sia i file xaml che i file cs.

Al termine di queste operazioni, è necessario sostituire i riferimenti di Capptain precedenti con i nuovi riferimenti di Engagement.

1. Tutti gli spazi dei nomi Capptain devono essere aggiornati.

	Prima della migrazione:
	
		using Capptain.Agent;
		using Capptain.Reach;
	
	Dopo la migrazione:
	
		using Microsoft.Azure.Engagement;

2. Tutte le classi Capptain che contengono "Capptain" devono contenere "Engagement".

	Prima della migrazione:
	
		public sealed partial class MainPage : CapptainPage
		{
		  protected override string GetCapptainPageName()
		  {
		    return "Capptain Demo";
		  }
		  ...
		}
	
	Dopo la migrazione:
	
		public sealed partial class MainPage : EngagementPage
		{
		  protected override string GetEngagementPageName()
		  {
		    return "Engagement Demo";
		  }
		  ...
		}

3. Per i file xaml cambiano anche attributi e spazio dei nomi di Capptain.

	Prima della migrazione:
	
		<capptain:CapptainPage
		...
		xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
		...
		</capptain:CapptainPage>
	
	Dopo la migrazione:
	
		<engagement:EngagementPage
		...
		xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
		...
		</engagement:EngagementPage>

4. Modifiche alle pagine di sovrimpressione
	> [AZURE.IMPORTANT]Anche la sovrimpressione cambia. Il nuovo spazio dei nomi è `Microsoft.Azure.Engagement.Overlay`. Deve essere usato sia nei file xaml sia nei file cs. Inoltre, `CapptainGrid` deve essere denominato `EngagementGrid` e `capptain_notification_content` e `capptain_announcement_content` sono denominati `engagement_notification_content` e `engagement_announcement_content`.
	
	Per la sovrimpressione:
	
		<capptain:CapptainPageOverlay
		  xmlns:capptain="using:Capptain.Overlay"
		  ...
		</capptain:CapptainPageOverlay>
	
	Diventa:
	
		<EngagementPageOverlay
		  engagement="using:Microsoft.Azure.Engagement.Overlay"
		  ...
		</engagement:EngagementPageOverlay>

5. Per altre risorse come le immagini di Capptain e i file HTML, tenere presente che sono state rinominate per l'utilizzo di "Engagement".

### Dichiarazione di progetto

In Package.appxmanifest `File Type Associations` è stato aggiornato da:

 -   capptain_reach_content a engagement_reach_content
 -   capptain_log_file a engagement_log_file

### ID applicazione / chiave SDK

Engagement utilizza una stringa di connessione. Non è necessario specificare un ID applicazione e una chiave SDK con Mobile Engagement, è sufficiente specificare una stringa di connessione. È possibile configurarla nel file EngagementConfiguration.

La configurazione di Engagement può essere impostata nel file `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

-   La stringa di connessione dell'applicazione tra i tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

	/* Engagement configuration. */
	EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
	engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
	
	/* Initialize Engagement agent with above configuration. */
	EngagementAgent.Instance.Init(args, engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di gestione di Azure.

### Modifica del nome di elementi

Tutti gli elementi contenenti *capptain* sono stati rinominati con *engagement*. Lo stesso vale per *Capptain*, che è stato sostituito con *Engagement*.

Esempi di elementi di Capptain di uso comune:

-   CapptainConfiguration è diventato EngagementConfiguration
-   CapptainAgent è diventato EngagementAgent
-   CapptainReach è diventato EngagementReach
-   CapptainHttpConfig è diventato EngagementHttpConfig
-   GetCapptainPageName è diventato GetEngagementPageName

Si noti la ridenominazione influisce anche sui metodi sottoposti a override.

 

<!---HONumber=July15_HO4-->