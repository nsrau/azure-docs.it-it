<properties
	pageTitle="Aumentare le prestazioni di un'app in Azure | Microsoft Azure"
	description="Informazioni su come aumentare le prestazioni di un'app nel servizio app di Azure per aggiungere capacità e funzionalità."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="cephalin"/>

# Aumentare le prestazioni di un'app in Azure #

Questo articolo illustra come passare a un piano superiore per un'app nel servizio app di Azure. Sono disponibili due flussi di lavoro per la scalabilità, l'aumento delle prestazioni e l'aumento del numero di istanze. Questo articolo illustra come aumentare le prestazioni.

- [Aumentare le prestazioni](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): è possibile ottenere più CPU, memoria, spazio su disco e altre funzionalità, ad esempio macchine virtuali dedicate, domini e certificati personalizzati, slot di staging, ridimensionamento automatico e altro ancora. È possibile aumentare le prestazioni cambiando il piano tariffario del piano di servizio app a cui appartiene l'app.
- [Aumentare il numero di istanze](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): è possibile incrementare il numero di istanze di macchine virtuali che eseguono l'app. È possibile specificare al massimo 20 istanze, in base al piano tariffario. Gli [ambienti del servizio app](../app-service/app-service-app-service-environments-readme.md) nel piano **Premium** consentono di aumentare ulteriormente il numero di istanze fino a 50. Per altre informazioni sull'aumento del numero di istanze, vedere [Ridimensionare il conteggio delle istanze manualmente o automaticamente](../azure-portal/insights-how-to-scale.md). Questo articolo illustra come usare la scalabilità automatica, ovvero come ridimensionare automaticamente il numero di istanze sulla base di regole e pianificazioni predefinite.

Le impostazioni di scalabilità diventano effettive in pochi secondi e interessano tutte le app nel [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Non richiedono di modificare il codice o ridistribuire l'applicazione.

Per informazioni sui prezzi e le funzionalità dei singoli piani di servizio app, vedere [Dettagli prezzi del servizio app](/pricing/details/web-sites/).

> [AZURE.NOTE] Prima di modificare il livello **Gratuito** di un piano di servizio app, è necessario innanzitutto rimuovere i [limiti di spesa](/pricing/spending-limits/) applicati alla sottoscrizione di Azure. Per visualizzare o modificare le opzioni per la sottoscrizione del servizio app di Microsoft Azure, vedere [Sottoscrizioni di Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## Passare al piano tariffario superiore

1. Accedere al [Portale di Azure][portal] dal browser.

2. Nel pannello dell'app fare clic su **Tutte le impostazioni** e quindi su **Aumenta prestazioni**.

	![Passare al ridimensionamento di un'app di Azure.][ChooseWHP]

4. Scegliere il livello e quindi fare clic su **Seleziona**.

	Al termine dell'operazione, nella scheda **Notifiche** verrà visualizzata la scritta verde lampeggiante **OPERAZIONE RIUSCITA**.

<a name="ScalingSQLServer"></a>
## Risorse correlate alla scalabilità
Se l'applicazione dipende da altri servizi, ad esempio database SQL o Archiviazione di Azure, è anche possibile aumentare le prestazioni di tali risorse in base alle esigenze. Queste risorse non vengono ridimensionate con il piano di servizio app ma separatamente.

1. In **Informazioni di base** fare clic sul collegamento **Gruppo di risorse**.

	![Aumentare le prestazioni delle risorse correlate all'app di Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Nella parte **Riepilogo** del pannello **Gruppo di risorse** fare quindi clic su una delle risorse da ridimensionare. La schermata seguente mostra una risorsa database SQL e una risorsa di Archiviazione di Azure.

	![Passare al pannello gruppo di risorse per aumentare le prestazioni dell'app di Azure](./media/web-sites-scale/ResourceGroup.png)

3. Per una risorsa database SQL, fare clic su **Impostazioni** > **Piano tariffario** per passare al piano tariffario superiore.

	![Aumentare le prestazioni di back-end del database SQL per l'app di Azure](./media/web-sites-scale/ScaleDatabase.png)

	Per l'istanza di database SQL è anche possibile attivare la [replica geografica](../sql-database/sql-database-geo-replication-overview.md).

    Per una risorsa di Archiviazione di Azure, fare clic su **Impostazioni** > **Configurazione** per aumentare le prestazioni delle opzioni di archiviazione.

    ![Aumentare le prestazioni dell'account di Archiviazione di Azure usato dall'app di Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>
## Informazioni sulle funzionalità per sviluppatori
A seconda del piano tariffario, sono disponibili le seguenti funzionalità orientate agli sviluppatori:

### Numero di bit ###

- Le modalità **Base**, **Standard** e **Premium** supportano applicazioni a 64 bit e a 32 bit.
- Le modalità del piano **Gratuito** e **Condiviso** supportano solo applicazioni a 32 bit.

### Supporto del debugger ###

- Il supporto del debugger è disponibile per i piani **Gratuito**, **Condiviso** e **Basic** con una connessione per piano di servizio app.
- Il supporto del debugger è disponibile per i piani **Standard** e **Premium** con 5 connessioni simultanee per piano di servizio app.

<a name="OtherFeatures"></a>
## Informazioni sulle altre funzionalità

- Per informazioni dettagliate su tutte le altre funzionalità disponibili nei piani di servizio app, inclusi i prezzi e le funzionalità di interesse per tutti gli utenti (compresi gli sviluppatori), vedere [Dettagli prezzi del servizio app](/pricing/details/web-sites/).

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<a name="Next Steps"></a>
## Passaggi successivi

- Per iniziare a usare Azure, vedere la pagina relativa alla [versione di valutazione gratuita di Microsoft Azure](/pricing/free-trial/).
- Per informazioni su prezzi, supporto e contratti di servizio, visitare i collegamenti seguenti.

	[Dettagli prezzi dei trasferimenti di dati](/pricing/details/data-transfers/)

	[Piani di supporto per Azure](/support/plans/)

	[Contratti di servizio](/support/legal/sla/)

	[Dettagli prezzi - Database SQL](/pricing/details/sql-database/)

	[Dimensioni delle macchine virtuali e dei servizi cloud per Azure][vmsizes]

	[Dettagli prezzi del servizio app](/pricing/details/app-service/)

	[Dettagli prezzi del servizio app - Connessioni SSL](/pricing/details/web-sites/#ssl-connections)

- Per informazioni sulle procedure consigliate per Servizio app di Azure, inclusa la creazione di un'architettura scalabile e resiliente, vedere il post di blog relativo alle [procedure consigliate per le app Web del servizio app di Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Per i video sul ridimensionamento delle app del servizio app, vedere le risorse seguenti:

	- [Quando è necessario ridimensionare i siti Web di Azure - con Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Scalabilità automatica per i siti Web di Azure, pianificata o in base alla CPU - con Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Che cosa accade durante il ridimensionamento dei siti Web di Azure - con Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)


<!-- LINKS -->
[vmsizes]: /pricing/details/app-service/
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!---HONumber=AcomDC_0810_2016-->
