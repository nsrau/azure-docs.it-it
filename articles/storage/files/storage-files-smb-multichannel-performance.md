---
title: Prestazioni SMB multicanale-File di Azure
description: Informazioni sulle prestazioni SMB multicanale.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: ee3d1335de1b2bb3096e88c4d04cd03daaa665f5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "96014101"
---
# <a name="smb-multichannel-performance"></a>Prestazioni di SMB multicanale

File di Azure SMB multicanale (anteprima) consente a un client SMB 3. x di stabilire più connessioni di rete alle condivisioni file Premium in un account di archiviazione filestorage. Il protocollo SMB 3,0 ha introdotto la funzionalità SMB multicanale nei client Windows Server 2012 e Windows 8. Per questo motivo, qualsiasi File di Azure client SMB 3. x che supporta SMB multicanale può sfruttare la funzionalità per le condivisioni file Premium di Azure. Non sono previsti costi aggiuntivi per l'abilitazione di SMB multicanale in un account di archiviazione.

## <a name="benefits"></a>Vantaggi

File di Azure SMB multicanale consente ai client di usare più connessioni di rete che garantiscono un miglioramento delle prestazioni, riducendo al contempo il costo di proprietà. Il miglioramento delle prestazioni viene effettuato tramite l'aggregazione della larghezza di banda su più schede di rete e l'utilizzo del supporto RSS (Receive Side Scaling) per la distribuzione del carico i/o tra più CPU.

- **Aumento della velocità effettiva**: più connessioni consentono il trasferimento dei dati su più percorsi in parallelo e, di conseguenza, i carichi di lavoro che usano dimensioni di file maggiori con dimensioni di i/o maggiori e richiedono una velocità effettiva elevata da un'unica macchina virtuale o un set di macchine virtuali più piccolo. Alcuni di questi carichi di lavoro includono supporti e intrattenimento per la creazione di contenuti o la transcodifica, genomica e analisi dei rischi di servizi finanziari.
- **IOPS più elevata**: la funzionalità RSS NIC consente una distribuzione efficace del carico tra più CPU con più connessioni. Questo consente di ottenere una scalabilità IOPS più elevata e un utilizzo efficace delle CPU VM. Questa operazione è utile per i carichi di lavoro con dimensioni di i/o ridotte, ad esempio le applicazioni di database.
- **Tolleranza di errore di rete**: più connessioni attenuano il rischio di rotture perché i client non si basano più su una singola connessione.
- **Configurazione automatica**: quando SMB multicanale è abilitato per i client e gli account di archiviazione, consente l'individuazione dinamica delle connessioni esistenti e può creare percorsi di connessione aggiuntivi, se necessario.
- **Ottimizzazione dei costi**: i carichi di lavoro possono ottenere una scalabilità più elevata da un'unica macchina virtuale o da un piccolo set di macchine virtuali, durante la connessione a condivisioni Premium. Questo potrebbe ridurre il costo totale di proprietà riducendo il numero di macchine virtuali necessarie per l'esecuzione e la gestione di un carico di lavoro.

Per ulteriori informazioni su SMB multicanale, consultare la [documentazione di Windows](/azure-stack/hci/manage/manage-smb-multichannel).

