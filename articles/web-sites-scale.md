<properties 
	pageTitle="Scalare un'app Web in Azure App Service" 
	description="Informazioni su come scalare un'app Web in Azure App Service, incluso il ridimensionamento automatico." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Scalare un'app Web in Azure App Service #

Per migliorare le prestazioni e la velocità effettiva delle app Web in Microsoft Azure, è possibile utilizzare il [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) e scalare il piano [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) dalla modalità **Gratuita** a quella **Condivisa**, **Base**, **Standard** o **Premium**. 

Scalare le app Web di Azure implica due azioni correlate: modifica della modalità del piano App Service a un livello più elevato e configurazione di determinate impostazioni dopo essere passati a un livello di servizio più elevato. In questo articolo vengono illustrati entrambi gli argomenti. Livelli di servizio più elevati quali le modalità **Standard** e **Premium** offrono maggiore potenza e flessibilità nella determinazione della modalità di utilizzo delle risorse in Azure.

È possibile eseguire le operazioni di modifica della modalità e di configurazione nella scheda Scale del portale di gestione. È possibile aumentare o ridurre le risorse in base alle esigenze. Tali modifiche diventano effettive in pochi secondi e interessano tutte le app Web nel piano App Service. Non richiedono alcuna modifica del codice o la ridistribuzione delle applicazioni.

