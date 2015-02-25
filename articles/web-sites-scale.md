<properties 
	pageTitle="Come scalare siti Web" 
	description="obbligatorio" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Come scalare siti Web #

Per aumentare le prestazioni e la velocità effettiva per i siti Web in Microsoft Azure, è possibile usare il portale di gestione di Azure per scalare la modalità del piano di hosting Web da Gratuito a Condiviso, Basic o Standard. 

La scalabilità verticale nei siti Web di Azure prevede due azioni correlate: la prima consiste nel modificare la modalità del piano di hosting Web a un livello di servizio superiore e la seconda consiste nel configurare determinate impostazioni in seguito al passaggio al livello di servizio superiore. In questo articolo vengono illustrati entrambi gli argomenti. I livelli di servizi superiori, come ad esempio la modalità standard, offrono maggiore potenza e flessibilità nella determinazione della modalità di utilizzo delle risorse in Azure.

È possibile eseguire le operazioni di modifica della modalità e di configurazione nella scheda Scale del portale di gestione. È possibile aumentare o ridurre le risorse in base alle esigenze. Le modifiche diventano effettive in pochi secondi e interessano tutti i siti Web nel piano di hosting Web. Non richiedono alcuna modifica del codice o la ridistribuzione delle applicazioni.

Per informazioni sui piani di hosting Web, vedere [Informazioni sui piani di hosting Web](http://azure.microsoft.com/it-it/documentation/articles/web-sites-web-hosting-plan-overview/) e [Informazioni dettagliate sui piani di hosting Web di Siti Web di Azure](http://www.azure.microsoft.com/it-it/Documentation/Articles/azure-web-sites-web-hosting-plans-in-depth-overview/). Per informazioni sui prezzi e le funzionalità dei singoli piani di hosting Web, vedere la pagina relativa ai [dettagli sui prezzi di Siti Web](http://www.windowsazure.com/it-it/pricing/details/web-sites/).  

> [AZURE.NOTE] Prima di passare dalla modalità del piano di hosting Web **Free** alla modalità **Basic** o **Standard**, è necessario rimuovere i limiti di spesa applicati alla sottoscrizione per Siti Web di Azure. Per visualizzare o modificare le opzioni per la sottoscrizione di Siti Web di Microsoft Azure, vedere [Sottoscrizioni Microsoft Azure][azuresubscriptions].

Contenuto dell'articolo:

- [Passaggio alla modalità del piano condiviso o base](#scalingsharedorbasic)
- [Passaggio alla modalità del piano standard](#scalingstandard)
- [Ridimensionamento di un database di SQL Server collegato al sito](#ScalingSQLServer)
- [Funzionalità per sviluppatori](#devfeatures)
- [Altre funzionalità](#OtherFeatures)

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Passaggio alla modalità del piano condiviso o base
<!-- ===================================== -->

1. Nel browser aprire il [portale di gestione][portal].
	
2. Nella scheda **Siti Web** selezionare il sito Web desiderato.
	
	![Selecting a website][SelectWebsite]
	
3. Fare clic sulla scheda **Scala**.
	
	![The scale tab][SelectScaleTab]
	
4. Nella sezione **Modalità piano di hosting Web** selezionare **CONDIVISO** o **BASIC**. Nell'esempio riportato nell'immagine viene scelta l'opzione Base.
	
	![Choose Web Hosting Plan][ChooseWHP]
	
	Nella sezione **Siti del piano di hosting Web** è visualizzato un breve elenco di siti nel piano corrente. Tutti i siti nel piano corrente passeranno alla modalità del piano di hosting Web selezionata.
	
5. Nella sezione **Capacità** scegliere un valore per **Dimensioni istanze**. Le opzioni disponibili sono **Piccola**, **Media** o **Grande**. L'opzione relativa alle dimensioni delle istanze non è disponibile nella modalità condivisa. Per altre informazioni sulle dimensioni delle istanze, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure][vmsizes].
	
	![Instance size for Basic mode][ChooseBasicInstanceSize]
	
6. Usare il dispositivo di scorrimento per scegliere il valore desiderato per **Conteggio istanze**.
	
	![Instance count for Basic mode][ChooseBasicInstanceCount]
	
7. Nella barra dei comandi scegliere **Salva**. 
	
	![Save button][SaveButton]
 	
	> [AZURE.NOTE] È possibile configurare e salvare le impostazioni di **Piano di hosting Web**, **Dimensioni istanze** e **Conteggio istanze** separatamente, se lo si desidera.
	
8. Un messaggio di conferma ricorderà all'utente che anche gli altri siti nello stesso piano di hosting Web del sito corrente passeranno alla nuova modalità. Scegliere **Sì** per completare la modifica. 
	
	Nell'esempio la modalità del piano è stata impostata su **Basic**:
	
	![Plan change complete][BasicComplete]
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Passaggio alla modalità del piano Standard
<!-- ================================= -->

> [AZURE.NOTE] Prima di passare alla modalità del piano di hosting Web Standard, è necessario rimuovere i limiti di spesa applicati alla sottoscrizione per Siti Web di Microsoft Azure. In caso contrario, qualora si raggiungano i limiti di spesa prima della fine del periodo di fatturazione, il sito potrebbe non essere più disponibile. Per visualizzare o modificare le opzioni per la sottoscrizione di Siti Web di Microsoft Azure, vedere [Sottoscrizioni Microsoft Azure][azuresubscriptions].

1. Per passare alla modalità standard, attenersi ai passaggi iniziali per il passaggio alla modalità del piano condiviso o base e quindi scegliere **Standard** per **Modalità piano di hosting Web**. 
	
	![Choose Standard Plan][ChooseStandard]
	
	Come in precedenza, nella sezione **Siti del piano di hosting Web** è visualizzato un breve elenco di siti nel piano corrente. In questo caso, tutti i siti nel piano corrente passeranno alla modalità del piano di hosting Web standard.
	
2. Selezionando **Standard** è possibile espandere la sezione **Capacità** per visualizzare le opzioni **Dimensioni istanze** e **Conteggio istanze** che sono disponibili anche nella modalità base. Le opzioni **Modifica impostazioni di scalabilità per la pianificazione** e **Scala in base alla metrica** sono disponibili solo nella modalità standard.
	
	![Capacity section in Standard][CapacitySectionStandard]
	
3. Configurare il valore **Dimensioni istanze**. Le opzioni disponibili sono **Piccola**, **Media** o **Grande**.
	
	![Choose instance size][ChooseInstanceSize]
	
	Per altre informazioni sulle dimensioni delle istanze, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure][vmsizes].
	
4. Se si desidera usufruire della scalabilità automatica delle risorse in base ad esempio al giorno e alla notte, ai giorni della settimana e al fine settimana e/o in base a date e ore specifiche, scegliere **Imposta ore di pianificazione** nell'opzione **Modifica impostazioni di scalabilità per la pianificazione**.
	
	![Set up schedule times][SetUpScheduleTimesButton]
	
5. Nella finestra di dialogo **Imposta ore di pianificazione** sono disponibili molte scelte di configurazione utili.
	
	![SetUpScheduleTimesDialog][SetUpScheduleTimesDialog]
	
6. In **Pianificazioni ricorrenti** selezionare **Impostazioni di scalabilità diverse per il giorno e la notte** e/o **Impostazioni di scalabilità diverse per i giorni della settimana e i fine settimana** per ridimensionare le risorse in base a pianificazioni distinte per il giorno e la notte o i giorni della settimana e il fine settimana.
	
	> [AZURE.NOTE] Ai fini di questa funzionalità, il fine settimana inizia alla fine del venerdì (alle 20.00 per impostazione predefinita) e termina all'inizio del lunedì (alle 8.00 per impostazione predefinita). Nel profilo del fine settimana vengono usati gli stessi orari di inizio e di fine del giorno specificati nell'impostazione **Ora**.
	
7. In **Ora** scegliere un'ora di inizio e un'ora di fine per il giorno con incrementi di mezz'ora e un fuso orario. Per impostazione predefinita il giorno inizia alle 8.00 e termina alle 20.00. Nel fuso orario selezionato si tiene conto dell'ora legale. 
	
8. In **Date specifiche** è possibile creare uno o più intervalli di tempo denominati per i quali si desidera ridimensionare le risorse. Può essere necessario, ad esempio, fornire risorse aggiuntive in occasione di eventi quali vendite o lanci di prodotti durante i quali è probabile che si verifichino picchi del traffico Web.
	
9. Dopo aver definito queste impostazioni, fare clic su **OK** per chiudere la finestra di dialogo **Imposta ore di pianificazione**.
	
10.   Nella casella **Modifica impostazioni di scalabilità per la pianificazione** vengono visualizzate le pianificazioni o gli eventi configurabili in base alle modifiche apportate. Selezionare una pianificazione ricorrente o date specifiche da configurare. 
	
	![Edit scale settings for schedule][EditScaleSettingsForSchedule]
	
	A questo punto è possibile usare le funzionalità **Scala in base alla metrica** e **Conteggio istanze** per ottimizzare la scalabilità delle risorse per ogni pianificazione selezionata. 
	
11.  Per regolare dinamicamente il numero di istanze usate dal sito Web in base alle variazioni del relativo carico, abilitare l'opzione **Scala in base alla metrica** scegliendo **CPU**.
	
	![Scale By Metric][ScaleByMetric]
	
	Nel grafico è mostrato il numero di istanze utilizzate nel corso della settimana precedente. È quindi possibile usare il grafico per monitorare l'attività di aggiunta o rimozione di istanze.
	
12. **Scala in base alla metrica** consente di modificare la funzionalità **Conteggio istanze** per impostare il numero minimo e massimo di macchine virtuali da usare per la scalabilità automatica. Azure non supererà né scenderà al di sotto dei limiti impostati.
	
	![Instance count][InstanceCount]
	
13. **Scala in base alla metrica** abilita anche l'opzione **CPU destinazione** che consente di specificare un intervallo di destinazione per l'utilizzo della CPU. Questo intervallo rappresenta l'uso della CPU medio per il sito Web. Per mantenere l'uso della CPU del sito Web in questo intervallo verranno aggiunte o rimosse istanze standard.
	
€
	
	**Nota**: Quando la funzionalità **Scala in base alla metrica** è abilitata, la CPU del sito Web viene controllata una volta ogni cinque minuti e vengono aggiunte istanze in base alle esigenze di quel momento. Se l'uso della CPU è minimo, tramite Microsoft Azure le istanze verranno rimosse una volta ogni due ore per garantire le prestazioni del sito Web. In genere, è appropriato impostare il conteggio minimo di istanze su 1. Tuttavia, se si verificano picchi improvvisi di uso nel sito Web, assicurarsi di disporre di un numero minimo di istanze sufficiente per gestire il carico. Ad esempio, se si verifica un picco di traffico improvviso durante un intervallo di 5 minuti prima che venga controllato l'uso della CPU da parte di Microsoft Azure, è possibile che il sito non sia disponibile durante quell'intervallo. Se si prevede una quantità di traffico elevata e improvvisa, impostare il conteggio minimo di istanze su un valore più elevato per prevenire questi picchi. 
	
14. Dopo aver apportato le modifiche a tutte le pianificazioni nell'elenco **Modifica impostazioni di scalabilità per la pianificazione** fare clic sull'icona **Salva** nella barra dei comandi nella parte inferiore della pagina per salvare contemporaneamente tutte le impostazioni.

> [AZURE.NOTE] Nel [portale di anteprima di Azure](https://portal.azure.com/) è possibile scalare non solo la percentuale di CPU, ma anche le ulteriori metriche relativa a Percentuale memoria, Lunghezza coda disco, Lunghezza coda HTTP, Dati in entrata e Dati in uscita. È inoltre possibile creare una o più regole Scalabilità verticale e Scalabilità orizzontale che offrono un controllo ancora più personalizzato sul ridimensionamento. Per altre informazioni, vedere [Come scalare un sito Web](http://azure.microsoft.com/it-it/documentation/articles/insights-how-to-scale/) nella documentazione del portale di anteprima di Azure.

<a name="ScalingSQLServer"></a>
##Ridimensionamento di un database di SQL Server collegato al sito	
Se sono presenti uno o più database di SQL Server collegati al sito Web (indipendentemente dalla modalità del piano di hosting Web), questi saranno visualizzati nella sezione **Risorse collegate** nella parte inferiore della pagina Ridimensionamento.

1. Per ridimensionare uno dei database, nella sezione **Risorse collegate** fare clic sul collegamento **Gestisci ridimensionamento per questo database** accanto al nome del database.
	
	![Linked database][LinkedResources]
	
2. Il collegamento reindirizzerà l'utente alla scheda SQL Server del portale di gestione di Azure in cui è possibile configurare le opzioni **Edizione** e **Dimensione massima** del database.
	
	![Scale your SQL Server database][ScaleDatabase]
	
	In **Edizione** scegliere **BASIC**, **STANDARD** o **PREMIUM** a seconda della capacità di archiviazione necessaria. Per informazioni sul futuro delle edizioni **Web** e **BUSINESS**, vedere [Domande frequenti sull'interruzione delle edizioni Web e Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).
	
	Il valore scelto per **Dimensioni massime** specifica un limite massimo per il database. Gli addebiti per il database dipendono dalla quantità di dati effettivamente archiviati e pertanto la modifica della proprietà **Dimensioni massime** non ha effetto sugli addebiti. Per altre informazioni, vedere [Account e fatturazione nel database SQL di Azure][SQLaccountsbilling].

<a name="devfeatures"></a>
## Funzionalità per sviluppatori
A seconda della modalità del piano di hosting Web scelta, sono disponibili le funzionalità orientate agli sviluppatori seguenti:

**Numero di bit**

- Le modalità del piano base e standard supportano applicazioni a 64 e 32 bit.
- Le modalità del piano gratuita e condivisa supportano solo applicazioni a 32 bit.

**Supporto del debugger**

- Il supporto del debugger è disponibile per le modalità del piano di hosting Web gratuita, condivisa e base con 1 connessione simultanea per ogni applicazione.
- Il supporto del debugger è disponibile per la modalità del piano di hosting Web standard con 5 connessioni simultanee per ogni applicazione.

<a name="OtherFeatures"></a>
## Altre funzionalità

**Monitoraggio degli endpoint Web**

- Il monitoraggio degli endpoint Web è disponibile nelle modalità del piano di hosting Web standard e base. Per altre informazioni sul monitoraggio degli endpoint, vedere [Come monitorare Siti Web](http://www.windowsazure.com/it-it/documentation/articles/web-sites-monitor/).

- Per informazioni dettagliate su tutte le altre funzionalità disponibili nei piani di hosting Web, inclusi i prezzi e le funzionalità di interesse per tutti gli utenti (compresi gli sviluppatori), vedere la pagina relativa ai [dettagli sui prezzi di Siti Web](http://www.windowsazure.com/it-it/pricing/details/web-sites/).

<a name="Next Steps"></a>	
## Passaggi successivi

- Per iniziare a usare Azure, vedere la pagina relativa alla [versione di valutazione gratuita di Microsoft Azure](http://azure.microsoft.com/it-it/pricing/free-trial/).

- Per informazioni su prezzi, supporto e contratti di servizio, visitare i collegamenti seguenti:
	
	[Dettagli prezzi dei trasferimenti di dati](http://www.windowsazure.com/it-it/pricing/details/data-transfers/)
	
	[Piani di supporto per Azure](http://www.windowsazure.com/it-it/support/plans/)
	
	[Contratti di servizio](http://www.windowsazure.com/it-it/support/legal/sla/)
	
	[Dettagli prezzi - Database SQL](http://www.windowsazure.com/it-it/pricing/details/sql-database/)
	
	[Dimensioni delle macchine virtuali e dei servizi cloud per Azure][vmsizes]
	
	[Dettagli sui prezzi di Siti Web](http://www.windowsazure.com/it-it/pricing/details/web-sites/)
	
	[Dettagli sui prezzi di Siti Web - Connessioni SSL](http://www.windowsazure.com/it-it/pricing/details/web-sites/#ssl-connections)

- Per informazioni sulle procedure consigliate per Siti Web di Azure, inclusa la creazione di un'architettura scalabile e resiliente, vedere l'articolo di blog relativo alle [procedure consigliate per Siti Web di Microsoft Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Video sul ridimensionamento di Siti Web di Azure:
	
	[Quando è necessario ridimensionare i siti Web di Azure - con Stefan Schackow](http://www.windowsazure.com/it-it/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[Scalabilità automatica per i siti Web di Azure, pianificata o in base alla CPU - con Stefan Schackow](http://www.windowsazure.com/it-it/documentation/videos/auto-scaling-azure-web-sites/)

	[Che cosa accade durante il ridimensionamento dei siti Web di Azure - con Stefan Schackow](http://www.windowsazure.com/it-it/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://manage.windowsazure.com/

<!-- IMAGES -->
[SelectWebsite]: ./media/web-sites-scale/01SelectWebSite.png
[SelectScaleTab]: ./media/web-sites-scale/02SelectScaleTab.png

[ChooseWHP]: ./media/web-sites-scale/03aChooseWHP.png
[ChooseBasicInstanceSize]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
[ChooseBasicInstanceCount]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ChooseStandard]: ./media/web-sites-scale/07ChooseStandard.png
[CapacitySectionStandard]: ./media/web-sites-scale/08CapacitySectionStandard.png
[ChooseInstanceSize]: ./media/web-sites-scale/09ChooseInstanceSize.png
[SetUpScheduleTimesButton]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
[SetUpScheduleTimesDialog]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
[EditScaleSettingsForSchedule]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
[ScaleByMetric]: ./media/web-sites-scale/13ScaleByMetric.png
[InstanceCount]: ./media/web-sites-scale/14InstanceCount.png
[TargetCPU]: ./media/web-sites-scale/15TargetCPU.png
[LinkedResources]: ./media/web-sites-scale/16LinkedResources.png
[ScaleDatabase]: ./media/web-sites-scale/17ScaleDatabase.png


<!--HONumber=42-->
