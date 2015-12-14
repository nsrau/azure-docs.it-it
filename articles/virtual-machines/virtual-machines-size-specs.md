<properties
 pageTitle="Dimensioni delle macchine virtuali | Microsoft Azure"
 description="Elenca le diverse dimensioni per le macchine virtuali e le relative capacità."
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="09/02/2015"
 ms.author="cynthn"/>

# Dimensioni delle macchine virtuali

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Panoramica

In questo articolo vengono descritte le dimensioni e le opzioni disponibili per le risorse di calcolo basate sulla macchina virtuale che è possibile usare per eseguire le applicazioni e i carichi di lavoro. Offre anche considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse.

Le macchine virtuali e i servizi cloud di Azure sono due dei diversi tipi di risorse di calcolo offerte da Azure. Per informazioni, vedere [Opzioni di hosting di calcolo fornite da Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926).

>[AZURE.NOTE]Per i limiti di Azure correlati, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## Considerazioni sulla pianificazione

Le macchine virtuali sono disponibili in due livelli, Basic e Standard. Entrambi i tipi offrono una gamma di dimensioni, ma il livello Basic non offre alcune funzionalità disponibili con il livello Standard, ad esempio il bilanciamento del carico e la scalabilità automatica. Il livello di dimensioni Standard è costituito da diverse serie: A, D, DS, G e GS. Si tengano presenti le considerazioni seguenti per alcune di queste dimensioni:

*   Le macchine virtuali serie D sono progettate per eseguire le applicazioni che richiedono maggiore potenza di calcolo e prestazioni del disco temporaneo. Le macchine virtuali serie D forniscono processori più veloci, un rapporto tra memoria e memoria centrale superiore e un'unità SSD ( solid-state drive) per il disco temporaneo. Per informazioni dettagliate, vedere l'annuncio sul blog di Azure [Nuove dimensioni delle macchine virtuali serie D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

*   Serie Dv2, una versione successiva della serie D originale, dotata di una CPU più potente. La CPU della serie Dv2 è circa il 35% più rapida rispetto alla CPU della serie D. È basata sul processore Intel Xeon® E5-2673 v3 a 2,4 GHz (Haswalle) e con tecnologia Intel Turbo Boost 2.0 può funzionare fino a 3,2 GHz. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

    La disponibilità internazionale della serie Dv2 sarà basata su questa pianificazione: Ottobre 2015: Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-settentrionali, Stati Uniti occidentali Novembre 2015: Stati Uniti orientali, Europa settentrionale, Europa occidentale Gennaio 2016: Stati Uniti centro-meridionali, APAC orientale, APAC sud-orientale, Giappone orientale, Giappone occidentale, Australia orientale, Australia sud-orientale, Brasile meridionale


*   Le macchine virtuali serie G offrono le dimensioni maggiori e le prestazioni migliori e vengono eseguite su host con processori della famiglia Intel Xeon E5 V3.

*   Le macchine virtuali serie DS e GS possono usare l'archiviazione Premium, che offre un'archiviazione ad alte prestazioni e a bassa latenza per carichi di lavoro con uso intensivo di I/O. Usano le unità SSD (Solid State Drive) per ospitare i dischi della macchina virtuale e offrono una cache del disco SSD locale. L'archiviazione Premium è disponibile solo in determinate aree geografiche. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage-premium-storage-preview-portal.md).

La dimensione della macchina virtuale influisce sul prezzo. Influisce, inoltre, sull’elaborazione, sulla memoria e sulla capacità di archiviazione della macchina virtuale. I costi di archiviazione vengono calcolati separatamente in base alle pagine usate nell'account di archiviazione. Per ulteriori informazioni, vedere [Dettagli prezzi per le macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/) e [Dettagli prezzi di archiviazione](http://azure.microsoft.com/pricing/details/storage/). Per altre informazioni dettagliate sull'archiviazione per le macchine virtuali, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-disks-vhds.md).

Le considerazioni seguenti potrebbero essere utili all’utente per scegliere una dimensione:

*   La dimensione A0\\Basic\_A0 è disponibile solo tramite Azure SDK versione 1.3 o successiva.  

