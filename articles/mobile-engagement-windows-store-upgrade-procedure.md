<properties 
	pageTitle="Procedura di aggiornamento dell'SDK Windows Store per Azure Mobile Engagement" 
	description="Ultimi aggiornamenti e procedure per l'SDK Windows Store per Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se sono state perse alcune versioni dell'SDK, potrebbe essere necessario seguire più procedure. Ad esempio, se si esegue la migrazione da 0.10.1 a 0.11.0, è necessario innanzitutto seguire la procedura "da 0.9.0 a 0.10.1", quindi la procedura "da 0.10.1 a 0.11.0".

##Da 1.1.1 a 2.0.0

Di seguito viene descritto come eseguire la migrazione di un'integrazione SDK dal servizio Capptain fornito da Capptain SAS in un'app con tecnologia Azure Mobile Engagement. 

>[Azure.IMPORTANT] Capptain e Mobile Engagement non sono servizi uguali e la procedura riportata di seguito indica solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON non trasferirà i dati dai server Capptain ai server Mobile Engagement

Se si esegue la migrazione da una versione precedente, consultare il sito web Capptain per eseguire prima la migrazione a 1.1.1, quindi applicare la procedura seguente

### Pacchetto NuGet

Sostituire Capptain.WindowsPhone.nupkg con azuresdk-mobileengagement-windows-VERSION.nupkg nella cartella lib del pacchetto di archivio.

### Applicazione di Mobile Engagement

L'SDK utilizza il termine  `Engagement`. È necessario aggiornare il progetto per tenere conto di questa modifica.

È necessario disinstallare il pacchetto nuget corrente di Capptain. Si consideri che verranno rimosse tutte le modifiche nella cartella Risorse di Capptain. Se si desidera mantenere tali file, eseguirne una copia.

Successivamente, installare il nuovo pacchetto nuget di Microsoft Azure Engagement nel progetto. È possibile individuarlo direttamente sul [sito Web nuget]. o indice qui. Questa operazione sostituisce tutti i file di risorse utilizzati da Engagement e aggiunge la nuova DLL di Engagement ai riferimenti del progetto.

È necessario eliminare i riferimenti del progetto rimuovendo i riferimenti DLL di Capptain. Se non si effettua questa operazione, la versione di Capptain creerà un conflitto e si verificheranno errori.

Se sono state personalizzate risorse Capptain, copiare il contenuto dei file precedenti e incollarlo in nuovi file di progetto. Si noti che è necessario aggiornare sia i file xaml che i file cs.

Al termine di queste operazioni, è necessario sostituire i riferimenti di Capptain precedenti con i nuovi riferimenti di Engagement.

Tutti gli spazi dei nomi Capptain devono essere aggiornati.

Prima della migrazione:

			using Capptain.Agent;
			using Capptain.Reach;

Dopo la migrazione:

			using Microsoft.Azure.Engagement;

Tutte le classi Capptain che contengono "Capptain" devono contenere "Engagement".

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

Per i file xaml cambiano anche attributi e spazio dei nomi di Capptain.

Prima della migrazione:

			<capptain:CapptainPage
			...
			xmlns:capptain="using:Capptain.Agent"
			...
			</capptain:CapptainPage>

Dopo la migrazione:

			<engagement:EngagementPage
			...
			xmlns:engagement="using:Microsoft.Azure.Engagement"
			...
			</engagement:EngagementPage>

> [AZURE.IMPORTANT] Anche la sovrapposizione cambia. Il nuovo spazio dei nomi è  `Microsoft.Azure.Engagement.Overlay`. Deve essere utilizzato sia nei file xaml sia nei file cs. Inoltre,  `CapptainGrid` deve essere denominato  `EngagementGrid`, `capptain_notification_content` e  `capptain_announcement_content` sono denominati  `engagement_notification_content` e  `engagement_announcement_content`.

Per la sovrapposizione:

			<capptain:CapptainPageOverlay
			 xmlns:capptain="using:Capptain.Overlay"
			 ...
			</capptain:CapptainPageOverlay>

Diventa:

			<EngagementPageOverlay
			 engagement="using:Microsoft.Azure.Engagement.Overlay"
			 ...
			</engagement:EngagementPageOverlay>

Per altre risorse come le immagini di Capptain e i file HTML, tenere presente che sono state rinominate per l'utilizzo di "Engagement".

### Dichiarazione di progetto

Su Package.appxmanifest  `File Type Associations` è stato aggiornato da:

> - capptain\_reach\_content to engagement\_reach\_content
> - capptain\_log\_file to engagement\_log\_file

### ID applicazione / chiave SDK

Engagement utilizza una stringa di connessione. Non è necessario specificare un ID applicazione e una chiave SDK con Mobile Engagement, è sufficiente specificare una stringa di connessione. È possibile configurarla nel file EngagementConfiguration.

La configurazione di Engagement può essere impostata ne file  `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

- La stringa di connessione dell'applicazione tra i tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale Azure.

### Modifica del nome di elementi

Ogni elemento denominato...... capptain....è stato rinominato ...engagement... Lo stesso accade per Capptain e Engagement.

Esempi su elementi di Capptain di uso comune:

> - CapptainConfiguration è diventato EngagementConfiguration
> - CapptainAgent è diventato EngagementAgent
> - CapptainReach è diventato EngagementReach
> - CapptainHttpConfig è diventato EngagementHttpConfig
> - GetCapptainPageName è diventato GetEngagementPageName

Si noti la ridenominazione influisce anche sui metodi sottoposti a override.

[sito Web NuGet]:http://www.nuget.org/packages/Capptain.WindowsCS


<!--HONumber=47-->
