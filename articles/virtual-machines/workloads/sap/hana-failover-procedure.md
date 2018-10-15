---
title: Procedura di failover di HANA al sito di emergenza per SAP HANA di Azure (Istanze large) | Microsoft Docs
description: Come eseguire il failover al sito di ripristino di emergenza per SAP HANA in Azure (Istanze large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48bea302f2f8e878275e6a7c222d703e0f31f239
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492834"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura di failover di ripristino di emergenza


>[!IMPORTANT]
>Questa documentazione non sostituisce la documentazione relativa all'amministrazione di SAP HANA o le note su SAP. Si presuppone che il lettore abbia una solida comprensione ed esperienza in termini di amministrazione e operazioni di SAP HANA, in particolare per quanto riguarda gli argomenti di backup, ripristino, disponibilità elevata e ripristino di emergenza. In questa documentazione, vengono mostrate schermate da SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

Esistono due casi diversi da prendere in considerazione per il failover nel sito di ripristino di emergenza:

- È necessario riportare il database SAP HANA allo stato più recente dei dati. In questo caso, è disponibile uno script self-service che permette di eseguire il failover senza contattare Microsoft. Tuttavia, per il failback sarà necessario operare insieme a Microsoft.
- Si vuole ripristinare uno snapshot di archiviazione che non è lo snapshot più recente replicato. In questo caso, è necessario operare insieme a Microsoft. 

>[!NOTE]
>È necessario eseguire i passaggi seguenti nell'unità HANA in istanze Large, che rappresenta l'unità di ripristino di emergenza. 
 
Per ripristinare gli snapshot di archiviazione replicata più recenti, completare i passaggi seguenti: 

1. Arrestare l'istanza non di produzione di HANA nell'unità di ripristino di emergenza di HANA in istanze Large in esecuzione. Il motivo è la presenza di un'istanza di produzione HANA inattiva preinstallata.
1. Assicurarsi che nessun processo SAP HANA sia più in esecuzione. A questo scopo, usare il comando `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. L'output dovrebbe mostrare il processo **hdbdaemon** con stato arrestato e nessun altro processo HANA con stato in esecuzione o avviato.
1. Nell'unità HANA in istanze Large del sito di ripristino di emergenza eseguire lo script *azure_hana_dr_failover.pl*. Lo script richiede che venga ripristinato un tipo di SID SAP HANA. Quando viene richiesto, digitare uno o l'unico SID SAP HANA che è stato replicato e che viene mantenuto nel file *HANABackupCustomerDetails.txt* nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 

      Se si vuole eseguire il failover di più istanze di SAP HANA, è necessario eseguire lo script diverse volte. Quando viene richiesto, digitare il SID SAP HANA di cui si vuole eseguire il failover e il ripristino. Al termine, lo script mostra un elenco di punti di montaggio dei volumi che vengono aggiunti all'unità HANA in istanze Large. Questo elenco include anche i volumi di ripristino di emergenza ripristinati.

1. Montare i volumi di ripristino di emergenza ripristinati usando i comandi del sistema operativo Linux nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 
1. Avviare l'istanza di produzione di SAP HANA inattiva.
1. Se si sceglie di copiare i log di backup del log delle transazioni per ridurre il valore RPO, è necessario unire questi backup del log delle transazioni nella nuova directory /hana/logbackups di ripristino di emergenza appena montata. Non sovrascrivere i backup esistenti, ma copiare quelli più recenti che non sono stati replicati con l'ultima replica di uno snapshot di archiviazione.
1. È anche possibile ripristinare singoli file degli snapshot replicati nel volume /hana/shared/PRD nell'area di Azure di ripristino di emergenza. 

È possibile eseguire anche il test del failover del ripristino di emergenza senza conseguenze per la relazione di replica effettiva. Per eseguire un failover di test, seguire i passaggi 1 e 2 precedenti e quindi continuare con il passaggio 3 seguente.

>[!IMPORTANT]
>*Non* eseguire transazioni di produzione nell'istanza creata nel sito di ripristino di emergenza tramite il processo di **test di un failover** con lo script presentato nel passaggio 3. Il comando crea un set di volumi che non hanno relazione con il sito primario. Di conseguenza, *non* sarà possibile eseguire una nuova sincronizzazione con il sito primario. 

Passaggio 3 per il test del failover:

Nell'unità HANA in istanze Large del sito di ripristino di emergenza eseguire lo script **azure_hana_test_dr_failover.pl**. Questo script *non* interrompe la relazione di replica tra il sito primario e il sito di ripristino di emergenza. Al contrario, questo script clona i volumi di archiviazione di ripristino di emergenza. Dopo che il processo di clonazione ha esito positivo, i volumi clonati vengono ripristinati allo stato dello snapshot più recente e quindi montati nell'unità di ripristino di emergenza. Lo script richiede che venga ripristinato un tipo di SID SAP HANA. Digitare uno o l'unico SID SAP HANA che è stato replicato e che viene mantenuto nel file *HANABackupCustomerDetails.txt* nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 

Se si vuole testare più istanze di SAP HANA, è necessario eseguire lo script più volte. Quando viene richiesto, digitare il SID SAP HANA dell'istanza da testare per il failover. Al termine, lo script mostra un elenco di punti di montaggio dei volumi che vengono aggiunti all'unità HANA in istanze Large. Questo elenco comprende anche i volumi di ripristino di emergenza clonati.

Continuare con il passaggio 4.

   >[!NOTE]
   >Se è necessario eseguire il failover nel sito di ripristino di emergenza per salvare alcuni dati che sono stati eliminati ore prima e di conseguenza i volumi di ripristino di emergenza devono essere impostati su uno snapshot più recente, è necessario usare questa procedura. 

1. Arrestare l'istanza non di produzione di HANA nell'unità di ripristino di emergenza di HANA in istanze Large in esecuzione. Il motivo è la presenza di un'istanza di produzione HANA inattiva preinstallata.
1. Assicurarsi che nessun processo SAP HANA sia più in esecuzione. A questo scopo, usare il comando `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. L'output dovrebbe mostrare il processo **hdbdaemon** in uno stato arrestato e nessun altro processo HANA in esecuzione o con stato avviato.
1. Determinare con quale nome di snapshot o ID backup di SAP HANA si vuole ripristinare il sito di ripristino di emergenza. In casi di ripristino di emergenza reali, questo snapshot è in genere il più recente. Nel caso in cui sia necessario recuperare dati persi, selezionare uno snapshot precedente.
1. Contattare il supporto di Azure tramite una richiesta di supporto ad alta priorità. Richiedere il ripristino dello snapshot (con il nome e la data dello snapshot) o l'ID backup di HANA nel sito di ripristino di emergenza. Il valore predefinito prevede che il lato operazioni ripristini solo il volume /hana/data. Se si vuole ripristinare anche i volumi hana/logbackups, è necessario indicarlo in modo specifico. *Non ripristinare il volume /hana/shared.* È preferibile invece scegliere file specifici, ad esempio global.ini, dalla directory **.snapshot** e dalle relative sottodirectory dopo aver rimontato il volume /hana/shared per PRD. 

   Sul lato operazioni si verificano i passaggi seguenti:

   a. Viene arrestata la replica degli snapshot dal volume di produzione ai volumi di ripristino di emergenza. È possibile che questa interruzione si sia già verificata se un'interruzione di corrente del sito di produzione è il motivo per cui è stato necessario eseguire la procedura di ripristino di emergenza.
   
   b. Il nome dello snapshot di archiviazione o con l'ID di backup selezionato viene ripristinato nei volumi di ripristino di emergenza.
   
   c. Dopo il ripristino, i volumi di ripristino di emergenza sono disponibili per essere montati nelle unità HANA in istanze Large nell'area di ripristino di emergenza.
      
1. Montare i volumi di ripristino di emergenza nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 
1. Avviare l'istanza di produzione di SAP HANA inattiva.
1. Se si sceglie di copiare i log di backup del log delle transazioni per ridurre il valore RPO, è necessario unire questi backup del log delle transazioni nella nuova directory /hana/logbackups di ripristino di emergenza appena montata. Non sovrascrivere i backup esistenti, ma copiare quelli più recenti che non sono stati replicati con l'ultima replica di uno snapshot di archiviazione.
1. È anche possibile ripristinare singoli file degli snapshot replicati nel volume /hana/shared/PRD nell'area di Azure di ripristino di emergenza.

La sequenza di passaggi successiva prevede il ripristino dell'istanza di produzione di SAP HANA in base allo snapshot di archiviazione ripristinato e ai backup del log delle transazioni disponibili:

1. Modificare il percorso di backup in **/hana/logbackups** con SAP HANA Studio.
   ![Modificare il percorso di backup per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA analizza i percorsi dei file di backup e suggerisce il backup del log delle transazioni più recente per il ripristino. L'analisi può richiedere alcuni minuti, fino a quando non viene visualizzata una schermata simile alla seguente: ![Elenco dei backup del log delle transazioni per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modificare alcune delle impostazioni predefinite:

      - Deselezionare **Use Delta Backups** (Usa backup differenziali).
      - Selezionare **Initialize Log Area** (Inizializza area log).

   ![Impostare l'opzione Initialize log area (Inizializza area log)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selezionare **Fine**.

   ![Completare il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Dovrebbe venire visualizzata una finestra di stato, come quella illustrata qui. Tenere presente che l'esempio si riferisce a un ripristino di emergenza di una configurazione di SAP HANA con scalabilità orizzontale a tre nodi.

![Stato del ripristino](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se il ripristino sembra bloccarsi nella schermata di **completamento** e non viene visualizzata la schermata di stato, verificare che tutte le istanze di SAP HANA nei nodi di lavoro siano in esecuzione. Se necessario, avviare manualmente le istanze di SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback da un sito di ripristino di emergenza a un sito di produzione
È possibile eseguire il failback dal ripristino di emergenza a un sito di produzione. Si consideri uno scenario in cui il failover nel sito di ripristino di emergenza è stato causato da problemi nell'area di Azure di produzione e non dalla necessità di recuperare dati persi. Per un periodo di tempo il carico di lavoro di produzione SAP è stato eseguito nel sito di ripristino di emergenza. Quando i problemi relativi al sito di produzione vengono risolti, si vuole eseguire il failback nel sito di produzione. Poiché non si vogliono perdere dati, tuttavia, il ritorno al sito di produzione prevede diversi passaggi e una stretta collaborazione con il servizio operazioni di SAP HANA in Azure. Una volta risolti i problemi, è necessario sollecitare personalmente il team operativo perché avvii la sincronizzazione di nuovo nel sito di produzione.

Questa è la sequenza dei passaggi da eseguire:

1. Il team operativo di SAP HANA in Azure avvia la sincronizzazione dei volumi di archiviazione di produzione dai volumi di archiviazione di ripristino di emergenza, che rappresentano ora lo stato di produzione. In questo stato, l'unità di istanze Large di HANA nel sito di produzione viene arrestata.
1. Il team operativo di SAP HANA in Azure monitora la replica e verifica il completamento dell'aggiornamento prima di informare il cliente.
1. È necessario arrestare le applicazioni che usano l'istanza di produzione di HANA nel sito di ripristino di emergenza. Eseguire quindi un backup del log delle transazioni HANA. Arrestare quindi l'istanza di HANA in esecuzione nelle unità HANA in istanze Large nel sito di ripristino di emergenza.
1. Dopo l'arresto dell'istanza di HANA in esecuzione nell'unità HANA in istanze Large nel sito di ripristino di emergenza, il team operativo sincronizza di nuovo i volumi dei dischi manualmente.
1. Il servizio operazioni di SAP HANA in Azure avvia nuovamente l'unità di istanze Large di HANA nel sito di produzione e la consegna all'utente. Assicurarsi che l'istanza di SAP HANA sia in uno stato di arresto nel momento in cui viene avviata l'unità HANA in istanze Large.
1. Eseguire gli stessi passaggi di ripristino del database eseguiti in precedenza per il failover nel sito di ripristino di emergenza.

## <a name="monitor-disaster-recovery-replication"></a>Monitorare la replica di ripristino di emergenza

È possibile monitorare lo stato di avanzamento della replica di archiviazione eseguendo lo script `azure_hana_replication_status.pl`. Per poter funzionare nel modo previsto, questo script deve essere eseguito da un'unità in esecuzione nella posizione di ripristino di emergenza. Lo script funziona indipendentemente dal fatto che la replica sia o meno attiva. Lo script può essere eseguito per ogni unità di istanze Large di HANA del tenant nella posizione di ripristino di emergenza. Non può essere usato per ottenere informazioni dettagliate sul volume di avvio.

Chiamare lo script con questo comando:
```
./azure_hana_replication_status.pl
```

L'output viene suddiviso per volume nelle sezioni seguenti:  

- Stato del collegamento
- Attività di replica corrente
- Snapshot più recente replicato 
- Dimensioni dello snapshot più recente
- Tempo di ritardo corrente tra snapshot, ovvero tra l'ultima replica snapshot completata e il momento attuale

Lo stato del collegamento viene visualizzato come **attivo**, a meno che il collegamento tra le posizioni non sia stato interrotto o non sia in corso un evento di failover. L'attività di replica si riferisce al fatto che sia in corso la replica dei dati oppure che i dati siano inattivi o ci siano altre attività in corso nel collegamento. L'ultimo snapshot replicato dovrebbe essere visualizzato solo come `snapmirror…`. Vengono quindi visualizzate le dimensioni dell'ultimo snapshot. Infine, viene visualizzato il tempo di ritardo. Il tempo di ritardo rappresenta il tempo tra la replica pianificata e il completamento della replica. Un tempo di ritardo può superare un'ora per la replica dei dati, in particolare per la replica iniziale, anche se la replica è stata avviata. Il tempo di ritardo continua ad aumentare fino al termine della replica in corso.

Di seguito viene fornito un esempio dell'output:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```

**Passaggi successivi**
- Consultare [Monitoraggio e risoluzione dei problemi dal lato HANA](hana-monitor-troubleshoot.md).