*   A1\\Basic\_A1 corrisponde alla dimensione minima consigliata per i carichi di lavoro di produzione.

*   Selezionare una macchina virtuale con 4 o 8 core CPU quando si usa SQL Server Enterprise Edition.

*   Alcuni degli host fisici nei data center di Azure potrebbero non supportare macchine virtuali di dimensioni superiori, ad esempio da A5 ad A11. Di conseguenza, potrebbe essere visualizzato il messaggio di errore **Impossibile configurare la macchina virtuale <machine name>** o **Impossibile creare la macchina virtuale <machine name>** quando si configura una macchina virtuale esistente in base a una nuova dimensione, si crea una nuova macchina virtuale in una rete virtuale creata prima del 16 aprile 2013 o si aggiunge una nuova macchina a un servizio cloud esistente. Vedere l'argomento [Errore: "Impossibile configurare la macchina virtuale"](https://social.msdn.microsoft.com/Forums/it-IT/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) nel forum di supporto per le soluzioni alternative per ogni scenario di distribuzione.

*   Le dimensioni delle macchine virtuali A8/A10 e A9/A11 hanno le stesse capacità. Le istanze di macchine virtuali A8 e A9 includono una scheda di rete aggiuntiva connessa a una rete con accesso diretto a memoria remota (RDMA) per una comunicazione rapida tra macchine virtuali. Le istanze A8 e A9 sono progettate per applicazioni di elaborazione ad alte prestazioni che richiedono comunicazione costante e a bassa latenza tra i nodi durante l'esecuzione, ad esempio, le applicazioni che usano l'interfaccia MPI (Message Passing Interface). Le istanze di macchina virtuale A10 e A11 non includono la scheda di rete aggiuntiva. Le istanze A10 e A11 sono progettate per applicazioni di calcolo ad alte prestazioni che non richiedono una comunicazione costante e a bassa latenza tra i nodi, note anche come applicazioni parametriche o imbarazzantemente parallele.

*   La serie Dv2 e la serie D sono ideali per le applicazioni che richiedono CPU più veloci, prestazioni migliori del disco locale o requisiti di memoria superiori. Offrono una potente combinazione per molte applicazioni di livello aziendale.



## Limiti generali

Questa tabella mostra i limiti applicati (indipendentemente dalle dimensioni di una macchina virtuale) per le macchine virtuali create usando il modello di distribuzione di Gestione servizi.

