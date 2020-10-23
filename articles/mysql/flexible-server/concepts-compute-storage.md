---
title: Opzioni di calcolo e archiviazione-database di Azure per MySQL-server flessibile
description: Questo articolo descrive le opzioni di calcolo e archiviazione in database di Azure per MySQL-server flessibile.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0755ca7e77592a2efd6d8687f9eb19eacc2f0128
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315166"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Opzioni di calcolo e archiviazione nel database di Azure per MySQL-server flessibile (anteprima)

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

È possibile creare un server flessibile per database di Azure per MySQL in uno dei tre diversi livelli di calcolo: con espansione, per utilizzo generico e con ottimizzazione per la memoria. I livelli di calcolo sono distinti in base allo SKU della VM sottostante usato serie B, serie D e serie E. La scelta del livello di calcolo e delle dimensioni determina la memoria e Vcore disponibili sul server. La stessa tecnologia di archiviazione viene usata in tutti i livelli di calcolo. Il provisioning di tutte le risorse viene effettuato a livello di server MySQL. Un server può avere uno o più database.

| Risorsa/livello | **Burst** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Serie VM| Serie B | Serie Ddsv4 | Serie Edsv4|
| vCore | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memoria per vCore | Variabile | 4 GiB | 8 GiB * |
| Dimensioni dello spazio di archiviazione | da 5 GiB a 16 TiB | da 5 GiB a 16 TiB | da 5 GiB a 16 TiB |
| Periodo di conservazione dei backup dei database | da 1 a 35 giorni | da 1 a 35 giorni | da 1 a 35 giorni |

\* Fatta eccezione per lo SKU di E64ds_v4 (con ottimizzazione per la memoria) con 504 GB di memoria

Per scegliere un livello di calcolo, usare la tabella seguente come punto di partenza.

| Livello di calcolo | Carichi di lavoro di destinazione |
|:-------------|:-----------------|
| Burst | Migliore per i carichi di lavoro che non necessitano di una CPU completa in modo continuo. |
| Utilizzo generico | La maggior parte dei carichi di lavoro aziendali che richiedono risorse di calcolo e di memoria bilanciate con velocità effettiva di I/O scalabile. Ad esempio, server per l'hosting di app Web e di app per dispositivi mobili e altre applicazioni aziendali.|
| Con ottimizzazione per la memoria | Carichi di lavoro di database ad alte prestazioni che richiedono prestazioni in memoria per l'elaborazione più rapida delle transazioni e una concorrenza maggiore. Ad esempio, server per l'elaborazione di dati in tempo reale e app transazionali o analitiche a prestazioni elevate.|

Dopo aver creato un server, il livello di calcolo, le dimensioni di calcolo e le dimensioni di archiviazione sono stati modificati. Il ridimensionamento del calcolo richiede un riavvio e richiede tra 60-120 secondi, mentre il ridimensionamento dell'archiviazione non richiede il riavvio. È anche possibile modificare in modo indipendente il periodo di conservazione dei backup. Per ulteriori informazioni, vedere la sezione [scale Resources](#scale-resources) .

## <a name="compute-tiers-size-and-server-types"></a>Livelli di calcolo, dimensioni e tipi di server

Le risorse di calcolo possono essere selezionate in base al livello e alle dimensioni. Ciò determina la Vcore e le dimensioni della memoria. VCore rappresentano la CPU logica dell'hardware sottostante.

Le specifiche dettagliate dei tipi di server disponibili sono le seguenti:

| Dimensioni di calcolo         | vCore | Dimensioni memoria (GiB) | 
|----------------------|--------|-------------------|
| **Burst**        |        |                   | 
| Standard_B1s         | 1      | 1                 |  
| Standard_B1ms        | 1      | 2                 | 
| Standard_B2s         | 2      | 4                 |  
| **Utilizzo generico**  |        |                   | 
| Standard_D2ds_v4     | 2      | 8                 |  
| Standard_D4ds_v4     | 4      | 16                | 
| Standard_D8ds_v4     | 8      | 32                | 
| Standard_D16ds_v4    | 16     | 64                | 
| Standard_D32ds_v4    | 32     | 128               |  
| Standard_D48ds_v4    | 48     | 192               |  
| Standard_D64ds_v4    | 64     | 256               | 
| **Con ottimizzazione per la memoria** |        |                   |
| Standard_E2ds_v4     | 2      | 16                |
| Standard_E4ds_v4     | 4      | 32                |
| Standard_E8ds_v4     | 8      | 64                |
| Standard_E16ds_v4    | 16     | 128               |
| Standard_E32ds_v4    | 32     | 256               |
| Standard_E48ds_v4    | 48     | 384               |
| Standard_E64ds_v4    | 64     | 504               |

