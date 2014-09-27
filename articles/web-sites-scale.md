<properties linkid="manage-scenarios-how-to-scale-websites" urlDisplayName="How to scale" pageTitle="How to scale web sites - Microsoft Azure service management" metaKeywords="Azure scaling web sites" description="Learn how to scale web sites in Azure to use Free, Shared, Basic and Standard web hosting plans." metaCanonical="" services="web-sites" documentationCenter="" title="How to Scale Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Come applicare la scalabilità ai siti Web
============

Per aumentare le prestazioni e la velocità effettiva per i siti Web in Microsoft Azure, è possibile utilizzare il portale di gestione di Azure per scalare la modalità del piano di hosting Web da gratuita a condivisa, a base o standard.

La scalabilità verticale nei siti Web di Azure prevede due operazioni correlate: la prima consiste nel modificare la modalità del piano di hosting Web a un livello di servizio superiore e la seconda consiste nel configurare determinate impostazioni in seguito al passaggio al livello di servizio superiore. In questo articolo vengono illustrati entrambi gli argomenti. I livelli di servizi superiori, come ad esempio la modalità standard, offrono maggiore potenza e flessibilità nella determinazione della modalità di utilizzo delle risorse in Azure.

È possibile eseguire le operazioni di modifica della modalità e di configurazione nella scheda Scale del portale di gestione. È possibile aumentare o ridurre le risorse in base alle esigenze. Le modifiche diventano effettive in pochi secondi e interessano tutti i siti Web nel piano di hosting Web. Non richiedono alcuna modifica del codice o la ridistribuzione delle applicazioni.