[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

Questa tabella mostra i limiti applicati (indipendentemente dalle dimensioni di una macchina virtuale) per le macchine virtuali create usando il modello di distribuzione di Gestione risorse.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## Tabelle delle dimensioni

Le tabelle seguenti illustrano le dimensioni e le capacità offerte da ogni dimensione.

>[AZURE.NOTE]La capacità di archiviazione è rappresentata usando 1024 ^ 3 byte come unità di misura per GB. Questa unità di misura è definita talvolta gibibyte o definizione di base 2. Quando si confrontano dimensioni che usano sistemi di base diversi, è necessario ricordare che le dimensioni di base 2 potrebbero risultare più piccole di quelli di base 10 ma per qualsiasi dimensione specifica (ad esempio, 1 GB) un sistema di base 2 fornisce maggiore capacità di un sistema di base 10, perché 1024^3 è superiore a 1000^3.

## Livello Basic

|Dimensione: portale di Azure classico\\cmdlet classico e API|Core CPU|Memoria|NIC (Max)|Dimensioni max. disco : macchina virtuale|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (300 per disco)|
|---|---|---|---|---|---|---|
|A0\\Basic\_A0|1|768 MB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 20 GB</p>|1|1x300|
|A1\\Basic\_A1|1|1,75 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 40 GB</p>|2|2x300|
|A2\\Basic\_A2|2|3,5 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 60 GB</p>|4|4x300|
|A3\\Basic\_A3|4|7 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 120 GB</p>|8|8x300|
|A4\\Basic\_A4|8|14 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 240 GB</p>|16|16x300|

## Livello Standard: serie A

|Dimensione: portale di Azure classico\\cmdlet classico e API|Core CPU|Memoria|NIC (Max)|Dimensioni max. disco : macchina virtuale|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)|
|---|---|---|---|---|---|---|
|A0\\ExtraSmall|1|768 MB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 20 GB</p>|1|1x500|
|A1\\piccola|1|1,75 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 70 GB</p>|2|2x500|
|A2\\media|2|3,5 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 135 GB</p>|4|4x500|
|A3\\grande|4|7 GB|2|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 285 GB</p>|8|8x500|
|A4\\ExtraLarge|8|14 GB|4|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 605 GB</p>|16|16x500|
|A5\\uguale|2|14 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 135 GB</p>|4|4X500|
|A6\\uguale|4|28 GB|2|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 285 GB</p>|8|8x500|
|A7\\uguale|8|56 GB|4|<p>Sistema operativo = 1023 GB</p><p>Temporaneo = 605 GB</p>|16|16x500|
|A8\\uguale|8|56 GB|2|<p><p>Sistema operativo = 1023 GB</p><p>Temporaneo = 382 GB</p><blockquote><p>Nota: per informazioni e considerazioni sull'uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato uso di calcolo</a>.</p></blockquote>|16|16x500|
|A9\\uguale|16|112 GB|4|<p><p>Sistema operativo = 1023 GB</p><p>Temporaneo = 382 GB</p><blockquote><p>Nota: per informazioni e considerazioni sull'uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato uso di calcolo</a>.</p></blockquote>|16|16x500|
|A10\\uguale|8|56 GB|2|<p><p>Sistema operativo = 1023 GB</p><p>Temporaneo = 382 GB</p><blockquote><p>Nota: per informazioni e considerazioni sull'uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato uso di calcolo</a>.</p></blockquote>|16|16x500|
|A11\\uguale|16|112 GB|4|<p><p>Sistema operativo = 1023 GB</p><p>Temporaneo = 382 GB</p><blockquote><p>Nota: per informazioni e considerazioni sull'uso di questa dimensione, vedere <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Informazioni sulle istanze A8, A9, A10 e A11 a elevato uso di calcolo</a>.</p></blockquote>|16|16x500|

## Livello Standard: serie D

|Dimensione: portale di Azure classico\\cmdlet classico e API|Core CPU|Memoria|NIC (Max)|Dimensioni max. disco : macchina virtuale|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)|
|---|---|---|---|---|---|---|
|Standard\_D1\\uguale|1|3,5 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 50 GB</p>|2|2x500|
|Standard\_D2\\uguale|2|7 GB|2|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 100 GB</p>|4|4x500|
|Standard\_D3\\uguale|4|14 GB|4|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 200 GB</p>|8|8x500|
|Standard\_D4\\uguale|8|28 GB|8|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 400 GB</p>|16|16x500|
|Standard\_D11\\uguale|2|14 GB|2|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 100 GB</p>|4|4x500|
|Standard\_D12\\uguale|4|28 GB|4|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 200 GB</p>|8|8x500|
|Standard\_D13\\uguale|8|56 GB|8|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 400 GB</p>|16|16x500|
|Standard\_D14\\uguale|16|112 GB|8|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 800 GB</p>|32|32x500|

## Livello standard: serie Dv2

|Dimensione: portale di Azure classico\\cmdlet classico e API|Core CPU|Memoria|NIC (Max)|Dimensioni max. disco : macchina virtuale|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)|
|---|---|---|---|---|---|---|
|Standard\_D1\_v2\\same|1|3,5 GB|1|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 50 GB</p>|2|2x500|
|Standard\_D2\_v2\\same|2|7 GB|2|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 100 GB</p>|4|4x500|
|Standard\_D3\_v2\\same|4|14 GB|4|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 200 GB</p>|8|8x500|
|Standard\_D4\_v2\\same|8|28 GB|8|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 400 GB</p>|16|16x500|
|Standard\_D5\_v2\\same|16|56 GB|8|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 800 GB</p>|32|32x500|
|Standard\_D11\_v2\\same|2|14 GB|2|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 100 GB</p>|4|4x500|
|Standard\_D12\_v2\\same|4|28 GB|4|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 200 GB</p>|8|8x500|
|Standard\_D13\_v2\\same|8|56 GB|8|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 400 GB</p>|16|16x500|
|Standard\_D14\_v2\\same|16|112 GB|8|<p>Sistema operativo = 1023 GB</p><p>Temporaneo (SSD) = 800 GB</p>|32|32x500|