Per ottenere altri dettagli sulle serie di calcolo disponibili, vedere la documentazione relativa alle macchine virtuali [di Azure per utilizzo generico per la](../../virtual-machines/sizes-b-series-burstable.md)serie [Ddsv4](../../virtual-machines/ddv4-ddsv4-series.md)e con ottimizzazione per la [memoria (serie Edsv4)](../../virtual-machines/edv4-edsv4-series.md).

## <a name="storage"></a>Archiviazione

L'archiviazione di cui si esegue il provisioning è la quantità di capacità di archiviazione disponibile per il server flessibile. Lo spazio di archiviazione viene usato per i file di database, i file temporanei, i log delle transazioni e i log del server MySQL. In tutti i livelli di calcolo, lo spazio di archiviazione minimo supportato è 5 GiB e il valore massimo è 16 TiB. Lo spazio di archiviazione viene scalato in incrementi di 1 GiB e può essere ridimensionato dopo la creazione del server.

>[!NOTE]
> Lo spazio di archiviazione può essere scalato solo, non inattivo.

È possibile monitorare il consumo di memoria nell'portale di Azure (con monitoraggio di Azure) usando il limite di archiviazione, la percentuale di archiviazione e le metriche di archiviazione usate. Per informazioni sulle metriche, vedere l' [articolo monitoraggio](./concepts-monitoring.md) . 

### <a name="reaching-the-storage-limit"></a>Raggiungimento del limite di archiviazione

Quando lo spazio di archiviazione utilizzato sul server si avvicina al raggiungimento del limite di cui è stato effettuato il provisioning, il server viene impostato in modalità di sola lettura per proteggere le Scritture perse nel server. I server con spazio di archiviazione con provisioning inferiore a 100 GiB sono contrassegnati come di sola lettura se lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione di cui è stato effettuato il provisioning. I server con più di 100 di spazio di archiviazione di cui è stato effettuato il provisioning sono contrassegnati come di sola lettura quando l'archiviazione gratuita è inferiore a 5 GiB.

Se, ad esempio, è stato effettuato il provisioning di 110 GiB di archiviazione e l'utilizzo effettivo supera 105 GiB, il server è contrassegnato come di sola lettura. In alternativa, se è stato effettuato il provisioning di 5 GiB di archiviazione, il server è contrassegnato come di sola lettura quando lo spazio di archiviazione disponibile è inferiore a 256 MB.

Mentre il servizio tenta di impostare il server come sola lettura, tutte le nuove richieste di transazione di scrittura vengono bloccate e le transazioni attive esistenti continueranno a essere eseguite. Quando il server è impostato su sola lettura, tutte le operazioni di scrittura e i commit delle transazioni successivi avranno esito negativo. Le query in lettura continueranno a funzionare senza interruzioni. 

Per impostare la modalità di sola lettura per il server, è necessario aumentare lo spazio di archiviazione di cui è stato effettuato il provisioning nel server. Questa operazione può essere eseguita usando il portale di Azure o l'interfaccia della riga di comando di Azure. Una volta aumentata, il server sarà pronto ad accettare di nuovo le transazioni di scrittura.

È consigliabile configurare un avviso per ricevere una notifica quando l'archiviazione server sta per raggiungere la soglia in modo tale da evitare di ottenere lo stato di sola lettura. Per informazioni sulle metriche disponibili, vedere l' [articolo monitoraggio](./concepts-monitoring.md) . 

Si consiglia di <!--turn on storage auto-grow or to--> configurare un avviso per ricevere una notifica quando l'archiviazione del server raggiunge la soglia, in modo da evitare di accedere allo stato di sola lettura. Per ulteriori informazioni, vedere la documentazione relativa alla documentazione degli avvisi [come configurare un avviso](how-to-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Aumento automatico dell'archiviazione

L'aumento automatico delle dimensioni dell'archiviazione non è ancora disponibile per il server flessibile di database di Azure per MySQL.

## <a name="iops"></a>Operazioni di I/O al secondo
Il valore minimo di IOPS effettivo è 100 per tutte le dimensioni di calcolo e il numero massimo di IOPS effettivi è determinato da entrambi gli attributi seguenti: 
- Calcolo: il numero massimo di operazioni di i/o al secondo effettive potrebbe essere limitato dal numero massimo di IOPS disponibili delle dimensioni di calcolo selezionate.
- Archiviazione: in tutti i livelli di calcolo, la scala IOPS con le dimensioni di archiviazione di cui è stato effettuato il provisioning in un rapporto 3:1.

