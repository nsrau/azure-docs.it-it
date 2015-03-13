<properties 
	pageTitle="Come monitorare un servizio cloud - Azure" 
	description="Informazioni su come monitorare i servizi cloud usando il portale di gestione di Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="robb"/>


# Come monitorare i servizi cloud

[AZURE.INCLUDE [disclaimer](../includes/disclaimer.md)]

È possibile monitorare le metriche delle prestazioni chiave per i servizi cloud nel portale di gestione di Azure. È possibile impostare il livello di monitoraggio da minimo a dettagliato per ogni ruolo del servizio e personalizzare le schermate di monitoraggio. I dati di monitoraggio dettagliati vengono memorizzati in un account di archiviazione, a cui è possibile accedere fuori dal portale. 

Le schermate di monitoraggio nel portale di gestione sono altamente configurabili. È possibile scegliere la metrica che si desidera monitorare dall'elenco delle metriche nella pagina **Monitoraggio** e la metrica da riportare nei relativi grafici nella pagina **Monitoraggio** e nel dashboard. 

## Sommario##
* [Concetti](#concepts)
* [Procedura: Configurare il monitoraggio per i servizi cloud](#verbose)
* [Procedura: Ricevere avvisi per le metriche dei servizi cloud](#receivealerts)
* [Procedura: Aggiungere metriche alla relativa tabella](#addmetrics)
* [Procedura: Personalizzare il grafico delle metriche](#customizechart)
* [Procedura: Accedere ai dati di monitoraggio dettagliati all'esterno del portale di gestione](#accessverbose)

<h2><a id="concepts"></a>Concetti</h2>

Per impostazione predefinita, per un nuovo servizio cloud è previsto un monitoraggio minimo, con contatori delle prestazioni raccolte dal sistema operativo per le istanze del ruolo (macchine virtuali). Le metriche minime si limitano a percentuale CPU, dati in entrata, dati in uscita, velocità effettiva di lettura dal disco e velocità effettiva di scrittura sul disco. Con la configurazione del monitoraggio dettagliato, è possibile ricevere ulteriori metriche in base ai dati delle prestazioni all'interno delle macchine virtuali (istanze del ruolo). Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione.

> [AZURE.NOTE]
> Se si usa il monitoraggio dettagliato è possibile aggiungere altri contatori delle prestazioni all'avvio dell'istanza del ruolo attraverso un file di configurazione della diagnostica. Per monitorare queste metriche nel portale di gestione è necessario aggiungere i contatori delle prestazioni prima di configurare il monitoraggio dettagliato. Per altre informazioni, vedere <a href="http://azure.microsoft.com/ documentation/articles/cloud-services-dotnet-diagnostics/">Abilitazione della diagnostica nei servizi cloud e nelle macchine virtuali di Azure</a>.

Per impostazione predefinita, i dati del contatore di prestazioni dalle istanze del ruolo sono campionati e trasferiti dall'istanza del ruolo a intervalli di 3 minuti. Quando viene abilitato il monitoraggio dettagliato, i dati non elaborati del contatore di prestazioni sono aggregati per ogni istanza del ruolo e per ogni ruolo tra istanze del ruolo a intervalli di 5 minuti, 1 ora e 12 ore. I dati aggregati vengono eliminati dopo 10 giorni.

Dopo aver abilitato il monitoraggio dettagliato, i dati aggregati di monitoraggio sono archiviati in tabelle nell'account di archiviazione. Per abilitare il monitoraggio dettagliato per un ruolo, è necessario configurare una stringa di connessione diagnostica collegata all'account di archiviazione. È possibile usare account di archiviazione diversi per i diversi ruoli.

Si noti che l'abilitazione del monitoraggio dettagliato comporta un aumento dei costi legati all'archiviazione e al trasferimento dei dati e alle transazioni di archiviazione. Il monitoraggio minimo non richiede un account di archiviazione. I dati per le metriche esposti al livello minimo di monitoraggio non sono archiviati nell'account di archiviazione, anche se si imposta il livello di monitoraggio dettagliato.


<h2><a id="verbose"></a>Procedura: Configurare il monitoraggio per i servizi cloud</h2>

Attenersi alle seguenti procedure per configurare il monitoraggio dettagliato o minimo nel portale di gestione. Per abilitare il monitoraggio dettagliato è necessario abilitare Diagnostica Azure e configurare le stringhe di connessione diagnostica, consentendo così a Diagnostica Azure di accedere agli account di archiviazione per archiviare i dati del monitoraggio dettagliato.

### Operazioni preliminari###

- Creare un account di archiviazione per archiviare i dati di monitoraggio. È possibile usare account di archiviazione diversi per i diversi ruoli. Per altre informazioni, vedere gli argomenti **Account di archiviazione** o [Come creare un account di archiviazione](/it-it/manage/services/storage/how-to-create-a-storage-account/) nella Guida.


- Abilitare Diagnostica Azure per i ruoli del servizio cloud. <br /><br />Per altre informazioni, vedere [Abilitazione della diagnostica nei servizi cloud e nelle macchine virtuali di Azure](/it-it/documentation/articles/cloud-services-dotnet-diagnostics/).

Nel portale di gestione è possibile aggiungere o modificare le stringhe di connessione diagnostica usate da Diagnostica Azure per accedere agli account di archiviazione in cui vengono archiviati i dati di monitoraggio dettagliati ed è possibile impostare il livello di monitoraggio dettagliato o minimo. Poiché il monitoraggio dettagliato archivia i dati in un account di archiviazione, è necessario configurare le stringhe di connessione diagnostica prima di impostare il livello di monitoraggio dettagliato.

### Per configurare le stringhe di connessione diagnostica per il monitoraggio dettagliato###

1. Copiare una chiave di accesso alle risorse di archiviazione per l'account di archiviazione da usare per l'archiviazione dei dati del monitoraggio dettagliato. Nel [portale di gestione di Azure](https://manage.windowsazure.com/) è possibile usare **Gestisci chiavi** nella pagina **Account di archiviazione**. Per altre informazioni, vedere [Come gestire Servizi cloud](/it-it/documentation/articles/cloud-services-how-to-manage/) o la pagina **Account di archiviazione** della Guida. 

2. Aprire **Servizi cloud**. Fare quindi clic sul nome del servizio cloud che si intende configurare per aprire il dashboard.

3. Fare clic su **Produzione** o **Gestione temporanea** per visualizzare la distribuzione che si desidera configurare.

4. Fare clic su **Configura**.

	Sarà possibile modificare le impostazioni di **monitoraggio** nella parte superiore della pagina **Configura**, illustrata di seguito. Se Diagnostica Azure non è abilitato per il servizio cloud, l'opzione **Livello** non è disponibile. Non è possibile modificare il criterio di conservazione dei dati. I dati di monitoraggio dettagliati per un servizio cloud sono archiviati per 10 giorni.

	![Monitoring options](./media/cloud-services-how-to-monitor/CloudServices_MonitoringOptions.png)

5. In **Stringhe di connessione di diagnostica** completare la stringa di connessione diagnostica per ogni ruolo per il quale si desidera il monitoraggio dettagliato.
	
	Le stringhe di connessione presentano il seguente formato. (L'esempio si riferisce a un servizio cloud che usa endpoint predefiniti). Per aggiornare una stringa di connessione, immettere un nome di account di archiviazione valido e la chiave di accesso alle risorse di archiviazione per l'account di archiviazione che si desidera usare.
         
 	DefaultEndpointsProtocol=https;AccountName=StorageAccountName;AccountKey=StorageAccountKey  

6. Fare clic su **Salva**.

Se si intende abilitare il monitoraggio dettagliato, eseguire la procedura successiva dopo aver configurato le stringhe di connessione diagnostica per i ruoli del servizio. 


### Per cambiare il livello di monitoraggio a dettagliato o minimo###

1. Nel [portale di gestione](https://manage.windowsazure.com/) aprire la pagina **Configura** per la distribuzione del servizio cloud.

2. In **Livello** fare clic su **Dettagliato** o **Minimo**. 

3. Fare clic su **Salva**.

Dopo avere abilitato il monitoraggio dettagliato, è consigliabile visualizzare i primi dati di monitoraggio nel portale di gestione entro un'ora.

I dati non elaborati del contatore di prestazioni e i dati aggregati di monitoraggio sono archiviati nell'account di archiviazione in tabelle contraddistinte dall'ID di distribuzione per i ruoli. 

<h2><a id="receivealerts"></a>Procedura: Ricevere avvisi per le metriche dei servizi cloud</h2>

È possibile ricevere avvisi basati sulle metriche di monitoraggio del servizio cloud. Nella pagina **Servizi di gestione** del portale di gestione di Azure è possibile creare una regola per attivare un avviso quando la metrica scelta raggiunge il valore specificato. È inoltre possibile impostare l'invio di un messaggio di posta elettronica all'attivazione dell'avviso. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

<h2><a id="addmetrics"></a>Procedura: Aggiungere metriche alla relativa tabella</h2>

1. Nel [portale di gestione](http://manage.windowsazure.com/) aprire la pagina **Monitoraggio** per il servizio cloud.

	Per impostazione predefinita, la tabella delle metriche riporta un subset delle metriche disponibili. Nell'illustrazione sono visualizzate le metriche dettagliate predefinite per un servizio cloud, limitate al contatore di prestazioni Memoria/MB disponibili, con dati aggregati a livello di ruolo. Usare **Aggiungi metriche** per selezionare altre metriche aggregate e a livello di ruolo da monitorare nel portale di gestione.

	![Verbose display](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. Per aggiungere metriche alla relativa tabella:

	a. Fare clic su **Aggiungi metriche** per aprire **Scegli metriche**, come illustrato di seguito.
	Viene espansa la prima metrica disponibile per visualizzare le opzioni disponibili. Per ogni metrica, la prima opzione consente di visualizzare dati aggregati di monitoraggio per tutti i ruoli. È inoltre possibile scegliere i singoli ruoli di cui visualizzare i dati.

	![Add metrics](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)


	b. Per selezionare la metrica da visualizzare:

	- Fare clic sulla freccia giù accanto alla metrica per espandere le opzioni di monitoraggio.
	- Selezionare le caselle di controllo relative alle opzioni di monitoraggio da visualizzare.

	Nella tabella delle metriche è possibile visualizzare fino a 50 metriche.

	> [AZURE.TIP] Nel monitoraggio dettagliato l'elenco delle metriche può contenere decine di metriche. Per visualizzare una barra di scorrimento, passare il puntatore del mouse sul lato destro della finestra di dialogo. Per filtrare l'elenco, fare clic sull'icona di ricerca e immettere il testo nella casella di ricerca, come illustrato di seguito.
 
	![Add metrics search](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)

3. Dopo aver completato la selezione delle metriche, fare clic su OK (segno di spunta).

	Le metriche selezionate vengono aggiunte alla tabella, come illustrato di seguito.

	![monitor metrics](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. Per eliminare una metrica dalla tabella, fare clic sulla metrica per selezionarla e quindi fare clic su **Elimina metrica**. (Selezionando una metrica viene visualizzata solo l'opzione **Elimina metrica**).


<h2><a id="customizechart"></a>Procedura: Personalizzare il grafico delle metriche</h2>

1. Nella tabella delle metriche selezionare fino a 6 metriche da tracciare nel relativo grafico. Per selezionare una metrica, fare clic sulla casella di controllo a sinistra. Per rimuovere una metrica dal grafico, deselezionare la casella di controllo nella tabella delle metriche.

	Le metriche selezionate nella tabella vengono aggiunte al grafico. Su uno schermo stretto, un elenco a discesa **n more** riporta le intestazioni delle metriche che non si adattano allo schermo.

 
2. Per passare dalla visualizzazione di valori relativi (solo il valore finale per ogni metrica) e valori assoluti (asse Y visualizzato), selezionare Relative o Absolute nella parte superiore del grafico.

	![Relative or Absolute](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare 1 ora, 24 ore o 7 giorni nella parte superiore del grafico.

	![Monitor display period](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

	Nel grafico delle metriche del dashboard le metriche vengono tracciate in modo diverso. È disponibile un set di metriche standard che vengono aggiunte o rimosse selezionandone l'intestazione.

### Per personalizzare il grafico delle metriche sul dashboard###

1. Aprire il dashboard per il servizio cloud.

2. Aggiungere o rimuovere le metriche dal grafico:

	- Per riportare una nuova metrica, selezionare la casella di controllo corrispondente nelle intestazioni del grafico. Su uno schermo stretto, fare clic sulla freccia giù accanto alla voce relativa a ***n*??metriche** per tracciare sul grafico una metrica non visualizzata nell'area delle intestazioni del grafico.

	- Per eliminare una metrica tracciata sul grafico, deselezionare la casella di controllo accanto all'intestazione.

3. Passare della visualizzazione **Relativo** o **Assoluto**.

4. Scegliere se visualizzare dati relativi a 1 ora, 24 ore o 7 giorni.

<h2><a id="accessverbose"></a>Procedura: Accedere ai dati di monitoraggio dettagliati all'esterno del portale di gestione</h2>

I dati di monitoraggio dettagliati sono archiviati in tabelle negli account di archiviazione specificati per ogni ruolo. Per ogni distribuzione del servizio cloud, vengono create sei tabelle per il ruolo. Vengono create due tabelle per ogni intervallo di tempo (5 minuti, 1 ora e 12 ore). Una di queste tabelle archivia aggregazioni a livello di ruolo, mentre l'altra archivia le aggregazioni per le istanze del ruolo. 

I nomi delle tabelle presentano il seguente formato:

	WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

dove:

- *deploymentID* è il GUID assegnato alla distribuzione del servizio cloud

- *aggregation_interval* = 5M, 1H o 12H

- aggregazioni a livello di ruolo = R

- aggregazioni per le istanze del ruolo = RI

Ad esempio, nelle seguenti tabelle sarebbero archiviati dati di monitoraggio dettagliato aggregati a intervalli di 1 ora:

	WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

	WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)


<!--HONumber=45--> 