## Livello Standard: serie DS*

|Dimensione: portale di Azure classico\\cmdlet classico e API|Core CPU|Memoria|NIC (Max)|Dimensioni max. disco : macchina virtuale|Dischi di dati max. (1023 GB ciascuno)|Dimensione cache (GB)|IOPS max. disco e larghezza di banda|
|---|---|---|---|---|---|---|---|
|Standard\_DS1\\uguale|1|3,5|1|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 7 GB</p>|2|43|<p>3.200</p><p>32 MB al secondo</p>|
|Standard\_DS2\\uguale|2|7|2|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 14 GB</p>|4|86|<p>6.400</p><p>64 MB al secondo</p>|
|Standard\_DS3\\uguale|4|14|4|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 28 GB</p>|8|172|<p>12.800</p><p>128 MB al secondo</p>|
|Standard\_DS4\\uguale|8|28|8|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 56 GB</p>|16|344|<p>25.600</p><p>256 MB al secondo</p>|
|Standard\_DS11\\uguale|2|14|2|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 28 GB</p>|4|72|<p>6.400</p><p>64 MB al secondo</p>|
|Standard\_DS12\\uguale|4|28|4|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 56 GB</p>|8|144|<p>12.800</p><p>128 MB al secondo</p>|
|Standard\_DS13\\uguale|8|56|8|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 112 GB</p>|16|288|<p>25.600</p><p>256 MB al secondo</p>|
|Standard\_DS14\\uguale|16|112|8|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 224 GB</p>|32|576|<p>50.000</p><p>512 MB al secondo</p>|

**Il numero massimo di operazioni di input/output al secondo (IOPS) e la velocità effettiva (larghezza di banda) possibili con una macchina virtuale serie DS dipende dalla dimensione del disco. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage-premium-storage-preview-portal.md).

## Livello Standard: serie G

|Dimensioni: portale di Azure classico\\cmdlet e API|Core CPU|Memoria|NIC (Max)|Dimensioni max. disco : macchina virtuale|Dischi di dati max. (1023 GB ciascuno)|Max. IOPS (500 per disco)|
|---|---|---|---|---|---|---|
|Standard\_G1\\uguale|2|28 GB|1|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 384 GB</p>|4|4x500|
|Standard\_G2\\uguale|4|56 GB|2|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 768 GB</p>|8|8x500|
|Standard\_G3\\uguale|8|112 GB|4|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 1.536 GB</p>|16|16x500|
|Standard\_G4\\uguale|16|224 GB|8|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 3.072 GB</p>|32|32x500|
|Standard\_G5\\uguale|32|448 GB|8|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 6.144 GB</p>|64|<p>64x500</p>|

## Livello Standard: serie GS

|Dimensione: portale di Azure classico\\cmdlet classico e API|Core CPU|Memoria|NIC (Max)|Dimensioni max. disco : macchina virtuale|Dischi di dati max. (1023 GB ciascuno)|Dimensione cache (GB)|IOPS max. disco e larghezza di banda|
|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 56 GB</p>|4|264|<p>5.000</p><p>125 MB al secondo</p>|
|Standard\_GS2|4|56|2|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 112 GB</p>|8|528|<p>10.000</p><p>250 MB al secondo</p>|
|Standard\_GS3|8|112|4|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 224 GB</p>|16|1056|<p>20.000</p><p>500 MB al secondo</p>|
|Standard\_GS4|16|224|8|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 448 GB</p>|32|2112|<p>40.000</p><p>1.000 MB al secondo</p>|
|Standard\_GS5|32|448|8|<p>Sistema operativo = 1023 GB</p><p>Disco SSD locale = 896 GB</p>|64|4224|<p>80.000</p><p>2.000 MB al secondo</p>|


### Vedere anche

[Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=AcomDC_1203_2015-->