È possibile ridimensionare i valori di IOPS effettivi aumentando l'archiviazione di cui è stato effettuato il provisioning o passando a dimensioni di calcolo maggiori (se le operazioni di i/o al secondo sono limitate dal calcolo) In anteprima, il numero massimo di IOPS efficaci supportato è 20.000 IOPS.

Per altre informazioni sul numero massimo di operazioni di i/o al secondo per calcolo, usando la combinazione di calcolo e archiviazione, vedere di seguito: 

| Dimensioni di calcolo         | Numero massimo di IOPS effettivi  | 
|----------------------|---------------------|
| **Burst**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Utilizzo generico**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **Con ottimizzazione per la memoria** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

Il valore massimo di IOPS effettivo dipende dal numero massimo di IOPS disponibili per ogni dimensione di calcolo. Vedere la formula seguente e fare riferimento alla colonna *numero massimo di velocità effettiva del disco non memorizzato nella cache: IOPS/Mbps* nella documentazione della serie [B](../../virtual-machines/sizes-b-series-burstable.md), della serie [Ddsv4](../../virtual-machines/ddv4-ddsv4-series.md)e della [serie Edsv4](../../virtual-machines/edv4-edsv4-series.md) .

**Numero massimo di IOPS effettivi** = minimo (*"velocità effettiva massima del disco non memorizzato nella cache: IOPS/Mbps"* di dimensioni di calcolo, archiviazione con provisioning in GIB * 3)

È possibile monitorare il consumo di I/O nella portale di Azure (con monitoraggio di Azure) [usando la](./concepts-monitoring.md) metrica di i/O. Se sono necessarie più operazioni di i/o al secondo, è necessario comprendere se si è limitati dalle dimensioni di calcolo o dal provisioning dell'archiviazione. Ridimensionare il provisioning di risorse di calcolo o di archiviazione del server di conseguenza.

## <a name="backup"></a>Backup

Il servizio esegue automaticamente il backup del server. È possibile selezionare un periodo di conservazione compreso tra 1 e 35 giorni. Per altre informazioni sui backup, vedere l' [articolo Concetti relativi a backup e ripristino](concepts-backup-restore.md).

## <a name="scale-resources"></a>Ridimensionare le risorse

Dopo aver creato il server, è possibile modificare in modo indipendente il livello di calcolo, le dimensioni di calcolo (VCore e memoria) e la quantità di risorse di archiviazione e il periodo di conservazione dei backup. È possibile aumentare o ridurre le dimensioni di calcolo. Il periodo di conservazione dei backup può essere aumentato o ridotto da 1 a 35 giorni. Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Il ridimensionamento delle risorse può essere eseguito tramite il portale o l'interfaccia della riga di comando di Azure.

> [!NOTE]
> Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Non è possibile tornare a una dimensione di archiviazione inferiore dopo l'incremento.

Quando si modifica il livello di calcolo o le dimensioni di calcolo, il server viene riavviato per rendere effettivo il nuovo tipo di server. Durante l'intervallo nel quale il sistema passa al nuovo server, non è possibile stabilire nuove connessioni e viene effettuato il rollback di tutte le transazioni di cui non è stato eseguito il commit. Questa finestra varia, ma nella maggior parte dei casi è compresa tra 60-120 secondi. 

La scalabilità dell'archiviazione e la modifica del periodo di conservazione dei backup sono operazioni online e non richiedono il riavvio del server.

## <a name="pricing"></a>Prezzi

Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/MySQL/). Per visualizzare il costo per la configurazione desiderata, il [portale di Azure](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) Mostra il costo mensile nella scheda **calcolo e archiviazione** in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Nel sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) selezionare **Aggiungi elementi**, espandere la categoria **database** , scegliere **database di Azure per MySQL**e **server flessibile** come tipo di distribuzione per personalizzare le opzioni.

Per ottimizzare i costi del server, è possibile prendere in considerazione i suggerimenti seguenti:

- Ridurre il livello di calcolo o le dimensioni di calcolo (VCore) se il calcolo è sottoutilizzato.
- Se il carico di lavoro non richiede la capacità di calcolo completa in modo continuo dai livelli di per utilizzo generico e con ottimizzazione per la memoria, provare a passare al livello di calcolo in sequenza.
- Arrestare il server quando non è in uso.
- Ridurre il periodo di conservazione del backup se non è necessaria una conservazione più lunga del backup.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [creare un server MySQL nel portale](quickstart-create-server-portal.md).
- Informazioni sulle [limitazioni del servizio](concepts-limitations.md).