Per informazioni sui piani di hosting Web, vedere l'articolo relativo ai [piani di hosting Web di Siti Web di Azure](http://go.microsoft.com/fwlink/?LinkId=9845584). Per informazioni sui prezzi e le funzionalità dei singoli piani di hosting Web, vedere l'articolo [Dettagli prezzi - Siti Web](http://www.windowsazure.com/it-it/pricing/details/web-sites/).

> [WACOM.NOTE] Prima di passare dalla modalità del piano di hosting Web **Free** alla modalità **Basic** o **Standard** è innanzitutto necessario rimuovere i limiti di spesa applicati alla sottoscrizione per il sito Web. Per visualizzare o modificare le opzioni per la sottoscrizione di Siti Web di Microsoft Azure nella pagina relativa alle [sottoscrizioni di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=235288).

Contenuto dell'articolo:

-   [Passaggio alla modalità del piano condiviso o base](#scalingsharedorbasic)
-   [Passaggio alla modalità del piano standard](#scalingstandard)
-   [Ridimensionamento di un database di SQL Server collegato al sito](#ScalingSQLServer)
-   [Funzionalità per sviluppatori](#devfeatures)
-   [Altre funzionalità](#OtherFeatures)

## Passaggio alla modalità del piano condiviso o base

1.  Accedere al [portale di gestione](https://manage.windowsazure.com/) dal browser.

2.  Nella scheda **Web Sites** selezionare il sito Web desiderato.

    ![Selezione di un sito Web](./media/web-sites-scale/01SelectWebSite.png)

3.  Fare clic sulla scheda **Scale**.

    ![Scheda Scalabilità](./media/web-sites-scale/02SelectScaleTab.png)

4.  Nella sezione **Web Hosting Plan Mode** selezionare **SHARED** o **BASIC**. Nell'esempio riportato nell'immagine viene scelta l'opzione Base.

    ![Scelta del piano di hosting Web](./media/web-sites-scale/03aChooseWHP.png)

    Nella sezione **Web Hosting Plan Sites** è visualizzato un breve elenco di siti nel piano corrente. Tutti i siti nel piano corrente passeranno alla modalità del piano di hosting Web selezionata.

5.  Nella sezione **Capacity** scegliere un valore per **Instance Size**. Le opzioni disponibili sono **Small**, **Medium** o **Large**. L'opzione relativa alle dimensioni delle istanze non è disponibile nella modalità condivisa. Per ulteriori informazioni sulle dimensioni delle istanze, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=309169).

    ![Dimensioni delle istanze per la modalità base](./media/web-sites-scale/03bChooseBasicInstanceSize.png)

6.  Utilizzare il dispositivo di scorrimento per scegliere il valore desiderato per **Instance Count**.

    ![Conteggio istanze per la modalità base](./media/web-sites-scale/04ChooseBasicInstanceCount.png)

7.  Nella barra dei comandi fare clic su **Save**.

    ![pulsante Salva](./media/web-sites-scale/05SaveButton.png)

    > [WACOM.NOTE] È possibile configurare e salvare le impostazioni di **Web Hosting Plan**, **Instance Size** e **Instance Count** separatamente, se lo si desidera.

8.  Un messaggio di conferma ricorderà all'utente che anche gli altri siti nello stesso piano di hosting Web del sito corrente passeranno alla nuova modalità. Scegliere **Yes** per completare la modifica.

    Nell'esempio fornito, la modalità del piano è stata impostata su **Basic**:

    ![Cambio piano completato](./media/web-sites-scale/06BasicComplete.png)

## Passaggio alla modalità del piano standard

> [WACOM.NOTE] Prima di passare alla modalità del piano di hosting Web standard, è necessario rimuovere i limiti di spesa applicati alla sottoscrizione per Siti Web di Microsoft Azure. In caso contrario, qualora si raggiungano i limiti di spesa prima della fine del periodo di fatturazione, il sito potrebbe non essere più disponibile. Per visualizzare o modificare le opzioni per la sottoscrizione di Siti Web di Microsoft Azure nella pagina relativa alle [sottoscrizioni di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=235288).

1.  Per passare alla modalità standard, attenersi ai passaggi iniziali per il passaggio alla modalità condivisa o base e quindi scegliere **Standard** per **Web Hosting Plan Mode**.

    ![Scelta del piano standard](./media/web-sites-scale/07ChooseStandard.png)

    Come in precedenza, nella sezione **Web Hosting Plan Sites** è visualizzato un breve elenco di siti nel piano corrente. In questo caso, tutti i siti nel piano corrente passeranno alla modalità del piano di hosting Web standard.

2.  Selezionando **Standard** è possibile espandere la sezione **Capacity** per visualizzare le opzioni **Instance Size** e **Instance Count** che sono disponibili anche nella modalità base. Le opzioni **Edit Scale Settings for Schedule** e **Scale by Metric** sono disponibili solo nella modalità standard.

    ![Sezione Capacità in Standard](./media/web-sites-scale/08CapacitySectionStandard.png)

3.  Scegliere un valore per **Instance Size**. Le opzioni disponibili sono **Small**, **Medium** o **Large**.

    ![Scelta delle dimensioni delle istanze](./media/web-sites-scale/09ChooseInstanceSize.png)

    Per ulteriori informazioni sulle dimensioni delle istanze, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=309169).

4.  Se si desidera usufruire della scalabilità automatica delle risorse in base ad esempio al giorno e alla notte, ai giorni della settimana e al fine settimana e/o in base a date e ore specifiche, scegliere **Set up schedule times** nell'opzione **Edit Scale Settings for Schedule**.

    ![Impostazione delle ore di pianificazione](./media/web-sites-scale/10SetUpScheduleTimesButton.png)

5.  Nella finestra di dialogo **Set up schedule times** sono disponibili molte scelte di configurazione utili.

    ![SetUpScheduleTimesDialog](./media/web-sites-scale/11SetUpScheduleTimesDialog.png)

6.  In **Recurring Schedules** selezionare **Differing scale between Day and Night** e/o **Differing Scale between Weekday and Weekend** per ridimensionare le risorse in base a pianificazioni distinte per il giorno e la notte o i giorni della settimana e il fine settimana.

    > [WACOM.NOTE] Ai fini di questa funzionalità, il fine settimana inizia alla fine del venerdì (alle 20.00 per impostazione predefinita) e termina all'inizio del lunedì (alle 8.00 per impostazione predefinita). Nel profilo del fine settimana vengono utilizzati gli stessi orari di inizio e di fine del giorno specificati nell'impostazione **Time**.

7.  In **Time** scegliere un'ora di inizio e un'ora di fine per il giorno in incrementi di mezz'ora e un fuso orario. Per impostazione predefinita il giorno inizia alle 8.00 e termina alle 20.00. Nel fuso orario selezionato si tiene conto dell'ora legale.

8.  In **Specific Dates** è possibile creare uno o più intervalli di tempo denominati per i quali si desidera ridimensionare le risorse. Può essere necessario, ad esempio, fornire risorse aggiuntive in occasione di eventi quali vendite o lanci di prodotti durante i quali è probabile che si verifichino picchi del traffico Web.

9.  Dopo aver definito queste impostazioni, fare clic su **OK** per chiudere la finestra di dialogo **Schedule Times**.

10. Nella casella **Edit Scale Settings for Schedule** vengono visualizzate le pianificazioni o gli eventi configurabili in base alle modifiche apportate. Selezionare una pianificazione ricorrente o date specifiche da configurare.

    ![Edit scale settings for schedule](./media/web-sites-scale/EditScaleSettingsForSchedule)

    È ora possibile utilizzare le funzionalità **Scale by Metric** e **Instance Count** per ottimizzare la scalabilità delle risorse per ogni pianificazione selezionata. 

1.  Per regolare dinamicamente il numero di istanze utilizzate dal sito Web in base alle variazioni del relativo carico, abilitare l'opzione **Scale by Metric** scegliendo **CPU**.

    ![Scale By Metric](./media/web-sites-scale/ScaleByMetric)

    Nel grafico è mostrato il numero di istanze utilizzate nel corso della settimana precedente. È quindi possibile utilizzare il grafico per monitorare l'attività di aggiunta o rimozione di istanze.

1.  **Scale by Metric** consente di modificare la funzionalità **Instance Count** per impostare il numero minimo e massimo di macchine virtuali da utilizzare per la scalabilità automatica. Azure non supererà né scenderà al di sotto dei limiti impostati.

    ![Conteggio istanze](./media/web-sites-scale/14InstanceCount.png)

2.  **Scale by Metric** abilita inoltre l'opzione **Target CPU** che consente di specificare un intervallo di destinazione per l'utilizzo della CPU. Questo intervallo rappresenta l'utilizzo della CPU medio per il sito Web. Per mantenere l'utilizzo della CPU del sito Web in questo intervallo verranno aggiunte o rimosse istanze standard.

    ![CPU di destinazione](./media/web-sites-scale/15TargetCPU.png)

    **Nota**: quando la funzionalità **Scale by Metric** è abilitata, la CPU del sito Web viene controllata una volta ogni cinque minuti e vengono aggiunte istanze in base alle esigenze di quel momento. Se l'utilizzo della CPU è minimo, tramite Microsoft Azure le istanze verranno rimosse una volta ogni due ore per garantire le prestazioni del sito Web. In genere, è appropriato impostare il conteggio minimo di istanze su 1. Tuttavia, se si verificano picchi improvvisi di utilizzo nel sito Web, assicurarsi di disporre di un numero minimo di istanze sufficiente per gestire il carico. Ad esempio, se si verifica un picco di traffico improvviso durante un intervallo di 5 minuti prima che venga controllato l'utilizzo della CPU da parte di Microsoft Azure, è possibile che il sito non sia disponibile durante quell'intervallo. Se si prevede una quantità di traffico elevata e improvvisa, impostare il conteggio minimo di istanze su un valore più elevato per prevenire questi picchi.

3.  Dopo aver apportato le modifiche a tutte le pianificazioni nell'elenco **Edit Scale Settings for Schedule**, fare clic sull'icona **Save** nella barra dei comandi nella parte inferiore della pagina per salvare contemporaneamente tutte le impostazioni.

## Ridimensionamento di un database di SQL Server collegato al sito 
Se sono presenti uno o più database di SQL Server collegati al sito Web (indipendentemente dalla modalità del piano di hosting Web), saranno visualizzati nella sezione **Linked Resources** nella parte inferiore della pagina Scaling.

1.  Per ridimensionare uno dei database, nella sezione **Linked Resources** fare clic sul collegamento **Manage scale for this database** accanto al nome del database.

    ![Database collegato](./media/web-sites-scale/16LinkedResources.png)

2.  Il collegamento reindirizzerà l'utente alla scheda SQL Server del portale di gestione di Azure in cui è possibile configurare le opzioni **Edition** e **Maximum Size** del database.

    ![Ridimensionamento di un database di SQL Server](./media/web-sites-scale/17ScaleDatabase.png)

    In **Edition** scegliere **Web** o **Business** a seconda della capacità di archiviazione necessaria. L'edizione **Web** offre un intervallo di capacità più contenute rispetto all'edizione **Business**.

    Il valore scelto per **Max Size** consente di specificare un limite superiore per il database. Gli addebiti per il database dipendono dalla quantità di dati effettivamente archiviati e pertanto la modifica della proprietà **Max Size** non ha effetto sugli addebiti. Per ulteriori informazioni, vedere [Account e fatturazione in database SQL di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=234930).

## Funzionalità per sviluppatori 
A seconda della modalità del piano di hosting Web scelta, sono disponibili le funzionalità orientate agli sviluppatori seguenti:

**Numero di bit**

-   Le modalità del piano base e standard supportano applicazioni a 64 e 32 bit.
-   Le modalità del piano gratuita e condivisa supportano solo applicazioni a 32 bit.

**Supporto del debugger**

-   Il supporto del debugger è disponibile per le modalità del piano di hosting Web gratuita, condivisa e base con 1 connessione simultanea per ogni applicazione.
-   Il supporto del debugger è disponibile per la modalità del piano di hosting Web standard con 5 connessioni simultanee per ogni applicazione.

## Altre funzionalità

**Monitoraggio degli endpoint Web**

-   Il monitoraggio degli endpoint Web è disponibile nelle modalità del piano di hosting Web standard e base. Per ulteriori informazioni sul monitoraggio degli endpoint, vedere [Come monitorare i siti Web](http://www.windowsazure.com/it-it/documentation/articles/web-sites-monitor/).

-   Per informazioni dettagliate su tutte le altre funzionalità disponibili nei piani di hosting Web, inclusi i prezzi e le funzionalità di interesse per tutti gli utenti (compresi gli sviluppatori), vedere [Dettagli prezzi - Siti Web](http://www.windowsazure.com/it-it/pricing/details/web-sites/).

## Passaggi successivi 
- Per informazioni sulle procedure consigliate per Siti Web di Azure, inclusa la creazione di un'architettura scalabile e resiliente, vedere l'articolo di blog relativo alle [procedure consigliate per Siti Web di Microsoft Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

-   Per informazioni su prezzi, supporto e contratti di servizio, visitare i collegamenti seguenti:

    [Dettagli prezzi dei trasferimenti di dati](http://www.windowsazure.com/it-it/pricing/details/data-transfers/)

    [Piani di supporto per Azure](http://www.windowsazure.com/it-it/support/plans/)

    [Contratti di servizio](http://www.windowsazure.com/it-it/support/legal/sla/)

    [Dettagli prezzi - Database SQL](http://www.windowsazure.com/it-it/pricing/details/sql-database/)

    [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://go.microsoft.com/fwlink/?LinkId=309169)

    [Dettagli prezzi - Siti Web](http://www.windowsazure.com/it-it/pricing/details/web-sites/)

    [Dettagli prezzi - Siti Web - Connessioni SSL](http://www.windowsazure.com/it-it/pricing/details/web-sites/#ssl-connections)

-   Di seguito sono riportati collegamenti a video relativi alla scalabilità dei siti Web di Azure:

    [Quando è necessario ridimensionare i siti Web di Azure - con Stefan Schackow](http://www.windowsazure.com/it-it/documentation/videos/azure-web-sites-free-vs-standard-scaling/)

    [Scalabilità automatica per i siti Web di Azure, pianificata o in base alla CPU - con Stefan Schackow](http://www.windowsazure.com/it-it/documentation/videos/auto-scaling-azure-web-sites/)

    [Che cosa accade durante il ridimensionamento dei siti Web di Azure - con Stefan Schackow](http://www.windowsazure.com/it-it/documentation/videos/how-azure-web-sites-scale/)


