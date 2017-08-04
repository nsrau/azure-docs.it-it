---
title: Progettare e implementare il database Oracle in Azure | Microsoft Docs
description: Progettare e implementare il database Oracle nell'ambiente Azure.
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e13e61a2d055ce4f9777cea8bf6199f2acf9e7bf
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---


# <a name="design-and-implement-of-oracle-database-on-azure"></a>Progettare e implementare il database Oracle in Azure

## <a name="assumptions"></a>Presupposti

- Pianificazione della migrazione del database Oracle da locale ad Azure
- Comprensione delle varie metriche dei report AWR di Oracle
- Disponibilità di una linea di base per le prestazioni delle applicazioni e l'uso della piattaforma

## <a name="goals"></a>Obiettivi

- Ottenere informazioni per ottimizzare la distribuzione di Oracle in Azure
- Esplorare le opzioni di ottimizzazione delle prestazioni per il database Oracle nell'ambiente Azure

### <a name="on-premises-vs-on-azure"></a>Confronto tra configurazione locale e Azure

Di seguito sono riportate alcune considerazioni importanti per la migrazione di applicazioni locali in Azure. A differenza delle configurazioni locali, le risorse in Azure (macchine virtuali, dischi, rete virtuale e così via) sono condivise tra altri client. Inoltre, le risorse possono essere limitate in base ai requisiti. Invece di focalizzarsi sull'evitare gli errori (MTBF), Azure è più orientato alla sopravvivenza agli errori (MTTR).

Nella tabella seguente sono elencate alcune delle differenze.

