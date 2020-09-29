---
title: SAP HANA configurazione e della macchina virtuale di Azure | Microsoft Docs
description: Azure NetApp Files consigli sull'archiviazione per SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, e, HANA, Azure NetApp Files, snapshot
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d0df2199a2afdbea1ee1c6c543df324e9fe3cc3e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450730"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>Volumi NFS v 4.1 su Azure NetApp Files per SAP HANA

Azure NetApp Files fornisce condivisioni NFS native che possono essere usate per i volumi **/Hana/Shared**, **/Hana/data**e **/Hana/log** . L'uso di condivisioni NFS basate su e per i volumi **/Hana/data** e **/Hana/log** richiede l'utilizzo del protocollo NFS v 4.1. Il protocollo NFS v3 non è supportato per l'utilizzo di volumi **/Hana/data** e **/Hana/log** quando si basano le condivisioni su e. 


> [!IMPORTANT]
> Il protocollo NFS v3 implementato in Azure NetApp Files **non** è supportato per l'uso di **/Hana/data** e **/Hana/log**. L'utilizzo di NFS 4,1 è obbligatorio per i volumi **/Hana/data** e **/Hana/log** da un punto di vista funzionale. Mentre per il volume **/Hana/Shared** il protocollo NFS v3 o NFS v 4.1 può essere utilizzato da un punto di vista funzionale.

## <a name="important-considerations"></a>Considerazioni importanti

Quando si prende in considerazione Azure NetApp Files per SAP NetWeaver e SAP HANA, tenere presente le considerazioni importanti seguenti:

