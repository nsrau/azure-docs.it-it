<properties 
 pageTitle="Dimensioni dei servizi cloud" 
 description="Elenca le varie dimensioni dei ruoli Web e di lavoro." 
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
 ms.date="05/20/2015" 
 ms.author="adegeo"/>
 
# Dimensioni dei servizi cloud

## Panoramica 

Questo argomento descrive le dimensioni e le opzioni disponibili per le istanze del ruolo di servizio cloud (ruoli Web e di lavoro). Fornisce anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse.

Le macchine virtuali e i servizi cloud di Azure sono due dei diversi tipi di risorse di calcolo offerte da Azure. Per informazioni, vedere [Opzioni di hosting di calcolo fornite da Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926).

>[AZURE.NOTE]Per i limiti di Azure correlati, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md).

###Dimensioni delle istanze del ruolo Web e di lavoro

Le considerazioni seguenti potrebbero essere utili all'utente per scegliere una dimensione:

*   A questo punto è possibile configurare le istanze per l'uso di macchine virtuali serie D. Queste macchine virtuali sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD per il disco temporaneo. Per informazioni dettagliate, vedere l'annuncio sul blog di Azure [Nuove dimensioni delle macchine virtuali serie D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

*   I ruoli Web e di lavoro richiedono più spazio su disco temporaneo rispetto alle macchine virtuali di Azure a causa dei requisiti di sistema. I file di sistema riservano 4 GB di spazio per il file di paging di Windows e 2 GB di spazio per il file dump di Windows.

*   Il disco del sistema operativo contiene il sistema operativo guest di Windows, la cartella Programmi (incluse le installazioni eseguite mediante attività di avvio a meno che non si specifichi un altro disco), le modifiche del Registro di sistema, la cartella System32 e .NET Framework.

*   Il disco di risorse locale contiene i file di configurazione e i log di Azure, Diagnostica Azure, inclusi i log di IIS, e tutte le risorse di archiviazione locali definite.

*   Il disco delle app (applicazioni) è il disco in cui viene estratto il file con estensione cspkg e che contiene il sito Web, i file binari, il processo host del ruolo, le attività di avvio, il file web.config e così via.

*   Le dimensioni delle macchine virtuali A8/A10 e A9/A11 hanno la stessa capacità. Le istanze di macchine virtuali A8 e A9 includono una scheda di rete aggiuntiva connessa a una rete con accesso diretto a memoria remota (RDMA) per una comunicazione rapida tra macchine virtuali. Le istanze A8 e A9 sono progettate per applicazioni di calcolo ad alte prestazioni che richiedono una comunicazione costante e a bassa latenza tra i nodi durante l'esecuzione, ad esempio le applicazioni che usano l'interfaccia MPI (Message Passing Interface). Le istanze di macchina virtuale A10 e A11 non includono la scheda di rete aggiuntiva. Le istanze A10 e A11 sono progettate per applicazioni di calcolo ad alte prestazioni che non richiedono una comunicazione costante e a bassa latenza tra i nodi, note anche come applicazioni parametriche o imbarazzantemente parallele.

|Dimensione|Core<br>CPU|Memoria|Dimensione disco|
|---|---|---|---|
|Molto piccola|1|768 MB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 19 GB</p><p>App = circa 1,5 GB</p>|
|Piccola|1|1,75 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 224 GB</p><p>App = circa 1,5 GB</p>|
|Media|2|3,5 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 489 GB</p><p>App = circa 1,5 GB</p>|
|Grande|4|7 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 999 GB</p><p>App = circa 1,5 GB</p>|
|Molto grande|8|14 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 2039 GB</p><p>App = circa 1,5 GB</p>|
|A5|2|14 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 489 GB</p><p>App = circa 1,5 GB</p>|
|A6|4|28 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 999 GB</p><p>App = circa 1,5 GB</p>|
|A7|8|56 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 2039 GB</p><p>App = circa 1,5 GB</p>
|A8|8|56 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 1,77 TB</p><p>App = circa 1,5 GB</p><blockquote><p>[AZURE.NOTE]Per informazioni e considerazione sull’uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo</a>.</p></blockquote>|
|A9|16|<p>112 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 1,77 TB</p><p>App = circa 1,5 GB</p><blockquote><p>[AZURE.NOTE]Per informazioni e considerazione sull’uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo</a>.</p></blockquote>|
|A10|8|<p>56 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 1,77 TB</p><p>App = circa 1,5 GB</p><blockquote><p>[AZURE.NOTE]Per informazioni e considerazione sull’uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo</a>.</p></blockquote>|
|A11|16|<p>112 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 1,77 TB</p><p>App = circa 1,5 GB</p><blockquote><p>[AZURE.NOTE]Per informazioni e considerazione sull’uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo</a>.</p></blockquote>|
|Standard_D1|1|3,5 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 50 GB</p><p>App = circa 1,5 GB</p>|
|Standard_D2|2|7 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 100 GB</p><p>App = circa 1,5 GB</p>|
|Standard_D3|4|14 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 200 GB</p><p>App = circa 1,5 GB</p>|
|Standard_D4|8|28 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 400 GB</p><p>App = circa 1,5 GB</p>|
|Standard_D11|2|14 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 100 GB</p><p>App = circa 1,5 GB</p>|
|Standard_D12|4|28 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 200 GB</p><p>App = circa 1,5 GB</p>|
|Standard_D13|8|56 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 400 GB</p><p>App = circa 1,5 GB</p>|
|Standard_D14|16|112 GB|<p>Sistema operativo = dimensioni sistema operativo guest</p><p>Risorsa locale = 800 GB</p><p>App = circa 1,5 GB</p>|

### Vedere anche

#### Concetti

[Impostare un servizio cloud per Azure](https://msdn.microsoft.com/library/hh124108)

[Configurare le dimensioni dei servizi cloud](https://msdn.microsoft.com/library/ee814754)

#### Altre risorse

[Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md)

 [Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](http://go.microsoft.com/fwlink/p/?linkid=328042)

 [Dimensioni delle macchine virtuali](virtual-machines-size-specs.md)

<!---HONumber=58-->