> 
> |  | **Locale** | **Azure** |
> | --- | --- | --- |
> | **Rete** |LAN/WAN  |SDN (Software Defined Networking)|
> | **Gruppo di sicurezza** |Strumenti di restrizione per indirizzi IP/porte |[Gruppo di sicurezza di rete (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Resilienza** |MTBF (tempo medio tra gli errori) |MTTR (tempo medio per il ripristino)|
> | **Manutenzione pianificata** |Applicazione di patch/aggiornamenti|[Set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (applicazione di patch/aggiornamenti gestita da Azure) |
> | **Risorsa** |Dedicato  |Condivisa con altri client|
> | **Aree** |Data center |[Coppie di aree](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Archiviazione** |Dischi fisici/SAN |[Archiviazione gestita da Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Ridimensionare** |scalabilità verticale |scalabilità orizzontale|


### <a name="requirements"></a>Requisiti

- Determinare le dimensioni e il tasso di crescita del database
- Determinare i requisiti IOPS, attraverso stime basate sul report AWR di Oracle o su altri strumenti di monitoraggio di rete

## <a name="configuration-options"></a>Opzioni di configurazione

Esistono quattro potenziali aree che è possibile ottimizzare per migliorare le prestazioni nell'ambiente Azure.

- Dimensioni della macchina virtuale
- Velocità effettiva della rete
- Tipi di disco e configurazioni
- Impostazioni della cache su disco

### <a name="generate-awr-report"></a>Generare il report AWR

Se si dispone di un database Oracle esistente e si prevede di eseguire la migrazione ad Azure. È possibile eseguire il report AWR di Oracle per ottenere le metriche (IOPS, Mbps, GiB e così via) e quindi scegliere la macchina virtuale in base alle metriche raccolte. In alternativa, è possibile contattare il team dell'infrastruttura per ottenere informazioni simili.

È possibile valutare se eseguire il report AWR durante i periodi con carichi di lavoro normali e di picco, in modo da confrontare la differenza. Attraverso questi report, è possibile ridimensionare le macchine virtuali in base al carico di lavoro medio o ai carichi di lavoro massimi.

Di seguito è riportato un esempio di come generare un report AWR.

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Metriche principali

Di seguito sono riportate le metriche che è possibile ottenere dal report AWR.

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
- Byte ricevuti tramite SQL*Net dal e verso il client

### <a name="virtual-machine-size"></a>Dimensioni della macchina virtuale

#### <a name="1-initial-estimate-of-vm-size-is-based-on-cpumemoryio-usage-from-the-awr-report"></a>1. La stima iniziale delle dimensioni della macchina virtuale si basa sull'uso di CPU, memoria e I/O dal report AWR.

Un elemento che è possibile osservare sono i primi cinque eventi in primo piano, che indicano dove sono presenti i colli di bottiglia del sistema.

Ad esempio, nel diagramma seguente, la sincronizzazione del file di log è in alto e indica il numero di attese di LGWR per la scrittura del buffer del log nel file di log di ripristino. Questo indica che sono necessari dischi/archiviazione con prestazioni superiori. Nel diagramma sono indicati anche il numero di CPU (core) e la memoria.

![Screenshot della pagina del report AWR](./media/oracle-design/cpu_memory_info.png)

Il diagramma seguente mostra l'I/O totale di lettura e scrittura. Sono stati registrati 59 GB in lettura e 247,3 GB in scrittura durante il periodo di esecuzione del report.

![Screenshot della pagina del report AWR](./media/oracle-design/io_info.png)

#### <a name="2-estimate-the-vm-size"></a>2. Stima delle dimensioni della macchina virtuale

In base alle informazioni raccolte dal report AWR, il passaggio successivo è scegliere una macchina virtuale con dimensioni simili che soddisfi i requisiti. È possibile trovare un elenco delle macchine virtuali disponibili in [Dimensioni delle macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes-memory).

#### <a name="3-fine-tune-the-vm-sizing-with-similar-vm-series-based-on-the-acu"></a>3. Ottimizzare il dimensionamento delle macchine virtuali con una serie di macchine virtuali simili basate sull'ACU

Dopo aver scelto le macchine virtuali, prestare attenzione all'ACU per le macchine virtuali. In base al valore ACU, è possibile scegliere una macchina virtuale simile che potrebbe essere più adatta agli specifici requisiti. Per altre informazioni, vedere la descrizione delle [unità di calcolo di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Screenshot della pagina delle unità ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Velocità effettiva della rete

Come illustrato di seguito, esiste una relazione tra la velocità effettiva e IOPS.

![Schermata della velocità effettiva](./media/oracle-design/throughput.png)

La velocità effettiva totale della rete viene stimata come segue:
- Traffico SQL*Net
- Mbps x numero di server (flusso in uscita, ad esempio Data Guard)
- Altri, ad esempio la replica dell'applicazione

![Screenshot della velocità effettiva SQL*Net](./media/oracle-design/sqlnet_info.png)

In base ai requisiti di larghezza di banda della rete, sono disponibili diversi tipi di gateway tra cui scegliere, ad esempio Basic, VpnGw ed ExpressRoute. Altre informazioni sono disponibili nella [pagina dei prezzi dei gateway VPN](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h)

Consigli

- La latenza di rete è superiore rispetto a una distribuzione locale. La riduzione dei round trip di rete può migliorare notevolmente le prestazioni.
- Consolidare le applicazioni con transazioni elevate o con un livello di comunicazioni elevato nella stessa macchina virtuale per ridurre i round trip.

### <a name="disk-types-and-configurations"></a>Tipi di disco e configurazioni

- Disco del sistema operativo predefinito: con i dati persistenti e la memorizzazione nella cache. Questo disco è ottimizzato per l'accesso del sistema operativo in fase di avvio, mentre non è progettato per i carichi di lavoro transazionali o di data warehouse (analitici)

- Dischi non gestiti: si gestiscono gli account di archiviazione usati per archiviare i file di disco rigido virtuale (VHD) che corrispondono ai dischi delle macchine virtuali. I file VHD vengono archiviati come BLOB di pagine in account di archiviazione di Azure.

- Dischi gestiti: Azure gestisce gli account di archiviazione usati per i dischi delle macchine virtuali. Specificare il tipo di disco (Premium o Standard) e la dimensione del disco necessaria. Azure crea e gestisce il disco automaticamente.

- Dischi di archiviazione Premium (particolarmente indicati per i carichi di lavoro di produzione): Archiviazione Premium di Azure supporta dischi di macchine virtuali che possono essere collegati a macchine virtuali di serie e dimensioni specifiche, ad esempio le serie DS, DSv2, GS e F. Il disco Premium può essere di varie dimensioni. È possibile scegliere tra diverse dimensioni del disco, da 32 GB a 4096 GB. Ciascuna dimensione di disco ha le proprie specifiche in termini di prestazioni. A seconda dei requisiti dell'applicazione è possibile collegare uno o più dischi alla VM.

Quando si crea un nuovo disco gestito dal portale, è possibile scegliere il tipo di account per specificare il tipo di disco da usare. È importante tenere presente che non tutti i dischi disponibili sono visualizzati nella casella di riepilogo a discesa. Ciò è dovuto al fatto che Archiviazione di Azure è integrato con le macchine virtuali e vengono applicati limiti a livello di SKU (ad esempio, numero massimo di unità, numero massimo di operazioni di I/O al secondo). Pertanto, dopo aver selezionato una determinata dimensione della macchina virtuale, vengono visualizzate solo le SKU di Archiviazione Premium disponibili basate sulla dimensione di tale macchina virtuale.

![Screenshot della pagina del disco gestito](./media/oracle-design/premium_disk01.png)

Per altre informazioni, vedere la pagina relativa ad [Archiviazione Premium di Azure](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Dopo avere configurato l'archiviazione in una macchina virtuale, è possibile sottoporre i dischi a test di carico prima di creare un database. Conoscendo la velocità di I/O in termini di latenza e velocità effettiva, è possibile determinare se le macchine virtuali supportano la velocità effettiva prevista con gli obiettivi di latenza.

Sono disponibili numerosi strumenti per i test di carico delle applicazioni, ad esempio Oracle Orion, Sysbench, Fio e così via.

Eseguire nuovamente il test di carico dopo aver distribuito il database Oracle, quindi avviare i carichi di lavoro normali e di picco. I risultati indicheranno la linea di base dell'ambiente.

Può essere più importante dimensionare l'archiviazione in base alla frequenza di IOPS anziché alle dimensioni di archiviazione. Se ad esempio sono richieste 5000 operazioni di I/O al secondo ma sono necessari solo 200 GB, è comunque possibile usare il disco Premium di classe P30, anche se viene fornito con dimensioni di archiviazione superiori a 200 GB.

La frequenza di IOPS può essere ottenuta dal report AWR. È determinata da log di ripristino, letture fisiche e percentuale di scritture.

![Screenshot della pagina del report AWR](./media/oracle-design/awr_report.png)

Ad esempio: la dimensione di ripristino è 12200000 byte al secondo, equivalente a 11,63 Mbps. Il numero di IOPS è 12200000 / 2358 = 5174.

Dopo aver ottenuto un quadro preciso dei requisiti di I/O, è possibile scegliere la combinazione di unità più adatta per soddisfare tali requisiti.

Consigli

- Per lo spazio di tabella dei dati, ripartire il carico di lavoro di I/O tra diversi dischi usando l'archiviazione gestita o Oracle ASM.
- Con l'aumento della dimensione dei blocchi di I/O, per le operazioni intensive di lettura e scrittura, aggiungere più dischi dati.
- Aumentare la dimensione dei blocchi per i processi sequenziali di grandi dimensioni.
- Usare la compressione dei dati per ridurre le operazioni di I/O (per i dati e gli indici).
- Separare i log di ripristino, di sistema e temporanei e annullare TS nei dischi dati separati.
- È consigliabile non inserire alcun file dell'applicazione nel disco del sistema operativo predefinito (dev/sda). Questo disco è ottimizzato per l'avvio rapido delle macchine virtuali e potrebbe non fornire buone prestazioni per l'applicazione.

### <a name="disk-cache-settings"></a>Impostazioni della cache su disco

Sono disponibili tre opzioni per la memorizzazione nella cache dell'host.

- Read Only (Sola lettura): tutte le richieste sono memorizzate nella cache per le letture future. Tutte le scritture sono rese persistenti direttamente nei BLOB del servizio di archiviazione di Azure.

- Read Write (Lettura/scrittura): si tratta di un algoritmo "read-ahead". Le letture e le scritture sono memorizzate nella cache per le letture future. Le scritture non write-through sono rese persistenti prima nella cache locale. Per SQL Server, le scritture vengono rese persistenti in Archiviazione di Azure perché usa write-through. Fornisce la latenza del disco più bassa per i carichi di lavoro leggeri.

- None (Nessuna): consente di ignorare la cache. Tutti i dati vengono trasferiti sul disco e resi persistenti nel servizio Archiviazione di Azure. Questo metodo offre la massima frequenza di I/O per i carichi di lavoro con un uso intensivo dell'I/O. È inoltre necessario considerare il costo delle transazioni.

Consigli

Per ottimizzare la velocità effettiva, è consigliabile iniziare con l'opzione None (Nessuna) per la memorizzazione nella cache dell'host. Per Archiviazione Premium, è importante notare che è necessario disabilitare le "barriere" quando si esegue il montaggio del file system con le opzioni Read Only (Sola lettura) o None (Nessuna). Aggiornare il file /etc/fstab con l'UUID dei dischi.

Per altre informazioni, vedere [Archiviazione Premium per VM Linux](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Screenshot della pagina del disco gestito](./media/oracle-design/premium_disk02.png)

- Per i dischi del sistema operativo, usare la memorizzazione nella cache Read Write (Lettura/scrittura) predefinita
- Per SYSTEM, TEMP e UNDO usare None (Nessuna) per la memorizzazione nella cache
- Per DATA usare None (Nessuna) per la memorizzazione nella cache, ma se il database è di sola lettura o esegue un'intensa attività di lettura, usare la memorizzazione nella cache Read Only (Sola lettura)

Dopo aver salvato l'impostazione del disco dati, non è possibile modificare l'impostazione della cache host (AFAIK), a meno che l'unità a livello di sistema operativo non venga smontata e quindi rimontata dopo la modifica.


## <a name="security"></a>Sicurezza

Dopo avere installato e configurato l'ambiente Azure, il passaggio successivo consiste nella protezione della rete. Di seguito sono elencati alcuni suggerimenti:

- Criteri NSG

NSG può essere definito per una subnet o una scheda di interfaccia di rete.  È più semplice controllare l'accesso a livello di subnet per la sicurezza e forzare il routing per elementi come il firewall per le applicazioni.

- Jumpbox ![Screenshot della pagina della topologia Jumpbox](./media/oracle-design/jumpbox.png)

Per una maggiore sicurezza dell'accesso, gli amministratori non devono connettersi direttamente al servizio dell'applicazione o al database. Viene usato un jumpbox come elemento intermedio tra il computer dell'amministratore e le risorse di Azure.

L'accesso IP del computer dell'amministratore deve essere limitato al solo jumpbox. Il jumpbox potrà quindi accedere all'applicazione o al database.

- Rete privata (subnet)

È consigliabile configurare il servizio dell'applicazione e il database in subnet distinte, in modo da rendere più efficiente il controllo impostato dai criteri NSG.


## <a name="additional-readings"></a>Letture aggiuntive:

- [Configurare Oracle ASM](configure-oracle-asm.md)
- [Configurare Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurare Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e ripristino di Oracle](oracle-backup-recovery.md)

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)