Per informazioni sui piani App Service, vedere [Cos'è un piano App Service?](web-sites-web-hosting-plan-overview.md) e [Panoramica dettagliata sui piani Azure App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md). Per informazioni sulle tariffe e sulle funzionalità dei singoli piani App Service, vedere [Dettagli sulle tariffe App Service](/pricing/details/web-sites/).  

> [AZURE.NOTE] Prima di passare un'app Web dalla modalità **Gratuita** a **Base**, **Standard** o **Premium**, è necessario innanzitutto rimuovere i limiti di spesa applicati alla sottoscrizione per Azure App Service. Per visualizzare o modificare le opzioni per la sottoscrizione di Microsoft Azure App Service, vedere [Sottoscrizioni Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Passaggio alla modalità Condivisa o Base
<!-- ===================================== -->

1. Nel browser, aprire il [portale di Azure][portal].
	
2. Nel pannello dell'app Web, fare clic su **Tutte le impostazioni**, quindi fare clic su **Scala**, su **Aggiorna da un piano Gratuita per aggiungere istanze e ottenere prestazioni migliori**.
	
	![Scegliere il piano][ChooseWHP]
	
4. Nel pannello **Scegliere il livello di prezzo**, scegliere la modalità **Condivisa** o **Base**, quindi fare clic su **Seleziona**.
	
	Una volta completata l'operazione, nella scheda**Notifiche** lampeggerà la scritta **OPERAZIONE RIUSCITA**. 
	
5. Far scorrere la barra **Istanza** da sinistra a destra per aumentare il numero delle istanze, quindi fare clic su **Salva** nella barra dei comandi. L'opzione relativa alle dimensioni delle istanze non è disponibile nella modalità **condivisa**. Per ulteriori informazioni sulle dimensioni di queste istanze, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Microsoft Azure][vmsizes].
	
	![Dimensioni delle istanze per la modalità base][ChooseBasicInstances]
	
	Una volta completata l'operazione, nella scheda **Notifiche** lampeggerà la scritta **OPERAZIONE RIUSCITA**. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Passaggio alla modalità Standard o Premium
<!-- ================================= -->

> [AZURE.NOTE] Prima di passare alla modalità del piano App Service **Standard** o **Premium**, è necessario rimuovere i limiti di spesa applicati alla sottoscrizione per Microsoft Azure App Service. In caso contrario, qualora si raggiungano i limiti di spesa prima della fine del periodo di fatturazione, l'app Web potrebbe non essere più disponibile. Per visualizzare o modificare le opzioni per la sottoscrizione di Microsoft Azure App Service, vedere [Sottoscrizioni Microsoft Azure][azuresubscriptions].

1. Per scalare alla modalità **Standard** o **Premium**, attenersi agli stessi passaggi iniziali del passaggio a **Condivisa** o **Base**, quindi scegliere la modalità **Standard** or **Premium** in **Scegliere il livello di prezzo** e fare clic su **Seleziona**. 
	
	Una volta completata l'operazione, nella scheda **Notifiche** lampeggerà la scritta verde **OPERAZIONE RIUSCITA** e verrà abilitata la **Modalità ridimensionamento automatico**.
	
	![Passare alla modalità Standard o Premium][ScaleStandard]
	
	È possibile comunque far scorrere la barra **Istanza** per scalare ad altre istanze, come nella modalità **Base** illustrata in precedenza. Tuttavia, si apprenderà l'uso della **Modalità di ridimensionamento automatico**. 
	
2. In **Modalità di ridimensionamento automatico**, selezionare **Prestazioni** per eseguire il ridimensionamento automatico in base alle metriche delle prestazioni.
	
	![Modalità di ridimensionamento automatico impostato su Prestazioni][Ridimensiona automaticamente]
	
3. In **Instance Range**, spostare le due barre di scorrimento per definire il numero minimo e quello massimo di istanze da scalare automaticamente per il piano App Service. Per questa esercitazione, spostare la barra di scorrimento relativa al massimo su **6** istanze.
	
4. Fare clic su **Salva** nella barra dei comandi.
	
4. In **Metriche di destinazione**, fare clic su **>** per configurare le regole di ridimensionamento automatico per la metrica predefinita.  
	
	![Impostare le metriche di destinazione][SetTargetMetrics]
	
	È possibile configurare le regole di ridimensionamento automatico per le diverse metriche di prestazione, incluse CPU, memoria, coda del disco, coda HTTP e flusso di dati. Qui verrà configurato il ridimensionamento automatico per la percentuale di CPU in base a quanto indicato di seguito:
	
	- Aumentare di 1 istanza se la CPU è al di sopra del 70% negli ultimi 10 minuti
	- Aumentare di 3 istanze se la CPU è al di sopra del 90% negli ultimi 5 minuti
	- Ridurre di 1 istanza se la CPU è al di sotto del 50% negli ultimi 30 minuti 
	
	
4. Lasciare la casella a discesa **Metrica** come **Percentuale CPU**.
	
5. In **Regole relative al ridimensionamento**, configurare la prima regola impostando **Condizione** su **Maggiore**, **Soglia** su **70** (%), **Negli ultimi** su **10** (minuti), **Aumenta di** su **1** (istanza) e **Raffreddamento** su **10** (minuti). 
	
	![Impostare la prima regola di ridimensionamento automatico][SetFirstRule]
	
	>[AZURE.NOTE] Nell'impostazione **Raffreddamento** viene specificato per quanto tempo la regola deve attendere prima eseguire un nuovo ridimensionamento.
	
6. Fare clic su **Aggiungi regola di ridimensionamento**, quindi configurare la seconda regola impostando **Condizione** su **Maggiore**, **Soglia** su **90** (%), **Negli ultimi** su **1** (minuti), **Aumenta di** su **3** (istanze) e **Raffreddamento** su **1** (minuti).
	
	![Imposta seconda regola di ridimensionamento automatico][SetSecondRule]
	
5. In **Regole di ridimensionamento**, configurare la terza regola impostando **Condizione** su **Inferiore**, **Soglia** su **50** (%), **Negli ultimi** su **30** (minuti), **Riduci di** su **1** (istanza) e **Raffreddamento** su **60** (minuti). 
	
	![Impostare la terza regola di ridimensionamento automatico][SetThirdRule]
	
7. Fare clic su **Salva** nella barra dei comandi. La regola di ridimensionamento automatico dovrebbe essere riflessa nel pannello **Scala**. 
	
	![Risultato dell'impostazione della regola di ridimensionamento automatico][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Ridimensionamento di un database SQL Server connesso all'app Web
Se si dispone di uno o più database SQL Server collegati all'app Web (a prescindere dalla modalità del piano App Service), è possibile scalarli in base alle proprie esigenze.

1. Per scalare uno dei database collegati, aprire il pannello dell'app Web nel [portale di Azure][portal]. Nell'elenco a discesa comprimibile **Essentials**, fare clic sul collegamento **Gruppo di risorse**. Quindi nella sezione **Riepilogo** del pannello del gruppo di risorse, fare clic su uno dei database collegati.

	![Database collegati][ResourceGroup]
	
2. Nel pannello Database SQL collegato, fare clic su **Livello di prezzo** selezionare uno dei livelli in base ai requisiti di prestazioni e fare clic su **Seleziona**. 
	
	![Scalare il Database SQL][ScaleDatabase]
	
3. È inoltre possibile configurare la replica geografica per aumentare le funzionalità di elevata disponibilità e ripristino di emergenza di Database SQL. A tal fine, fare clic su **Replica geografica**.
	
	![Configurare la replica geografica per Database SQL][GeoReplication]

<a name="devfeatures"></a>
## Funzionalità per sviluppatori
A seconda della modalità dell'app Web, sono disponibili le seguenti funzionalità orientate agli sviluppatori:

### Numero di bit ###

- Le modalità **Base**, **Standard** e **Premium** supportano applicazioni a 64 bit e a 32 bit.
- Le modalità di piano **Gratuita** e **Condivisa** supportano unicamente applicazioni a 32 bit.

### Supporto del debugger ###

- Il supporto del debugger è disponibile per le modalità **Gratuita**, **Condivisa** e **Base** a una connessione simultanea per piano App Service.
- Il supporto del debugger è disponibile per le modalità **Standard** e **Premium** a 5 connessioni simultanee per piano App Service.

<a name="OtherFeatures"></a>
## Altre funzionalità

### Monitoraggio degli endpoint Web ###

- Il monitoraggio degli endpoint Web è disponibile nelle modalità **Base**, **Standard** e **Premium**. Per ulteriori informazioni sul monitoraggio degli endpoint Web, vedere [Come monitorare le app Web](web-sites-monitor.md).

- Per informazioni dettagliate su tutte le rimanenti funzionalità nei piani App Service, incluse le tariffe e le funzionalità di interesse per tutti gli utenti (inclusi gli sviluppatori), vedere [Dettagli sulle tariffe di App Service](/pricing/details/web-sites/).

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app ](http://go.microsoft.com/fwlink/?LinkId=523751) dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<a name="Next Steps"></a>	
## Passaggi successivi

- Per iniziare a usare Azure, vedere [Versione di valutazione gratuita di Microsoft Azure](/pricing/free-trial/).
- Per informazioni su prezzi, supporto e contratti di servizio, andare a i collegamenti seguenti:
	
	[Dettagli delle tariffe per il trasferimento di dati](/pricing/details/data-transfers/)
	
	[Piani di supporto di Microsoft Azure](/support/plans/)
	
	[Contratti di servizio](/support/legal/sla/)
	
	[Dettagli relativi ai prezzi di Database SQL](/pricing/details/sql-database/)
	
	[Dimensioni delle macchine virtuali e dei servizi cloud per Microsoft Azure][vmsizes]
	
	[Dettagli dei prezzi di App Service](/pricing/details/web-sites/)
	
	[Dettagli dei prezzi di App Service - Connessioni SSL](/pricing/details/web-sites/#ssl-connections)

- Per informazioni sulle procedure consigliate per Azure App Service, inclusa la creazione di un'architettura scalabile e resiliente, vedere l'articolo del blog relativo alle [procedure consigliate per le app Web di Azure App Service](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Video sul ridimensionamento delle app Web:
	
	- [video su quando eseguire il ridimensionamento dei siti Web di Azure, con Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Video sul ridimensionamento automatico dei siti Web di Azure, pianificato o in base alla CPU, con Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Video sulla modalità di ridimensionamento dei siti Web di Azure, con Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Ridimensiona automaticamente]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!--HONumber=49-->