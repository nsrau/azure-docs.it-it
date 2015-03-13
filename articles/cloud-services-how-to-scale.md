<properties 
	pageTitle="Come scalare un servizio cloud - Azure" 
	description="Informazioni su come scalare un servizio cloud e le risorse collegate in Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="adegeo"/>





# Come scalare un'applicazione


Nella pagina Scale del portale di gestione di Azure è possibile scalare manualmente l'applicazione o impostare parametri per la scalabilità automatica. È possibile scalare le applicazioni che eseguono ruoli Web, ruoli di lavoro o macchine virtuali. Per scalare un'applicazione che esegue istanze di ruoli Web o ruoli di lavoro, vengono aggiunte o rimosse istanze dei ruoli per supportare il carico di lavoro.

Quando si scala un'applicazione che esegue macchine virtuali, non vengono create o eliminate macchine, ma vengono attivate o disattivate macchine da un set di disponibilità di macchine create in precedenza. È possibile specificare la scalabilità in base a una percentuale media di uso della CPU o al numero di messaggi in una coda.

Prima di configurare la scalabilità per l'applicazione, tenere presente quanto segue:

- Le macchine virtuali create devono essere aggiunte a un set di disponibilità per scalare un'applicazione che le usa. Le macchine virtuali aggiunte possono essere inizialmente attivate o disattivate, ma verranno attivate in caso di aumento e disattivate in caso di riduzione del numero di istanze. Per altre informazioni sulle macchine virtuali e sui set di disponibilità, vedere [Gestire la disponibilità delle macchine virtuali](http://azure.microsoft.com/ documentation/articles/virtual-machines-manage-availability/).
- La scalabilità è influenzata dall'uso di core. Le istanze del ruolo o le macchine virtuali più ampie usano più core. Un'applicazione può essere scalata solo entro i limiti di core previsti dalla sottoscrizione. Ad esempio, se la sottoscrizione prevede al massimo venti core e si esegue un'applicazione con due macchine virtuali di medie dimensioni (per un totale di quattro core), l'aumento di istanze di altre distribuzioni del servizio cloud nella sottoscrizione è limitata a sedici core. Tutte le macchine virtuali in un set di disponibilità usate per scalare un'applicazione devono avere le stesse dimensioni. Per altre informazioni sull'uso di core e sulle dimensioni delle macchine, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/dn197896.aspx).
- È necessario creare una coda e associarla a un ruolo o set di disponibilità prima di scalare un'applicazione in base a una soglia di messaggi. Per altre informazioni, vedere [Introduzione all'archiviazione di code](http://azure.microsoft.com/develop/net/how-to-guides/queue-service).
- È possibile scalare le risorse collegate al servizio cloud. Per altre informazioni sul collegamento di risorse, vedere [Procedura: Collegare una risorsa a un servizio cloud](http://azure.microsoft.com/manage/services/cloud-services/how-to-manage-a-cloud-service/#linkresources).
- Per abilitare la disponibilità elevata dell'applicazione, è necessario accertarsi che sia distribuita con due o più istanze del ruolo o macchine virtuali. Per altre informazioni, vedere [Contratti di servizio](https://www.windowsazure.com/it-it/support/legal/sla/).

È possibile eseguire le seguenti azioni di scalabilità per un servizio cloud:

- [Ridimensionare manualmente un'applicazione che esegue ruoli Web o ruoli di lavoro](#manualscale)
- [Ridimensionare automaticamente un'applicazione che esegue ruoli Web, ruoli di lavoro o macchine virtuali](#autoscale)
- [Ridimensionare risorse collegate](#scalelink)
- [Pianificare il ridimensionamento dell'applicazione](#schedule)


<h2><a id="manualscale"></a>Ridimensionare manualmente un'applicazione che esegue ruoli Web o ruoli di lavoro</h2>

Nella pagina Scale è possibile aumentare o diminuire manualmente il numero delle istanze in esecuzione in un servizio di cloud.

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud per aprire il dashboard.

2. Fare clic su **Ridimensiona**. La scalabilità automatica è disattivata per impostazione predefinita per tutti i ruoli, ovvero è possibile modificare manualmente il numero di istanze usate dall'applicazione.

	![Scale page][manual_scale]

3. In ogni ruolo nel servizio cloud è presente un dispositivo di scorrimento che consente di modificare il numero di istanze da usare. Per aggiungere un'istanza del ruolo, trascinare la barra verso destra. Per rimuovere un'istanza, trascinare la barra verso sinistra.

	![Scale role][slider_role] 


	È possibile aumentare il numero di istanze usate solo se è disponibile il numero di core appropriato per supportare le istanze. I colori del dispositivo di scorrimento rappresentano i core usati e disponibili nella sottoscrizione:

	- Il blu rappresenta i core usati dal ruolo selezionato
	- Il grigio scuro rappresenta i core usati da tutti i ruoli e da tutte le macchine virtuali nella sottoscrizione
	- Il grigio chiaro rappresenta i core disponibili per l'uso per la scalabilità
	- Il rosa rappresenta una modifica apportata che non è stata salvata

4. Fare clic su **Salva**. Verranno aggiunte o rimosse istanze del ruolo in base alle selezioni effettuate.

<h2><a id="autoscale"></a>Ridimensionare automaticamente un'applicazione che esegue ruoli Web, ruoli di lavoro o macchine virtuali</h2>

Nella pagina Scale è possibile configurare il servizio cloud in modo da aumentare o ridurre automaticamente il numero di istanze o macchine virtuali usate dall'applicazione. La scalabilità può essere configurata in base ai seguenti parametri:

- [Utilizzo medio della CPU](#averagecpu): se la percentuale media di uso della CPU sale o scende oltre le soglie specificate, vengono create o eliminate istanze del ruolo o vengono attivate o disattivate macchine virtuali da un set di disponibilità.
- [Messaggi in coda](#queuemessages): se il numero di messaggi in una coda sale o scende oltre una soglia specificata, vengono create o eliminate istanze del ruolo o vengono attivate o disattivate macchine virtuali da un set di disponibilità.

<h3><a id="averagecpu"></a>Utilizzo medio della CPU</h3>

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud per aprire il dashboard.
2. Fare clic su **Ridimensiona**.
3. Scorrere fino alla sezione relativa al ruolo o al set di disponibilità, quindi fare clic su **CPU**. In questo modo viene abilitata la scalabilità automatica dell'applicazione in base alla percentuale media di uso delle risorse di CPU.

	![Autoscale on][autoscale_on]

4. Per ogni ruolo o set di disponibilità è presente un dispositivo di scorrimento che consente di modificare il numero di istanze che possono essere usate. Per impostare il numero massimo di istanze utilizzabili, trascinare verso destra la barra di destra. Per impostare il numero minimo di istanze utilizzabili, trascinare verso sinistra la barra di sinistra.

	**Nota**: il valore **Istanza** nella pagina Ridimensiona rappresenta un'istanza del ruolo o un'istanza di una macchina virtuale.

	![Instance range][instance_range]

	Il numero massimo di istanze è limitato dai core disponibili nella sottoscrizione. I colori del dispositivo di scorrimento rappresentano i core usati e disponibili nella sottoscrizione:

	- Il blu rappresenta il numero massimo di core che il ruolo può usare.
	- Il grigio scuro rappresenta i core usati da tutti i ruoli e da tutte le macchine virtuali nella sottoscrizione. Quando questo valore si sovrappone ai core usati dal ruolo, il colore diventa blu scuro.
	- Il grigio chiaro rappresenta i core disponibili per l'uso per la scalabilità.
	- Il rosa rappresenta una modifica apportata che non è stata salvata.

5. Tramite un dispositivo di scorrimento è possibile specificare l'intervallo della percentuale media di uso della CPU. Quando la percentuale media di uso della CPU supera l'impostazione massima, vengono create più istanze del ruolo o vengono attivate più macchine virtuali. Quando la percentuale media di uso della CPU scende al di sotto dell'impostazione minima, vengono eliminate istanze del ruolo o vengono disattivate macchine virtuali. Per impostare il valore massimo di percentuale media di uso della CPU, trascinare verso destra la barra di destra. Per impostare il valore minimo di percentuale media di uso della CPU, trascinare verso sinistra la barra di sinistra.

	![Target cpu][target_cpu]

6. È possibile specificare il numero di istanze da aggiungere o attivare ogni volta che viene aumentato il numero di istanze dell'applicazione. Per aumentare il numero di istanze create o attivate quando viene aumentato il numero di istanze dell'applicazione, trascinare la barra verso destra. Per ridurre il numero, trascinare la barra verso sinistra.

	![Scale cpu up][scale_cpuup]

7. Impostare il numero di minuti di attesa tra l'ultima azione di ridimensionamento e la successiva azione di aumento del numero di istanze. L'ultima azione di ridimensionamento può essere un aumento o una riduzione del numero di istanze.

	![Up time][scale_uptime]

	Nel calcolo della percentuale media di uso della CPU vengono incluse tutte le istanze e la media si basa sull'uso nell'ora precedente. A seconda del numero di istanze usate dall'applicazione, l'azione di ridimensionamento può richiedere più tempo rispetto al tempo di attesa specificato, se quest'ultimo è impostato su un valore molto basso. Il tempo minimo tra le azioni di ridimensionamento è pari a cinque minuti. Le azioni di ridimensionamento non possono avere luogo se è in corso la transizione di una delle istanze.

8. È inoltre possibile specificare il numero di istanze da eliminare o disattivare ogni volta che viene ridotto il numero di istanze dell'applicazione.  Per aumentare il numero di istanze eliminate o disattivate quando viene ridotto il numero di istanze dell'applicazione, trascinare la barra verso destra. Per ridurre il numero, trascinare la barra verso sinistra.

	![Scale cpu down][scale_cpudown]

	Se l'applicazione può presentare improvvisi incrementi di uso della CPU, assicurarsi di disporre di un numero minimo di istanze sufficiente per gestirli.

9. Impostare il numero di minuti di attesa tra l'ultima azione di ridimensionamento e la successiva azione di riduzione del numero di istanze. L'ultima azione di ridimensionamento può essere un aumento o una riduzione del numero di istanze.

	![Down time][scale_downtime]

10. Fare clic su **Salva**. Per completare l'azione di ridimensionamento possono essere necessari fino a cinque minuti.

<h3><a id="queuemessages"></a>Messaggi in coda</h3>

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud per aprire il dashboard.
2. Fare clic su **Ridimensiona**.
3. Scorrere fino alla sezione relativa al ruolo o al set di disponibilità, quindi fare clic su **Coda**. In questo modo viene abilitata la scalabilità automatica dell'applicazione in base a un determinato numero di messaggi in coda.

	![Scale queue][scale_queue]

4. Per ogni ruolo o set di disponibilità nel servizio cloud è presente un dispositivo di scorrimento che consente di modificare il numero di istanze che possono essere usate. Per impostare il numero massimo di istanze utilizzabili, trascinare verso destra la barra di destra. Per impostare il numero minimo di istanze utilizzabili, trascinare verso sinistra la barra di sinistra.

	![Queue range][queue_range]

	**Nota**: il valore **Istanza** nella pagina Ridimensiona rappresenta un'istanza del ruolo o un'istanza di una macchina virtuale.
	
	Il numero massimo di istanze è limitato dai core disponibili nella sottoscrizione. I colori del dispositivo di scorrimento rappresentano i core usati e disponibili nella sottoscrizione:
	- Il blu rappresenta il numero massimo di core che il ruolo può usare.
	- Il grigio scuro rappresenta i core usati da tutti i ruoli e da tutte le macchine virtuali nella sottoscrizione. Quando questo valore si sovrappone ai core usati dal ruolo, il colore diventa blu scuro.
	- Il grigio chiaro rappresenta i core disponibili per l'uso per la scalabilità.
	- Il rosa rappresenta una modifica apportata che non è stata salvata.

5. Selezionare l'account di archiviazione associato alla coda che si desidera usare.

	![Storage name][storage_name]	

6. Selezionare la coda.

	![Queue name][queue_name]

7. Specificare il numero di messaggi che si prevede che ogni istanza supporterà. Le istanze verranno scalate in base al numero totale di messaggi diviso per il numero di messaggi definito per ogni macchina.

	![Message number][message_number]

8. È possibile specificare il numero di istanze da aggiungere o attivare ogni volta che viene aumentato il numero di istanze dell'applicazione. Per aumentare il numero di istanze aggiunte o attivate quando viene aumentato il numero di istanze dell'applicazione, trascinare la barra verso destra. Per ridurre il numero, trascinare la barra verso sinistra.

	![Scale cpu up][scale_cpuup]

9. Impostare il numero di minuti di attesa tra l'ultima azione di ridimensionamento e la successiva azione di aumento del numero di istanze. L'ultima azione di ridimensionamento può essere un aumento o una riduzione del numero di istanze.

	![Up time][scale_uptime]

	Il tempo minimo tra le azioni di ridimensionamento è pari a cinque minuti. Le azioni di ridimensionamento non possono avere luogo se è in corso la transizione di una delle istanze.

10. È inoltre possibile specificare il numero di istanze da eliminare o non usare ogni volta che viene ridotto il numero di istanze dell'applicazione.  Per specificare l'incremento di scalabilità viene usato un dispositivo di scorrimento. Per aumentare il numero di istanze eliminate o inutilizzate quando viene ridotto il numero di istanze dell'applicazione, trascinare la barra verso destra. Per ridurre il numero, trascinare la barra verso sinistra.

	![Scale cpu down][scale_cpudown]

11.	Impostare il numero di minuti di attesa tra l'ultima azione di ridimensionamento e la successiva azione di riduzione del numero di istanze. L'ultima azione di ridimensionamento può essere un aumento o una riduzione del numero di istanze.

	![Down time][scale_downtime]

12. Fare clic su **Salva**. Per completare l'azione di ridimensionamento possono essere necessari fino a cinque minuti.

<h2><a id="scalelink"></a>Ridimensionare risorse collegate</h2>

Quando si scala un ruolo, spesso risulta utile scalare anche il database usato dall'applicazione. Se si collega il database al servizio cloud, nella pagina Scale viene modificata l'edizione del database SQL e viene ridimensionato il database.

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud per aprire il dashboard.
2. Fare clic su **Ridimensiona**.
3. Nella sezione Linked Resources selezionare l'edizione da usare per il database.

	![Linked resources][linked_resources]

4. Selezionare le dimensioni del database.
5. Fare clic su **Salva** per aggiornare le risorse collegate.

<h2><a id="schedule"></a>Pianificare il ridimensionamento dell'applicazione</h2>

È possibile pianificare la scalabilità automatica dell'applicazione configurando pianificazioni per orari diversi. Per la scalabilità automatica sono disponibili le seguenti opzioni:

- **No schedule**: opzione predefinita che consente di scalare automaticamente l'applicazione sempre nello stesso modo.

- **Day and night**: opzione che consente di specificare la scalabilità per determinati orari del giorno e della notte.

**Nota**: le pianificazioni non sono attualmente disponibili per le applicazioni che usano Macchine virtuali.

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud per aprire il dashboard.
2. Fare clic su **Ridimensiona**.
3. Nella pagina Ridimensiona fare clic su **Imposta ore di pianificazione**.

	![Schedule scaling][scale_schedule]

4. Selezionare il tipo di pianificazione della scalabilità che si desidera impostare.

5. Specificare gli orari di inizio e fine del giorno e impostare il fuso orario. Per la pianificazione diurna e notturna, gli orari rappresentano l'inizio e la fine del giorno e il tempo rimanente rappresenta la notte.

6. Fare clic sul segno di spunta nella parte inferiore della pagina per salvare le pianificazioni.

7. Dopo il salvataggio, le pianificazioni verranno visualizzate nell'elenco. È possibile selezionare la pianificazione oraria da usare, quindi modificare le impostazioni di scalabilità. Le impostazioni di scalabilità verranno applicate solo durante la pianificazione selezionata. Per modificare le pianificazioni, fare clic su **Imposta ore di pianificazione**.

[manual_scale]: ./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png
[slider_role]: ./media/cloud-services-how-to-scale/CloudServices_SliderRole.png
[autoscale_on]: ./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png
[instance_range]: ./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png
[target_cpu]: ./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png
[scale_cpuup]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png
[scale_uptime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png
[scale_cpudown]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png
[scale_downtime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png
[scale_queue]: ./media/cloud-services-how-to-scale/CloudServices_QueueScale.png
[queue_range]: ./media/cloud-services-how-to-scale/CloudServices_QueueRange.png
[storage_name]: ./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png
[queue_name]: ./media/cloud-services-how-to-scale/CloudServices_QueueName.png
[message_number]: ./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png
[linked_resources]: ./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png
[scale_schedule]: ./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png

<!--HONumber=45--> 
