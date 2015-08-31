<properties 
   pageTitle="Test delle prestazioni di un servizio cloud"
   description="Test delle prestazioni di un servizio cloud con il profiler di Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.date="08/12/2015"
   ms.author="patshea" />

# Test delle prestazioni di un servizio cloud 

##Panoramica

È possibile testare le prestazioni di un servizio cloud nei modi seguenti:

- Utilizzare la diagnostica di Azure per raccogliere informazioni su connessioni e richieste e per esaminare le statistiche del sito che mostrano le prestazioni del servizio dalla prospettiva del cliente. Per iniziare, consultare [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=623009).

- Utilizzare il profiler di Visual Studio per ottenere un'analisi approfondita degli aspetti computazionali dell'esecuzione del servizio. Come descritto in questo argomento, è possibile utilizzare il profiler per misurare le prestazioni durante l'esecuzione di un servizio in Azure. Per informazioni su come utilizzare il profiler per misurare le prestazioni durante l'esecuzione di un servizio localmente in un emulatore di calcolo, vedere [Test delle prestazioni di un servizio cloud di Azure in locale nell'emulatore di calcolo tramite Visual Studio Profiler](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## Scelta di un metodo di test delle prestazioni

###Utilizzare la diagnostica Azure per raccogliere:###

- Statistiche su pagine Web o servizi, ad esempio richieste e connessioni.

- Statistiche sui ruoli, come la frequenza con cui un ruolo viene riavviato.

- Informazioni generali sull'utilizzo della memoria, ad esempio la percentuale di tempo impiegato dal garbage collector o la memoria di un ruolo in esecuzione.

###Utilizzare il profiler di Visual Studio per:###

- Determinare le funzioni che richiedono più tempo.

- Misurare la quantità di tempo richiesta da ogni parte di un programma di calcolo intensivo.

- Confrontare rapporti dettagliati delle prestazioni per due versioni di un servizio.

- Analizzare l'allocazione della memoria in modo più dettagliato rispetto al livello delle singole allocazioni di memoria.

- Analizzare i problemi di concorrenza nel codice multithreading.

Quando si utilizza il profiler, è possibile raccogliere dati quando un servizio cloud viene eseguito localmente o in Azure.

###Raccogliere localmente dati della profilatura per:###

- Test delle prestazioni di una parte di un servizio cloud, ad esempio l'esecuzione di un ruolo di lavoro specifico che non richiede un carico simulato realistico.

- Test delle prestazioni di un servizio cloud in isolamento, in condizioni controllate.

- Test delle prestazioni di un servizio cloud prima della distribuzione in Azure.

- Test delle prestazioni di un servizio cloud privatamente, senza interferire con le distribuzioni esistenti.

- Test delle prestazioni del servizio senza incorrere in spese per l'esecuzione in Azure.

###Raccogliere dati della profilatura in Azure per:###

- Test delle prestazioni di un servizio cloud con un carico simulato o reale.

- Utilizzare il metodo di strumentazione di raccolta dei dati della profilatura, come descritto più avanti in questo argomento.

- Test delle prestazioni del servizio nello stesso ambiente in cui il servizio viene eseguito in produzione.

In genere si simula un carico per testare i servizi cloud in condizioni normali o di sovraccarico.

## Profilatura di un servizio cloud in Azure

Quando si pubblica un servizio cloud da Visual Studio, è possibile eseguire la profilatura del servizio e specificare le impostazioni di profilatura che forniscono le informazioni desiderate. Viene avviata una sessione di profilatura per ogni istanza di un ruolo. Per ulteriori informazioni su come pubblicare un servizio da Visual Studio, vedere [Pubblicazione in un servizio cloud di Azure da Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Per ulteriori informazioni sulla profilatura delle prestazioni in Visual Studio, vedere [Guida per principianti alla profilatura delle prestazioni](https://msdn.microsoft.com/library/azure/ms182372.aspx) e [Analisi delle prestazioni dell'applicazione mediante strumenti di profilatura](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE]Quando si pubblica il servizio cloud è possibile abilitare o IntelliTrace o la profilatura. Non è possibile abilitare entrambi.

###Metodi di raccolta del profiler###
È possibile utilizzare metodi di raccolta diversi per la profilatura, in base ai problemi delle prestazioni:

- **Campionamento CPU**: questo metodo consente di raccogliere statistiche dell'applicazione utili per l'analisi iniziale dei problemi relativi all'utilizzo della CPU. Campionamento CPU è il metodo consigliato per iniziare la maggior parte delle indagini sulle prestazioni. Esiste un impatto minimo sull'applicazione di cui si esegue la profilatura quando si raccolgono dati di campionamento CPU.

- **Strumentazione**: questo metodo consente di raccogliere dati dettagliati sui tempi utili per l'analisi mirata e per l'analisi dei problemi relativi alle prestazioni di input/output. Il metodo della strumentazione registra ogni voce, uscita e chiamata di funzione delle funzioni di un modulo durante l'esecuzione di una profilatura. Questo metodo è utile per raccogliere informazioni dettagliate sui tempi per una sezione del codice e per comprendere l'impatto delle operazioni di input e output sulle prestazioni dell'applicazione. Questo metodo è disabilitato per un computer che esegue un sistema operativo a 32 bit. Questa opzione è disponibile solo quando si esegue il servizio cloud in Azure, non in locale nell'emulatore di calcolo.

- **Allocazione della memoria .NET**: questo metodo raccoglie dati sull'allocazione della memoria .NET Framework utilizzando il metodo di profilatura del campionamento. I dati raccolti includono il numero e la dimensione degli oggetti allocati.

- **Concorrenza**: questo metodo raccoglie dati sui conflitti delle risorse e dati di esecuzione dei thread e dei processi utili per l'analisi delle applicazioni multithread e multiprocesso. Il metodo di concorrenza raccoglie i dati per ogni evento che blocca l'esecuzione del codice, ad esempio quando un thread attende che l'accesso bloccato a una risorsa dell'applicazione venga liberato. Questo metodo è utile per l'analisi di applicazioni multithread.

- È inoltre possibile abilitare **Profilatura interazione tra livelli**, che fornisce informazioni aggiuntive sui tempi di esecuzione delle chiamate ADO.NET sincrone nelle funzioni di applicazioni multilivello che comunicano con uno o più database. È possibile raccogliere dati dell’interazione tra livelli con qualsiasi metodo di profilatura. Per ulteriori informazioni sulla profilatura dell’interazione tra livelli, vedere [Visualizzazione Interazioni tra livelli](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## Configurazione delle impostazioni di profilatura

Nella figura seguente viene illustrato come configurare le impostazioni di profilatura dalla finestra di dialogo Pubblica l'applicazione Azure.

![Configurare le impostazioni di profilatura](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)


### Per configurare le impostazioni di profilatura

1. In Esplora soluzioni aprire il menu di scelta rapida per il progetto Azure e quindi scegliere **Pubblica**. Per ulteriori informazioni su come pubblicare un servizio cloud, vedere [Pubblicazione di un servizio cloud con gli strumenti di Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. Nella finestra di dialogo **Pubblica l'applicazione Azure** scegliere la scheda **Impostazioni avanzate**.

1. Per abilitare la profilatura, selezionare la casella di controllo **Abilita profilatura**.

1. Per configurare le impostazioni di profilatura, scegliere il collegamento ipertestuale **Impostazioni**. Viene visualizzata la finestra di dialogo Impostazioni di profilatura.

1. Dai pulsanti di opzione **Specificare il metodo di analisi da utilizzare**, scegliere il tipo di analisi necessario.

1. Per raccogliere i dati di profilatura dell’interazione tra livelli, selezionare la casella di controllo **Abilita profilatura interazione tra livelli** .

1. Per salvare le impostazioni, scegliere il pulsante **OK**. Quando si pubblica l'applicazione, queste impostazioni vengono utilizzate per creare la sessione di profilatura per ogni ruolo.

## Visualizzare i rapporti della profilatura

Viene creata una sessione di profilatura per ogni istanza di un ruolo nel servizio cloud. Per visualizzare i report di profilatura di ogni sessione da Visual Studio, è possibile visualizzare la finestra Esplora server e quindi scegliere il nodo di calcolo di Azure per selezionare un'istanza di un ruolo. È quindi possibile visualizzare il report di profilatura, come illustrato nella figura seguente.

![Visualizzare il rapporto della profilatura da Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)



### Per visualizzare i rapporti della profilatura

1. Per visualizzare la finestra Esplora server in Visual Studio, nella barra dei menu scegliere Visualizza, Esplora server.

1. Scegliere il nodo di calcolo di Azure e quindi scegliere il nodo di distribuzione di Azure per il servizio cloud selezionato per la profilatura durante la pubblicazione da Visual Studio.

1. Per visualizzare i rapporti di profilatura per un'istanza, scegliere il ruolo nel servizio, aprire il menu di scelta rapida per un'istanza specifica, quindi scegliere Visualizza rapporto sulla profilatura. Il report, un file con estensione VSP, viene ora scaricato da Azure e lo stato del download viene visualizzato nel Log attività di Azure. Al termine del download, il report della profilatura viene visualizzato in una scheda nell'editor di Visual Studio denominata <Role name>\_<Instance Number>\_<identifier>.vsp. Vengono visualizzati i dati di riepilogo per il report.

1. Per visualizzare viste diverse del report, nell'elenco Vista corrente, scegliere il tipo di visualizzazione che si desidera. Per ulteriori informazioni, vedere [Visualizzazioni dei rapporti degli strumenti di profilatura](https://msdn.microsoft.com/library/bb385755.aspx).

## Passaggi successivi

[Debug di Servizi cloud](http://go.microsoft.com/fwlink//p/?LinkID=62304)

[Pubblicazione in un servizio cloud di Azure da Visual Studio](http://go.microsoft.com/fwlink//p/?LinkID=623012)

<!---HONumber=August15_HO8-->