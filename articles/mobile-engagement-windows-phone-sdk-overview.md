<properties 
	pageTitle="Integrazione dell'SDK Windows Phone per Azure Mobile Engagement" 
	description="Ultimi aggiornamenti e procedure per l'SDK Windows Phone per Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#SDK Windows Phone per Azure Mobile Engagement v2.0.0
Iniziare da qui per ottenere tutti i dettagli su come integrare Azure Mobile Engagement in un'app Windows Phone. Se si desidera effettuare prima una prova, eseguire l'[esercitazione di 15 minuti](../mobile-engagement-windows-phone-get-started/).

Fare clic per visualizzare il [contenuto SDK](../mobile-engagement-windows-phone-sdk-content/)

##Procedure di integrazione

1. Iniziare da qui: [Come integrare Mobile Engagement nell'app Windows Phone](../mobile-engagement-windows-phone-integrate-engagement/)

2. Per le notifiche: [Come integrare Copertura (Notifiche) nell'app Windows Phone](../mobile-engagement-windows-phone-integrate-engagement-reach/)

3. Implementazione del piano di tag: [Come utilizzare l'API di tag di Mobile Engagement avanzata nell'app Windows Phone](../mobile-engagement-windows-phone-use-engagement-api/)

##Note sulla versione

###2.0.0 (17/02/2015)
- Versione iniziale di Azure Mobile Engagement
- La configurazione di appId/sdkKey viene sostituita da una configurazione della stringa di connessione.
- Miglioramenti della protezione.

Per la versione precedente, vedere le [note sulla versione complete](../mobile-engagement-windows-phone-release-notes/)

##Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se sono state perse diverse versioni dell'SDK, è possibile che sia necessario seguire più procedure; vedere le [procedure di aggiornamento](mobile-engagement-windows-phone-upgrade-procedure/) complete. Ad esempio, se si esegue la migrazione da 0.10.1 a 0.11.0, è necessario innanzitutto seguire la procedura "da 0.9.0 a 0.10.1", quindi la procedura "da 0.10.1 a 0.11.0".

###Da 1.1.1 a 2.0.0

Di seguito viene descritto come eseguire la migrazione di un'integrazione SDK dal servizio Capptain fornito da Capptain SAS in un'app con tecnologia Azure Mobile Engagement. 

>[Azure.IMPORTANT] Capptain e Mobile Engagement non sono servizi uguali e la procedura riportata di seguito indica solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON non trasferirà i dati dai server Capptain ai server Mobile Engagement

Se si esegue la migrazione da una versione precedente, consultare il sito Web Capptain per eseguire prima la migrazione a 1.1.1, quindi applicare la procedura seguente

#### Pacchetto NuGet

Sostituire Capptain.WindowsPhone.nupkg con azuresdk-mobileengagement-windowsphone-VERSION.nupkg nella cartella lib del pacchetto di archivio.

#### Applicazione di Mobile Engagement

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

After migration:

			using Microsoft.Azure.Engagement;

All Capptain classes that contain "Capptain" should contain "Engagement".

Before migration:

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
			xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
			...
			</capptain:CapptainPage>

Dopo la migrazione:

			<engagement:EngagementPage
			...
			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
			...
			</engagement:EngagementPage>

Per altre risorse come le immagini di Capptain, tenere presente che sono state rinominate per l'utilizzo di "Engagement".

#### ID applicazione / chiave SDK

Engagement utilizza una stringa di connessione. Non è necessario specificare un ID applicazione e una chiave SDK con Mobile Engagement, è sufficiente specificare una stringa di connessione. È possibile configurarla nel file EngagementConfiguration.

La configurazione di Engagement può essere impostata ne file  `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

- La stringa di connessione dell'applicazione tra i tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale Azure.

#### Modifica del nome di elementi

Ogni elemento denominato...... capptain....è stato rinominato ...engagement... Lo stesso accade per Capptain e Engagement.

Esempi su elementi di Capptain di uso comune:

> - CapptainConfiguration è diventato EngagementConfiguration
> - CapptainAgent è diventato EngagementAgent
> - CapptainReach è diventato EngagementReach
> - CapptainHttpConfig è diventato EngagementHttpConfig
> - GetCapptainPageName è diventato GetEngagementPageName

Si noti la ridenominazione influisce anche sui metodi sottoposti a override.

### Eseguire l'aggiornamento da versioni precedenti

Vedere [Procedure aggiornamento](mobile-engagement-windows-phone-upgrades/)

<!--HONumber=47-->