- Il pool di capacità minimo è 4 TiB  
- Le dimensioni minime del volume sono di 100 GiB.
- Azure NetApp Files e tutte le macchine virtuali, in cui vengono montati i volumi Azure NetApp Files, devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peering](../../../virtual-network/virtual-network-peering-overview.md) nella stessa area
- È importante che le macchine virtuali vengano distribuite in prossimità dell'archiviazione di Azure NetApp per una bassa latenza.  
- La rete virtuale selezionata deve avere una subnet, delegata a Azure NetApp Files
- Verificare che la latenza dal server di database al volume e sia misurata e inferiore a 1 millisecondo
- La velocità effettiva di un volume di Azure NetApp è una funzione della quota del volume e del livello di servizio, come documentato in [Livelli di servizio per Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Quando si ridimensionano i volumi di Azure NetApp di HANA, verificare che la velocità effettiva risultante soddisfi i requisiti di sistema HANA
- Provare a "consolidare" i volumi per ottenere prestazioni più elevate in un volume più grande, ad esempio, usare un volume per/sapmnt,/usr/sap/trans,... Se possibile  
- Azure NetApp Files offre [criteri di esportazione](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): è possibile controllare i client consentiti, il tipo di accesso, ad esempio lettura e scrittura, sola lettura e così via. 
- La funzionalità Azure NetApp Files non è ancora in grado di riconoscere la zona. Attualmente la funzionalità Azure NetApp Files non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni di latenza potenziali in alcune aree di Azure.   
- L'ID utente per <b>sid</b>adm e l'ID gruppo per `sapsys` nelle macchine virtuali devono corrispondere alla configurazione in Azure NetApp Files. 

> [!IMPORTANT]
> Per carichi di lavoro SAP HANA, è fondamentale una bassa latenza. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi Azure NetApp Files vengano distribuiti in prossimità.  

> [!IMPORTANT]
> Se è presente una mancata corrispondenza tra l'ID utente per <b>SID</b>ADM e l'ID del gruppo per `sapsys` tra la macchina virtuale e la configurazione di Azure NetApp, le autorizzazioni per i file in volumi NetApp di Azure, montate nella VM, verranno visualizzate come `nobody` . Assicurarsi di specificare l'ID utente corretto per <b>sid</b>adm e l'ID gruppo per `sapsys` quando [si acquisisce un nuovo sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) in Azure NetApp Files.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento per il database HANA in Azure NetApp Files

La velocità effettiva di un volume di Azure NetApp è una funzione delle dimensioni del volume e del livello di servizio, come documentato in [Livelli di servizio per Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Importante comprendere è la relazione tra le prestazioni e la dimensione e la presenza di limiti fisici per un LIF (interfaccia logica) di SVM (macchina virtuale di archiviazione).

La tabella seguente illustra che può essere utile creare un volume "standard" di grandi dimensioni per archiviare i backup e che non ha senso creare un volume "ultra" maggiore di 12 TB perché la capacità della larghezza di banda fisica di un singolo LIF verrebbe superata. 

La velocità effettiva massima per un LIF e una singola sessione Linux è compresa tra 1,2 e 1,4 GB/s. 

| Dimensione  | Velocità effettiva standard | Velocità effettiva Premium | Velocità effettiva ultra |
| --- | --- | --- | --- |
| 1 TB | 16 MB/sec | 64 MB/sec | 128 MB/sec |
| 2 TB | 32 MB/sec | 128 MB/sec | 256 MB/sec |
| 4 TB | 64 MB/sec | 256 MB/sec | 512 MB/sec |
| 10 TB | 160 MB/sec | 640 MB/sec | 1,280 MB/sec |
| 15 TB | 240 MB/sec | 960 MB/sec | 1,400 MB/sec |
| 20 TB | 320 MB/sec | 1,280 MB/sec | 1,400 MB/sec |
| 40 TB | 640 MB/sec | 1,400 MB/sec | 1,400 MB/sec |

È importante comprendere che i dati vengono scritti nelle stesse unità SSD nel back-end di archiviazione. La quota di prestazioni del pool di capacità è stata creata per poter gestire l'ambiente.
Gli indicatori KPI di archiviazione sono uguali per tutte le dimensioni del database HANA. In quasi tutti i casi, questo presupposto non riflette la realtà e l'aspettativa del cliente. Le dimensioni dei sistemi HANA non implicano necessariamente che un piccolo sistema richieda una velocità effettiva di archiviazione ridotta e che un sistema di grandi dimensioni richieda una velocità effettiva di archiviazione elevata. Tuttavia, in genere è possibile prevedere requisiti di velocità effettiva maggiori per le istanze di database HANA più grandi. In seguito alle regole di ridimensionamento di SAP per l'hardware sottostante, le istanze HANA più grandi forniscono anche più risorse della CPU e un parallelismo più elevato in attività come il caricamento dei dati dopo il riavvio di un'istanza. Di conseguenza, le dimensioni dei volumi devono essere adottate per le aspettative e i requisiti dei clienti. E non solo in base ai requisiti di capacità pura.

Quando si progetta l'infrastruttura per SAP in Azure, è necessario conoscere alcuni requisiti minimi di velocità effettiva di archiviazione (per i sistemi di produzione) da SAP, che si traduce in caratteristiche minime di velocità effettiva:

| Tipo di volume e tipo di I/O | Indicatore KPI minimo richiesto da SAP | Livello di servizio Premium | Livello di servizio ultra |
| --- | --- | --- | --- |
| Scrittura volume log | 250 MB/sec | 4 TB | 2 TB |
| Scrittura del volume di dati | 250 MB/sec | 4 TB | 2 TB |
| Lettura volume dati | 400 MB/sec | 6,3 TB | 3,2 TB |

Poiché tutti i tre indicatori KPI sono richiesti, il volume di **/Hana/data** deve essere dimensionato in base alla capacità più elevata per soddisfare i requisiti minimi di lettura.

Per i sistemi HANA, che non richiedono una larghezza di banda elevata, le dimensioni del volume e possono essere inferiori. E nel caso in cui un sistema HANA richieda una maggiore velocità effettiva, il volume può essere adattato ridimensionando la capacità online. Nessun KPI definito per i volumi di backup. Tuttavia, la velocità effettiva del volume di backup è essenziale per un ambiente con prestazioni ottimali. Log: le prestazioni del volume dei dati devono essere progettate per le aspettative dei clienti.

> [!IMPORTANT]
> Indipendentemente dalla capacità che si distribuisce in un singolo volume NFS, la velocità effettiva è prevista per un livello di larghezza di banda compreso tra 1,2 e 1,4 GB/sec, usata da un consumer in una macchina virtuale. Questo dipende dall'architettura sottostante dell'offerta di Azure NetApp Files e dai limiti della sessione Linux correlata in base a NFS. I numeri relativi a prestazioni e velocità effettiva, come documentato nell'articolo [Risultati dei test di benchmark delle prestazioni per Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) sono stati eseguiti su un volume NFS condiviso con più macchine virtuali client e, di conseguenza, più sessioni. Questo scenario è diverso dallo scenario misurato in SAP dove misuriamo la velocità effettiva da una singola VM a un volume NFS Ospitata in e.

Per soddisfare i requisiti di velocità effettiva minima SAP per dati e log e in base alle linee guida per **/Hana/Shared**, le dimensioni consigliate sono le seguenti:

| Volume | Dimensione<br /> Livello Archiviazione Premium | Dimensione<br /> Livello Archiviazione Ultra | Protocollo NFS supportato |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v4.1 |
| scalabilità verticale/Hana/Shared | Min (1 TB, 1 x RAM)  | Min (1 TB, 1 x RAM) | v3 o v4.1 |
| scalabilità orizzontale di/Hana/Shared | 1 x RAM del nodo del ruolo di lavoro<br /> per 4 nodi del ruolo di lavoro  | 1 x RAM del nodo del ruolo di lavoro<br /> per 4 nodi del ruolo di lavoro  | v3 o v4.1 |
| /Hana/Logbackup | 3 x RAM  | 3 x RAM | v3 o v4.1 |
| /Hana/backup | 2 x RAM  | 2 x RAM | v3 o v4.1 |

Per tutti i volumi, è consigliabile usare NFS v 4.1

Le dimensioni dei volumi di backup sono stime. I requisiti esatti devono essere definiti in base al carico di lavoro e ai processi operativi. Per i backup, è possibile consolidare molti volumi per diverse istanze di SAP HANA a uno o due volumi più grandi, che potrebbero avere un livello di servizio inferiore di e.

> [!NOTE]
> Il Azure NetApp Files, le indicazioni relative al dimensionamento indicate in questo documento sono destinate ai requisiti minimi che SAP esprime ai propri provider di infrastruttura. Nelle distribuzioni reali dei clienti e negli scenari di carico di lavoro, questo potrebbe non essere sufficiente. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro specifico.  

È pertanto possibile prendere in considerazione la distribuzione di una velocità effettiva simile per i volumi Azure NetApp Files, come elencato già per l'archiviazione su disco Ultra. Prendere in considerazione anche le dimensioni elencate per i volumi per i diversi SKU di VM, come già fatto nelle tabelle del disco Ultra.

> [!TIP]
> È possibile ridimensionare i volumi Azure NetApp Files in modo dinamico, senza dover eseguire l'operazione `unmount` per i volumi, arrestare le macchine virtuali o SAP HANA. Questo consente la flessibilità di soddisfare le esigenze di velocità effettiva previste e non previste per le applicazioni.

La documentazione su come distribuire una configurazione con scalabilità orizzontale SAP HANA con un nodo standby usando i volumi NFS v4.1 ospitati in Azure NetApp Files è pubblicata in [Distribuire un sistema di SAP HANA con scalabilità orizzontale con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files su SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="availability"></a>Disponibilità
Gli aggiornamenti e gli aggiornamenti del sistema e vengono applicati senza alcun impatto sull'ambiente del cliente. Il contratto di contratto definito è pari al [99,99%](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Volumi e indirizzi IP e pool di capacità
Con e, è importante comprendere il modo in cui viene compilata l'infrastruttura sottostante. Un pool di capacità è solo una struttura, semplificando la creazione di un modello di fatturazione per e. Un pool di capacità non ha alcuna relazione fisica con l'infrastruttura sottostante. Se si crea un pool di capacità viene creata solo una shell, che può essere addebitata, non più. Quando si crea un volume, il primo SVM (macchina virtuale di archiviazione) viene creato in un cluster di diversi sistemi NetApp. Per accedere al volume viene creato un singolo IP per questo SVM. Se si creano più volumi, tutti i volumi vengono distribuiti in questa SVM su questo cluster NetApp Multicontroller. Anche se si ottiene un solo indirizzo IP, i dati vengono distribuiti su più controller. E dispone di una logica che distribuisce automaticamente i carichi di lavoro dei clienti quando i volumi e/o la capacità dell'archiviazione configurata raggiungono un livello interno predefinito. È possibile notare questi casi perché per accedere ai volumi viene assegnato un nuovo indirizzo IP.

##<a name="log-volume-and-log-backup-volume"></a>Volume di log e volume di backup del log
Il "volume di log" (**/Hana/log**) viene utilizzato per scrivere il log di ripristino online. In questo volume sono presenti file aperti che non hanno senso per lo snapshot del volume. I file di log di rollforward online vengono archiviati o sottoposti a backup nel volume di backup del log quando il file di log di ripristino online è pieno o viene eseguito un backup del log di rollforward. Per garantire prestazioni di backup ragionevoli, il volume di backup del log richiede una buona velocità effettiva. Per ottimizzare i costi di archiviazione, può essere utile consolidare log-backup-volume di più istanze HANA. Per consentire a più istanze di HANA di sfruttare lo stesso volume e scrivere i backup in directory diverse. Utilizzando un consolidamento di questo tipo, è possibile ottenere una maggiore velocità effettiva con perché è necessario aumentare la velocità del volume. 

Lo stesso vale per il volume utilizzato per scrivere backup completo del database HANA in.  
 

## <a name="backup"></a>Backup
Oltre ai backup di streaming e al servizio di backup di Azure che eseguono il backup SAP HANA database, come descritto nell'articolo [relativo alla guida di backup per SAP Hana in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide), Azure NetApp Files consente di eseguire backup di snapshot basati sull'archiviazione. 

SAP HANA supporta:

- Backup di snapshot basati su archiviazione da SAP HANA 1,0 SPS7 in
- Supporto del backup di snapshot basato su archiviazione per ambienti HANA (Multidatabase container) HANA da SAP HANA 2,0 SPS4 in


La creazione di backup di snapshot basati sull'archiviazione è una semplice procedura in quattro passaggi, 
1. Creazione di uno snapshot del database HANA (interno): un'attività che è necessario eseguire 
1. SAP HANA scrive i dati nei file di dati per creare uno stato coerente nello spazio di archiviazione: HANA esegue questo passaggio in seguito alla creazione di uno snapshot HANA
1. Creare uno snapshot nel volume **/Hana/data** nell'archiviazione: un passaggio che è necessario eseguire. Non è necessario eseguire uno snapshot nel volume **/Hana/log**
1. Eliminare lo snapshot del database HANA (interno) e riprendere il normale funzionamento: un passaggio che gli strumenti devono eseguire

> [!WARNING]
> L'ultimo passaggio o la mancata esecuzione dell'ultimo passaggio ha un effetto grave sulla richiesta di memoria SAP HANA e può causare un arresto del SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![Backup di snapshot e per SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![E snapshot backup per SAP HANA part2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Questa procedura di backup dello snapshot può essere gestita in diversi modi, usando vari strumenti. Un esempio è lo script Python "ntaphana_azure. py" disponibile in GitHub. si tratta di [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) codice di esempio, fornito "così com'è" senza alcuna manutenzione o supporto.



> [!CAUTION]
> Uno snapshot non è un backup protetto perché si trova nello stesso spazio di archiviazione fisico del volume di cui si è appena fatto uno snapshot. È obbligatorio "proteggere" almeno uno snapshot al giorno in una posizione diversa. Questa operazione può essere eseguita nello stesso ambiente, in un'area di Azure remota o nell'archiviazione BLOB di Azure.


Per gli utenti dei prodotti di backup CommVault, una seconda opzione è CommVault IntelliSnap V. 11.21 e versioni successive. Questa o versioni successive di CommVault offre supporto Azure NetApp Files. L'articolo [CommVault IntelliSnap 11,21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) fornisce ulteriori informazioni.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Eseguire il backup dello snapshot usando l'archiviazione BLOB di Azure
Il backup nell'archiviazione BLOB di Azure è un metodo conveniente e rapido per salvare i backup degli snapshot di archiviazione del database HANA basati su e. Per salvare gli snapshot nell'archiviazione BLOB di Azure, è preferibile lo strumento azcopy. Scaricare la versione più recente di questo strumento e installarla, ad esempio, nella directory bin in cui è installato lo script Python da GitHub.
Scaricare la versione più recente dello strumento azcopy:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

La funzionalità più avanzata è l'opzione SYNC. Se si usa l'opzione di sincronizzazione, azcopy mantiene sincronizzate la directory di origine e quella di destinazione. L'utilizzo del parametro **--Delete-Destination** è importante. Senza questo parametro, azcopy non elimina i file nel sito di destinazione e l'utilizzo dello spazio sul lato di destinazione aumenterà. Creare un contenitore BLOB in blocchi nell'account di archiviazione di Azure. Creare quindi la chiave SAS per il contenitore BLOB e sincronizzare la cartella snapshot con il contenitore BLOB di Azure.

Ad esempio, se uno snapshot giornaliero deve essere sincronizzato con il contenitore BLOB di Azure per proteggere i dati. Il comando seguente può essere usato solo per uno snapshot.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Passaggi successivi
Leggi l'articolo:

- [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)