Questa funzionalità offre maggiori vantaggi a livello di prestazioni per le applicazioni multithread, ma in genere non consente le applicazioni a thread singolo. Per ulteriori informazioni, vedere la sezione [confronto delle prestazioni](#performance-comparison) .

## <a name="limitations"></a>Limitazioni

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Configurazione

SMB multicanale funziona solo quando la funzionalità è abilitata sia sul lato client (client) che sul lato servizio (l'account di archiviazione di Azure).

Nei client Windows SMB multicanale è abilitato per impostazione predefinita. È possibile verificare la configurazione eseguendo il comando PowerShell seguente: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Nell'account di archiviazione di Azure sarà necessario abilitare SMB multicanale. Vedere [abilitare SMB multicanale (anteprima)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Disabilitare SMB multicanale
Nella maggior parte degli scenari, in particolare per i carichi di lavoro multithread, i client dovrebbero ottenere prestazioni migliori con SMB multicanale. Tuttavia, in alcuni scenari specifici, ad esempio carichi di lavoro a thread singolo o a scopo di test, potrebbe essere necessario disabilitare SMB multicanale. Per ulteriori informazioni, vedere [confronto delle prestazioni](#performance-comparison) .

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Verificare che SMB multicanale sia configurato correttamente

1. Creare una condivisione file Premium o utilizzarne una esistente.
1. Verificare che il client supporti SMB multicanale (per una o più schede di rete è abilitato Receive-Side Scaling). Per altri dettagli, vedere la [documentazione di Windows](/azure-stack/hci/manage/manage-smb-multichannel) .
1. Montare una condivisione file nel client.
1. Genera il carico con l'applicazione.
    Uno strumento di copia, ad esempio Robocopy/MT, o qualsiasi strumento per le prestazioni, ad esempio Diskspd, per i file di lettura/scrittura può generare un carico.
1. Aprire PowerShell come amministratore e usare il comando seguente: `Get-SmbMultichannelConnection |fl`
1. Cercare le proprietà **MaxChannels** e **CurrentChannels**

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Screenshot dei risultati di Get-smbmultichannelconnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Confronto delle prestazioni

Esistono due categorie di modelli di carico di lavoro di lettura/scrittura, a thread singolo e multithread. La maggior parte dei carichi di lavoro usa più file, ma possono essere presenti casi d'uso specifici in cui il carico di lavoro funziona con un singolo file in una condivisione. In questa sezione vengono illustrati i diversi casi d'uso e l'effetto sulle prestazioni per ognuno di essi. In generale, la maggior parte dei carichi di lavoro è multithreading e distribuisce il carico di lavoro su più file, in modo da poter osservare miglioramenti significativi delle prestazioni con SMB multicanale.

- **Multi-threading/più file**: a seconda del modello di carico di lavoro, si noterà un miglioramento significativo delle prestazioni in lettura e scrittura di iOS su più canali. I miglioramenti in termini di prestazioni variano da qualsiasi punto compreso tra 2x e 4x in termini di IOPS, velocità effettiva e latenza. Per questa categoria, è necessario abilitare SMB multicanale per ottenere prestazioni ottimali.
- Multithread **/singolo file**: per la maggior parte dei casi d'uso in questa categoria, i carichi di lavoro trarranno vantaggio dalla presenza di SMB multicanale abilitato, soprattutto se il carico di lavoro ha dimensioni medie di i/o > ~ 16K. Alcuni scenari di esempio che traggono vantaggio da SMB multicanale sono il backup o il ripristino di un singolo file di grandi dimensioni. Un'eccezione che può essere utile disabilitare SMB multicanale è se il carico di lavoro è di piccole dimensioni IOs. In tal caso, è possibile che si verifichi una lieve riduzione delle prestazioni di circa il 10%. A seconda del caso d'uso, valutare la possibilità di distribuire il carico tra più file o disabilitare la funzionalità. Per informazioni dettagliate, vedere la sezione [configurazione](#configuration) .
- **File a thread singolo o file singolo**: per la maggior parte dei carichi di lavoro a thread singolo, i vantaggi a livello di prestazioni minimi sono dovuti alla mancanza di parallelismo, in genere esiste una lieve riduzione delle prestazioni di circa il 10% se SMB multicanale è abilitato. In questo caso, è ideale disabilitare SMB multicanale, con un'unica eccezione. Se il carico di lavoro a thread singolo è in grado di distribuire il carico tra più file e usa una dimensione di i/o media più grande (> ~ 16K), si dovrebbero ottenere lievi vantaggi a livello di prestazioni da SMB multicanale.

### <a name="performance-test-configuration"></a>Configurazione del test delle prestazioni

Per i grafici in questo articolo è stata usata la configurazione seguente: una singola VM D32s v3 standard con una singola scheda di interfaccia di rete abilitata per RSS con quattro canali. Il carico è stato generato con diskspd.exe, a thread multipli con profondità i/o di 10 e IOs casuale con diverse dimensioni di i/o.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Screenshot dei risultati di Get-smbmultichannelconnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Mutli-thread/più file con SMB multicanale

Il carico è stato generato su 10 file con diverse dimensioni di i/o. I risultati dei test con scalabilità verticale hanno migliorato significativamente i risultati dei test di IOPS e della velocità effettiva con SMB multicanale abilitato. I diagrammi seguenti illustrano i risultati:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagramma delle prestazioni" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagramma delle prestazioni della velocità effettiva." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- In una singola scheda di interfaccia di rete, per le letture è stato osservato un aumento delle prestazioni di 2x-3x e per le Scritture, i guadagni di 3x-4x in termini di IOPS e velocità effettiva.
- SMB multicanale ha consentito IOPS e velocità effettiva per raggiungere i limiti delle VM anche con una singola scheda di interfaccia di rete e il limite di quattro canali.
- Poiché l'uscita (o la lettura nella risorsa di archiviazione) non è a consumo, la velocità effettiva di lettura è riuscita a superare il limite pubblicato della macchina virtuale di 16.000 Mbps (2 GiB/s). Il test è risultato >2,7 GiB/s. Il traffico in ingresso (o scritture nell'archiviazione) è ancora soggetto a limiti di VM.
- Distribuzione del carico su più file consentiti per miglioramenti sostanziali.

Un comando di esempio usato in questo test è: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Carichi di lavoro con più thread o singoli file con SMB multicanale

Il carico è stato generato in base a un singolo file GiB 128. Con SMB multicanale abilitato, nella maggior parte dei casi il test di scalabilità verticale con file multithread/singoli ha mostrato miglioramenti. I diagrammi seguenti illustrano i risultati:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagramma delle prestazioni di IOPS." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagramma delle prestazioni della velocità effettiva di un singolo file." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- In una singola scheda di interfaccia di rete con dimensioni di i/o medie maggiori (> ~ 16K), sono stati apportati miglioramenti significativi nelle operazioni di lettura e scrittura.
- Per le dimensioni di i/o più piccole, si è verificato un lieve effetto del 10% sulle prestazioni quando SMB multicanale è stato abilitato. Questo problema può essere risolto diffondendo il carico su più file o disabilitando la funzionalità.
- Le prestazioni sono ancora limitate da  [limiti di file singolo](storage-files-scale-targets.md#file-level-limits).

## <a name="optimizing-performance"></a>Ottimizzazione delle prestazioni

I suggerimenti seguenti possono aiutare a ottimizzare le prestazioni:

- Verificare che l'account di archiviazione e il client si trovino nella stessa area di Azure per ridurre la latenza di rete.
- Usare applicazioni multithread e distribuire il carico su più file.
- Vantaggi in merito alle prestazioni dell'incremento multicanale SMB con il numero di file che distribuiscono il carico.
- Le prestazioni di condivisione Premium sono limitate dalle dimensioni della condivisione di cui è stato effettuato il provisioning (IOPS/uscita/ingresso) e dai limiti dei singoli file. Per informazioni dettagliate, vedere [informazioni sul provisioning per le condivisioni file Premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- Il numero massimo di prestazioni di un singolo client VM è ancora associato ai limiti della macchina virtuale. Ad esempio, [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) può supportare una larghezza di banda massima di 16.000 Mbps (o 2 Gbps), l'uscita dalla macchina virtuale (scritture nell'archiviazione) è a consumo, il traffico in ingresso (letture dall'archiviazione) non lo è. Le prestazioni della condivisione file sono soggette ai limiti della rete, alle CPU, alla larghezza di banda di rete disponibile per l'archiviazione interna, alle dimensioni di i/o, al parallelismo e ad altri fattori.
- Il test iniziale è in genere un riscaldamento, ne elimina i risultati e ripete il test.
- Se le prestazioni sono limitate da un singolo client e il carico di lavoro è ancora inferiore ai limiti della condivisione con provisioning, è possibile ottenere prestazioni più elevate distribuendo il carico su più client.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>Relazione tra IOPS, velocità effettiva e dimensioni di i/o

**Velocità effettiva = IO dimensioni * IOPS**

Le dimensioni di i/o superiori comportano una maggiore velocità effettiva e avranno latenze più elevate, ottenendo un numero inferiore di IOPS netti. Le dimensioni di i/o più piccole comportano operazioni di i/o al secondo più elevate, ma hanno

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare SMB multicanale in un account filestorage (anteprima)](storage-files-enable-smb-multichannel.md)
- Per ulteriori informazioni su SMB multicanale, vedere la [documentazione di Windows](/azure-stack/hci/manage/manage-smb-multichannel) .
