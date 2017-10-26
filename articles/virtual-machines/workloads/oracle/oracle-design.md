---
title: Progettare e implementare un database Oracle in Azure | Microsoft Docs
description: Progettare e implementare un database Oracle nell'ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.openlocfilehash: c8f858bf249c4b56ad4fe60654ab489676eceb1f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Progettare e implementare un database Oracle in Azure

## <a name="assumptions"></a>Presupposti

- Si sta pianificando la migrazione di un database Oracle da locale ad Azure.
- Si conoscono le varie metriche dei report AWR di Oracle.
- Si ha una conoscenza di base delle prestazioni delle applicazioni e dell'utilizzo della piattaforma.

## <a name="goals"></a>Obiettivi

- Sapere come ottimizzare la distribuzione di Oracle in Azure.
- Esplorare le opzioni di ottimizzazione delle prestazioni per un database Oracle in un ambiente Azure.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Differenze tra un'implementazione locale e in Azure 

Di seguito sono descritti alcuni importanti elementi da tenere in considerazione quando si esegue la migrazione di applicazioni locali ad Azure. 

Una differenza importante è che in un'implementazione in Azure le risorse, ad esempio VM, dischi e reti virtuali, vengono condivise con gli altri client. Le risorse possono anche essere limitate in base ai requisiti. Invece di focalizzarsi sull'evitare gli errori (MTBF), Azure è più orientato alla sopravvivenza agli errori (MTTR).

La tabella seguente elenca alcune differenze tra un'implementazione locale e un'implementazione in Azure di un database Oracle.

