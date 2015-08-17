<properties 
 pageTitle="Dimensioni dei servizi cloud" 
 description="Elenca le varie dimensioni dei ruoli Web e di lavoro del servizio cloud di Azure." 
 services="cloud-services" 
 documentationCenter="" 
 authors="Thraka" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="cloud-services" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="na" 
 ms.workload="tbd"
 ms.date="06/04/2015" 
 ms.author="adegeo"/>
 
# Dimensioni dei servizi cloud

Questo argomento descrive le dimensioni e le opzioni disponibili per le istanze del ruolo di servizio cloud (ruoli Web e di lavoro). Fornisce anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse.

Le macchine virtuali e i servizi cloud di Azure sono due dei diversi tipi di risorse di calcolo offerte da Azure. Per informazioni, vedere [Opzioni di hosting di calcolo fornite da Azure](fundamentals-application-models.md).

> [AZURE.NOTE]Per i limiti di Azure correlati, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## Dimensioni delle istanze del ruolo Web e di lavoro

Le considerazioni seguenti potrebbero essere utili all'utente per scegliere una dimensione:

* A questo punto è possibile configurare le istanze per l'uso di macchine virtuali serie D. Queste macchine virtuali sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD per il disco temporaneo. Per informazioni dettagliate, vedere l'annuncio sul blog di Azure [Nuove dimensioni delle macchine virtuali serie D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

* I ruoli Web e di lavoro richiedono più spazio su disco temporaneo rispetto alle macchine virtuali di Azure a causa dei requisiti di sistema. I file di sistema riservano 4 GB di spazio per il file di paging di Windows e 2 GB di spazio per il file dump di Windows.

* Il disco del sistema operativo contiene il sistema operativo guest di Windows, la cartella Programmi (incluse le installazioni eseguite mediante attività di avvio a meno che non si specifichi un altro disco), le modifiche del Registro di sistema, la cartella System32 e .NET Framework.

* Il disco di risorse locale contiene i file di configurazione e i log di Azure, Diagnostica Azure, inclusi i log di IIS, e tutte le risorse di archiviazione locali definite.

* Il disco delle app (applicazioni) è il disco in cui viene estratto il file con estensione cspkg e che contiene il sito Web, i file binari, il processo host del ruolo, le attività di avvio, il file web.config e così via.

* Le dimensioni delle macchine virtuali A8/A10 e A9/A11 hanno la stessa capacità. Le istanze di macchine virtuali A8 e A9 includono una scheda di rete aggiuntiva connessa a una rete con accesso diretto a memoria remota (RDMA) per una comunicazione rapida tra macchine virtuali. Le istanze A8 e A9 sono progettate per applicazioni di elaborazione ad alte prestazioni che richiedono comunicazione costante e a bassa latenza tra i nodi durante l'esecuzione, ad esempio, le applicazioni che usano l'interfaccia MPI (Message Passing Interface). Le istanze di macchina virtuale A10 e A11 non includono la scheda di rete aggiuntiva. Le istanze A10 e A11 sono progettate per applicazioni di calcolo ad alte prestazioni che non richiedono una comunicazione costante e a bassa latenza tra i nodi, note anche come applicazioni parametriche o imbarazzantemente parallele.

|Dimensione|Core<br>CPU|Memoria|Dimensione disco|
|---|---|---|---|
|Molto piccola|1|768 MB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 19 GB<br/>App = circa 1,5 GB|
|Piccola|1|1,75 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 224 GB<br/>App = circa 1,5 GB|
|Media|2|3,5 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 489 GB<br/>App = circa 1,5 GB|
|Grande|4|7 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 999 GB<br/>App = circa 1,5 GB|
|Molto grande|8|14 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 2.039 GB<br/>App = circa 1,5 GB|
|A5|2|14 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 489 GB<br/>App = circa 1,5 GB|
|A6|4|28 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 999 GB<br/>App = circa 1,5 GB|
|A7|8|56 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 2.039 GB<br/>App = circa 1,5 GB
|A8|8|56 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 1,77 TB<br/>App = circa 1,5 GB<blockquote>Nota: per informazioni e considerazioni sull’uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo</a>.</blockquote>|
|A9|16|112 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 1,77 TB<br/>App = circa 1,5 GB<blockquote>Nota: per informazioni e considerazioni sull’uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo</a>.</blockquote>|
|A10|8|56 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 1,77 TB<br/>App = circa 1,5 GB<blockquote>Nota: per informazioni e considerazioni sull’uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo</a>.</blockquote>|
|A11|16|112 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 1,77 TB<br/>App = circa 1,5 GB<blockquote>Nota: per informazioni e considerazioni sull’uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo</a>.</blockquote>|
|Standard\_D1|1|3,5 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 50 GB<br/>App = circa 1,5 GB|
|Standard\_D2|2|7 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 100 GB<br/>App = circa 1,5 GB|
|Standard\_D3|4|14 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 200 GB<br/>App = circa 1,5 GB|
|Standard\_D4|8|28 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 400 GB<br/>App = circa 1,5 GB|
|Standard\_D11|2|14 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 100 GB<br/>App = circa 1,5 GB|
|Standard\_D12|4|28 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 200 GB<br/>App = circa 1,5 GB|
|Standard\_D13|8|56 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 400 GB<br/>App = circa 1,5 GB|
|Standard\_D14|16|112 GB|Sistema operativo = dimensioni sistema operativo guest<br/>Risorsa locale = 800 GB<br/>App = circa 1,5 GB|

## Configurare le dimensioni per i servizi Cloud

È possibile specificare la dimensione di un'istanza del ruolo di macchina virtuale come parte del modello del servizio descritto dal file csdef. Le dimensioni di un ruolo determinano il numero di memorie centrali CPU, la capacità di memoria e la dimensione del file system locale allocati a una istanza in esecuzione. Scegliere le dimensioni del ruolo in base ai requisiti delle risorse dell'applicazione.

Di seguito è riportato un esempio per impostare la dimensione ridotta del ruolo per un'istanza del ruolo Web:


    <WebRole name="WebRole1" vmsize="Small">
    …
    </WebRole>
## Passaggi successivi

[Configurare un servizio cloud per Azure](https://msdn.microsoft.com/library/hh124108)

<!---HONumber=August15_HO6-->