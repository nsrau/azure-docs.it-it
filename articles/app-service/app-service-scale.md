<properties 
	pageTitle="Scalare il piano tariffario in Azure App Service" 
	description="Informazioni sulla scalabilità delle app Web, mobili, per API e per la logica in Azure App Service, inclusa scalabilità automatica." 
	services="app-service" 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="stepsic"/>

# Scalare il piano tariffario in Azure App Service

Per migliorare le prestazioni e la velocità effettiva delle app in Microsoft Azure, è possibile usare il [portale di Azure](https://portal.azure.com/) per scalare il piano App Service dalla modalità **Gratuita** a quella **Condivisa**, **Base**, **Standard** o **Premium**.

Il livello di servizio con il piano di servizio App dipende il [*piano tariffario* per il piano](/pricing/details/app-service/). Livelli di servizio più elevati quali le modalità **Standard** e **Premium** offrono maggiore potenza e flessibilità nella determinazione della modalità di uso delle risorse in Azure. La modifica del piano tariffario influisce sul numero di core e sulla quantità di memoria posseduta dal servizio e questo viene definito come *Aumento* (o *Riduzione*).

Oltre all'aumento del piano tariffario, è possibile aumentare il numero di istanze possedute dal servizio. Questa operazione viene detta *Aumento* o *Riduzione*. Vedere l'articolo relativo alla [scalabilità del numero di istanze manuale o automatica](../insights-how-to-scale.md) per altre informazioni su come *aumentare* e *ridimensionare*.

Per informazioni su Azure App Service Per informazioni sui piani di servizio app, vedere [Informazioni sui piani di servizio app](../web-sites-web-hosting-plan-overview.md) e [Panoramica approfondita dei piani di servizio app di Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). Per informazioni sui prezzi e le funzionalità dei singoli piani di servizio app, vedere [Dettagli prezzi del servizio app](/pricing/details/app-service/).

Infine, la scalabilità funziona in modo diverso se si desidera utilizzare un oggetto dedicato[ambiente App](app-service-app-service-environment-intro.md). Vedere[la scalabilità di applicazioni web in un ambiente App](app-service-web-scale-a-web-app-in-an-app-service-environment.md)per ulteriori dettagli.

> [AZURE.NOTE]Prima di passare un'app Web dalla modalità **Gratuita** a **Base**, **Standard** o **Premium**, è necessario innanzitutto rimuovere i limiti di spesa applicati alla sottoscrizione di Azure App Service. Per visualizzare o modificare le opzioni per la sottoscrizione di Microsoft Azure App Service, vedere [Sottoscrizioni di Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## Scalare il piano tariffario

1. Nel browser aprire il [portale di Azure][portal] e selezionare l'app che si vuole scalare.
	
2. Nel **Informazioni di base** per l'app, fare clic sul collegamento **Piano di servizio app/piano tariffario**.

3. Fare clic su **livello prezzi**, quindi verrà visualizzato l'elenco dei livelli di servizio per il piano. Ogni livello è accompagnato da un prezzo stimato per avere un'idea del costo medio per tale livello.
	
	![Scegliere il piano](./media/app-service-scale/ChoosePricingTier.png)
	
4. Dopo aver scelto il livello, fare clic su **Seleziona**.
	
	Dopo il completamento dell'operazione, nella scheda **Notifiche** verrà visualizzata la scritta verde lampeggiante **OPERAZIONE RIUSCITA**.
 
È inoltre possibile apprendere i diversi livelli di calcolo in Azure[qui](http://go.microsoft.com/fwlink/?LinkId=309169).
	
<a name="ScalingSQLServer"></a>
##Scalabilità delle risorse correlate
Se l'applicazione dipende da altri servizi, ad esempio SQL o Archiviazione, è anche possibile scalarli in base alle esigenze.

1. In **Informazioni di base** fare clic sul collegamento **Gruppo di risorse**.

2. Quindi, nella parte **Riepilogo** del pannello relativo al gruppo di risorse, fare clic su uno dei database collegati (o eventuali altre risorse che si vogliono scalare).

	![Database collegato](./media/app-service-scale/ResourceGroup.png)
	
3. Nel pannello relativo alle risorse collegate fare clic sulla parte **Piano tariffario**, selezionare uno dei piani in base alle esigenze di prestazioni, quindi scegliere **Seleziona**.
	
	![Scalare il database SQL](./media/app-service-scale/ScaleDatabase.png)
	
4. Se l'applicazione usa Archiviazione, la replica geografica viene automaticamente impostata quando si sceglie un piano tariffario che la supporta. Per SQL, d'altra parte, è necessario configurare manualmente la replica geografica per potenziare le funzionalità di disponibilità elevate e ripristino di emergenza del database SQL. A tale scopo, fare clic sulla parte **Replica geografica**.
	
	![Configurare la replica geografica per il database SQL](./media/app-service-scale/GeoReplication.png)
	
<a name="devfeatures"></a>
## Funzionalità per sviluppatori
A seconda del piano tariffario, sono disponibili le seguenti funzionalità orientate agli sviluppatori:

### Numero di bit ###

- Le modalità **Base**, **Standard** e **Premium** supportano applicazioni a 64 bit e a 32 bit.
- Le modalità del piano **Gratuito** e **Condiviso** supportano solo applicazioni a 32 bit.

### Supporto del debugger ###

- Il supporto del debugger è disponibile per le modalità **Gratuita**, **Condivisa** e **Base** a una connessione simultanea per piano App Service.
- Il supporto del debugger è disponibile per le modalità **Standard** e **Premium** a 5 connessioni simultanee per piano App Service.

<a name="OtherFeatures"></a>
## Altre funzionalità

- Per informazioni dettagliate su tutte le altre funzionalità disponibili nei piani di servizio app, inclusi i prezzi e le funzionalità di interesse per tutti gli utenti (compresi gli sviluppatori), vedere [Dettagli prezzi del servizio app](/pricing/details/web-sites/).

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<a name="Next Steps"></a>
## Passaggi successivi

- Per iniziare a usare Azure, vedere la pagina relativa alla [versione di valutazione gratuita di Microsoft Azure](/pricing/free-trial/).
- Per informazioni su prezzi, supporto e contratti di servizio, visitare i collegamenti seguenti:
	
	[Dettagli prezzi dei trasferimenti di dati](/pricing/details/data-transfers/)
	
	[Piani di supporto per Azure](/support/plans/)
	
	[Contratti di servizio](/support/legal/sla/)
	
	[Dettagli prezzi - Database SQL](/pricing/details/sql-database/)
	
	[Dimensioni delle macchine virtuali e dei servizi cloud per Azure][vmsizes]
	
	[Dettagli prezzi del servizio app](/pricing/details/app-service/)
	
	[Dettagli prezzi del servizio app - Connessioni SSL](/pricing/details/web-sites/#ssl-connections)

- Per informazioni sulle procedure consigliate per Azure App Service, inclusa la creazione di un'architettura scalabile e resiliente, vedere l'articolo di blog relativo alle [procedure consigliate per App Web del servizio app di Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Video sul ridimensionamento delle app Web:
	
	- [Quando è necessario ridimensionare i siti Web di Azure - con Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Scalabilità automatica per i siti Web di Azure, pianificata o in base alla CPU - con Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Che cosa accade durante il ridimensionamento dei siti Web di Azure - con Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

<!---HONumber=Oct15_HO3-->