> 
> |  | **Implementazione locale** | **Implementazione in Azure** |
> | --- | --- | --- |
> | **Rete** |LAN/WAN  |SDN (Software Defined Networking)|
> | **Gruppo di sicurezza** |Strumenti di restrizione per indirizzi IP/porte |[Gruppo di sicurezza di rete (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Resilienza** |MTBF (tempo medio tra gli errori) |MTTR (tempo medio per il ripristino)|
> | **Manutenzione pianificata** |Applicazione di patch/aggiornamenti|[Set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (applicazione di patch/aggiornamenti gestita da Azure) |
> | **Risorsa** |Dedicato  |Condivisa con altri client|
> | **Aree** |Data center |[Coppie di aree](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Archiviazione** |Dischi fisici/SAN |[Archiviazione gestita da Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Ridimensionare** |Scalabilità verticale |Scalabilità orizzontale|


### <a name="requirements"></a>Requisiti

- Determinare le dimensioni e il tasso di crescita del database.
- Determinare i requisiti IOPS, che è possibile stimare in base ai report AWR di Oracle o ad altri strumenti di monitoraggio della rete.

## <a name="configuration-options"></a>Opzioni di configurazione

Esistono quattro potenziali aree che è possibile ottimizzare per migliorare le prestazioni in un ambiente Azure:

- Dimensioni della macchina virtuale
- Velocità effettiva della rete
- Tipi di disco e configurazioni
- Impostazioni della cache su disco

### <a name="generate-an-awr-report"></a>Generare un report AWR

Se è già disponibile database Oracle di cui si sta pianificando la migrazione ad Azure, esistono diverse opzioni. È possibile eseguire il report AWR di Oracle per ottenere le metriche (operazioni di I/O al secondo, Mbps, GiB e così via). Scegliere quindi la VM in base alle metriche raccolte. In alternativa, è possibile contattare il team dell'infrastruttura per ottenere informazioni simili.

È possibile valutare se eseguire il report AWR durante i carichi di lavoro sia normali che di picco, per poter effettuare un confronto. Attraverso questi report, è possibile ridimensionare le macchine virtuali in base al carico di lavoro medio o al carico di lavoro massimo.

Di seguito è riportato un esempio di come generare un report AWR:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Metriche principali

Di seguito sono indicate le metriche che è possibile ottenere dal report AWR:

- Numero totale di core
- Velocità di clock della CPU
- Memoria totale in GB
- Uso della CPU
- Velocità di trasferimento dati di picco
- Frequenza delle modifiche di I/O (lettura/scrittura)
- Velocità del log di ripristino (Mbps)
- Velocità effettiva della rete
- Latenza di rete (bassa/alta)
- Dimensioni del database in GB
- Byte ricevuti tramite SQL*Net da e verso il client

### <a name="virtual-machine-size"></a>Dimensioni della macchina virtuale

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Stimare le dimensioni della macchina virtuale in base a utilizzo di CPU, memoria e I/O dal report AWR

Un elemento che è possibile osservare sono i primi cinque eventi in primo piano, che indicano dove sono presenti i colli di bottiglia del sistema.

Nel diagramma seguente, ad esempio, la sincronizzazione dei file di log è all'inizio. Indica il numero di attese necessarie prima che LGWR scriva il buffer del log nel file di log di ripristino. Questi risultati indicano che è necessario migliorare le prestazioni della risorsa di archiviazione o dei dischi. Il diagramma indica anche il numero di CPU (core) e la quantità di memoria.

![Screenshot della pagina del report AWR](./media/oracle-design/cpu_memory_info.png)

Il diagramma seguente mostra l'I/O totale di lettura e scrittura. Sono stati registrati 59 GB in lettura e 247,3 GB in scrittura durante il periodo di esecuzione del report.

![Screenshot della pagina del report AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Scegliere una VM

In base alle informazioni raccolte dal report AWR, il passaggio successivo è scegliere una macchina virtuale di dimensioni simili che soddisfi i requisiti. È possibile trovare un elenco delle VM disponibili nell'articolo [Ottimizzate per la memoria](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Ottimizzare il dimensionamento delle macchine virtuali con una serie di macchine virtuali simili basate sull'ACU

Dopo avere scelto la VM, prestare attenzione all'ACU per la VM. In base al valore ACU, è possibile scegliere una macchina virtuale diversa più adatta agli specifici requisiti. Per altre informazioni, vedere [Unità di calcolo di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Screenshot della pagina delle unità ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Velocità effettiva della rete

Come illustra il diagramma seguente, esiste una relazione tra velocità effettiva e IOPS:

![Screenshot della velocità effettiva](./media/oracle-design/throughput.png)

La velocità effettiva totale della rete viene stimata in base alle informazioni seguenti:
- Traffico SQL*Net
- Mbps x numero di server (flusso in uscita, ad esempio Oracle Data Guard)
- Altri fattori, ad esempio la replica dell'applicazione

![Screenshot della velocità effettiva SQL*Net](./media/oracle-design/sqlnet_info.png)

In base ai requisiti di larghezza di banda della rete, sono disponibili diversi tipi di gateway tra cui scegliere, ad esempio Basic, VpnGw e Azure ExpressRoute. Per altre informazioni, vedere la [pagina Prezzi di Gateway VPN](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h).

**Raccomandazioni**

- La latenza di rete è superiore rispetto a una distribuzione locale. La riduzione dei round trip di rete può migliorare notevolmente le prestazioni.
- Per ridurre i round trip, consolidare le applicazioni con transazioni elevate o con un livello di comunicazioni elevato nella stessa macchina virtuale.

### <a name="disk-types-and-configurations"></a>Tipi di disco e configurazioni

- *Dischi del sistema operativo predefiniti*: questi tipi di dischi offrono dati persistenti e memorizzazione nella cache. Sono ottimizzati per l'accesso del sistema operativo all'avvio e non sono progettati per i carichi di lavoro transazionali o di data warehouse (analitici).

- *Dischi non gestiti*: con questo tipi di dischi, si gestiscono gli account di archiviazione che archiviano i file di disco rigido virtuale (VHD) che corrispondono ai dischi delle macchine virtuali. I file VHD vengono archiviati come BLOB di pagine in account di archiviazione di Azure.

- *Dischi gestiti*: Azure gestisce gli account di archiviazione usati per i dischi delle macchine virtuali. Specificare il tipo di disco (Premium o Standard) e la dimensione del disco necessaria. Azure crea e gestisce il disco automaticamente.

- *Dischi di archiviazione Premium*: questi tipi di dischi sono ideali per i carichi di lavoro di produzione. Archiviazione Premium supporta i dischi di VM che possono essere collegati a VM di serie con dimensioni specifiche, ad esempio VM serie DS, DSv2, GS e F. Il disco Premium può essere di varie dimensioni ed è possibile scegliere tra dischi compresi tra 32 GB e 4.096 GB. Ciascuna dimensione di disco ha le proprie specifiche in termini di prestazioni. A seconda dei requisiti dell'applicazione è possibile collegare uno o più dischi alla VM.

Quando si crea un nuovo disco gestito dal portale, è possibile scegliere il **tipo di account** per il tipo di disco da usare. Tenere presente che non tutti i dischi disponibili sono visualizzati nel menu a discesa. Dopo avere scelto determinate dimensioni per la VM, il menu mostra solo gli SKU di Archiviazione Premium disponibili in base a tali dimensioni della VM.

![Screenshot della pagina del disco gestito](./media/oracle-design/premium_disk01.png)

Per altre informazioni, vedere [Archiviazione Premium a prestazioni elevate e dischi gestiti per le VM](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Dopo avere configurato la risorsa di archiviazione in una macchina virtuale, è possibile sottoporre i dischi a test di carico prima di creare un database. Conoscendo la velocità di I/O in termini di latenza e velocità effettiva, è possibile determinare se le macchine virtuali supportano la velocità effettiva prevista con gli obiettivi di latenza.

Sono disponibili numerosi strumenti per il test di carico delle applicazioni, ad esempio Oracle Orion, Sysbench e Fio.

Dopo avere distribuito un database Oracle, eseguire nuovamente il test di carico. Avviare i carico di lavoro normali e di picco. I risultati indicheranno la baseline dell'ambiente.

Può essere più importante ridimensionare l'archiviazione in base alla frequenza di operazioni di I/O al secondo invece che alle dimensioni di archiviazione. Se ad esempio le operazioni di I/O al secondo necessarie sono 5.000, ma servono solo 200 GB, si potrebbe comunque scegliere il disco Premium classe P30 anche se ha più di 200 GB di spazio di archiviazione.

Dal report AWR si può ottenere la frequenza di operazioni di I/O, che è determinata da log di rollforward, letture fisiche e frequenza delle scritture.

![Screenshot della pagina del report AWR](./media/oracle-design/awr_report.png)

Ad esempio: la dimensione di rollforward è 12.200.000 byte al secondo, equivalente a 11,63 Mbps.
Le operazioni di I/O al secondo corrispondono a 12.200.000/2.358 = 5.174.

Dopo avere ottenuto un quadro preciso dei requisiti di I/O, è possibile scegliere la combinazione delle unità più adatte per soddisfare tali requisiti.

**Raccomandazioni**

- Per lo spazio di tabella dei dati, ripartire il carico di lavoro di I/O tra diversi dischi usando l'archiviazione gestita o Oracle ASM.
- Con l'aumento della dimensione dei blocchi di I/O, per le operazioni intensive di lettura e di scrittura, aggiungere più dischi dati.
- Aumentare la dimensione dei blocchi per i processi sequenziali di grandi dimensioni.
- Usare la compressione dei dati per ridurre le operazioni di I/O (per i dati e gli indici).
- Separare i log di rollforward, di sistema e temporanei e annullare TS nei dischi dati separati.
- Non inserire alcun file dell'applicazione nei dischi del sistema operativo predefiniti (dev/sda). Questi dischi non sono ottimizzati per l'avvio rapido delle macchine virtuali e potrebbero non offrire prestazioni valide per l'applicazione.

### <a name="disk-cache-settings"></a>Impostazioni della cache su disco

Sono disponibili tre opzioni per la memorizzazione nella cache dell'host:

- *Sola lettura*: tutte le richieste sono memorizzate nella cache per le letture future. Tutte le scritture vengono rese persistenti direttamente nell'archivio BLOB di Azure.

- *Lettura/scrittura*: si tratta di un algoritmo "read-ahead". Le letture e le scritture sono memorizzate nella cache per le letture future. Le scritture non write-through sono rese persistenti prima nella cache locale. Per SQL Server, le scritture sono rese persistenti in Archiviazione di Microsoft Azure perché usa le scritture write-through. Offre anche la latenza del disco più bassa per i carichi di lavoro leggeri.

- *Nessuna* (disabilitata): usando questa opzione, è possibile ignorare la cache. Tutti i dati vengono trasferiti sul disco e resi persistenti in Archiviazione di Azure. Questo metodo offre la massima frequenza di I/O per i carichi di lavoro con un uso intensivo dell'I/O. È anche necessario considerare il costo delle transazioni.

**Raccomandazioni**

Per ottimizzare la velocità effettiva, è consigliabile iniziare con l'opzione **Nessuna** per la memorizzazione nella cache dell'host. Per Archiviazione Premium, tenere presente che è necessario disabilitare le "barriere" quando si esegue il montaggio del file system con le opzioni **Sola lettura** o **Nessuna**. Aggiornare il file /etc/fstab con l'UUID dei dischi.

Per altre informazioni, vedere [Archiviazione Premium per VM Linux](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Screenshot della pagina del disco gestito](./media/oracle-design/premium_disk02.png)

- Per i dischi del sistema operativo, usare la memorizzazione nella cache **Lettura/scrittura** predefinita.
- Per SYSTEM, TEMP e UNDO usare **Nessuna** per la memorizzazione nella cache.
- Per DATA usare **Nessuna** per la memorizzazione nella cache, ma se il database è di sola lettura o esegue un'intensa attività di lettura, usare la memorizzazione nella cache **Sola lettura**.

Dopo avere salvato l'impostazione del disco dati, non è possibile modificare l'impostazione della cache host, a meno che l'unità a livello di sistema operativo non venga smontata e quindi rimontata dopo avere apportato la modifica.


## <a name="security"></a>Sicurezza

Dopo avere installato e configurato l'ambiente Azure, il passaggio successivo consiste nel proteggere la rete. Di seguito sono elencati alcuni suggerimenti:

- *Criteri del gruppo di sicurezza di rete*: il gruppo di sicurezza di rete può essere definito da una subnet o una scheda di interfaccia di rete. È più semplice controllare l'accesso a livello di subnet per la sicurezza e forzare il routing per elementi come i firewall per le applicazioni.

- *Jumpbox*: per una maggiore sicurezza dell'accesso, gli amministratori non devono connettersi direttamente al servizio dell'applicazione o al database. Viene usato un jumpbox come elemento intermedio tra il computer dell'amministratore e le risorse di Azure.
![Screenshot della pagina della topologia jumpbox](./media/oracle-design/jumpbox.png)

    Il computer dell'amministratore deve offrire accesso con restrizioni IP al solo jumpbox. Il jumpbox deve avere accesso all'applicazione e al database.

- *Rete privata* (subnet): è consigliabile tenere il servizio dell'applicazione e il database in subnet separate, per garantire un maggiore controllo ai criteri del gruppo di sicurezza di rete.


## <a name="additional-reading"></a>Informazioni aggiuntive

- [Configurare Oracle ASM](configure-oracle-asm.md)
- [Configurare Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurare Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e ripristino di Oracle](oracle-backup-recovery.md)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)
- [Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)
