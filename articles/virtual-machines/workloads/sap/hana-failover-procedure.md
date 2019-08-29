---
title: Procedura di failover HANA in un sito di emergenza per SAP HANA in Azure (istanze large) | Microsoft Docs
description: Come eseguire il failover in un sito di ripristino di emergenza per SAP HANA in Azure (istanze large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c2c8483948deae41edbe3922dc77361ba2c58a94
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099873"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura di failover di ripristino di emergenza


>[!IMPORTANT]
>Questo articolo non sostituisce la documentazione di amministrazione SAP HANA o le note SAP. Ci si aspetta che l'utente abbia una conoscenza approfondita di e le competenze in SAP HANA amministrazione e operazioni, specialmente per il backup, il ripristino, la disponibilità elevata e il ripristino di emergenza. In questo articolo vengono visualizzate le schermate di SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

Quando si esegue il failover in un sito di ripristino di emergenza, è necessario prendere in considerazione due casi:

- È necessario riportare il database SAP HANA allo stato più recente dei dati. In questo caso, è disponibile uno script self-service con cui è possibile eseguire il failover senza la necessità di contattare Microsoft. Per il failback, è necessario collaborare con Microsoft.
- Si vuole eseguire il ripristino in uno snapshot di archiviazione che non è lo snapshot replicato più recente. In questo caso, è necessario operare insieme a Microsoft. 

>[!NOTE]
>I passaggi seguenti devono essere eseguiti nell'unità di istanze large di HANA, che rappresenta l'unità di ripristino di emergenza. 
 
Per ripristinare gli snapshot di archiviazione replicati più recenti, seguire la procedura descritta in "eseguire il ripristino completo del ripristino di emergenza-azure_hana_dr_failover" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). 

Se si desidera eseguire il failover di più istanze di SAP HANA, eseguire il comando azure_hana_dr_failover più volte. Quando richiesto, immettere il SID SAP HANA di cui si vuole eseguire il failover e il ripristino. 


È possibile testare il failover di ripristino di emergenza anche senza alcun effetto sulla relazione di replica effettiva. Per eseguire un failover di test, seguire la procedura descritta in "eseguire un test di failover del ripristino di emergenza-azure_hana_test_dr_failover" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf). 

>[!IMPORTANT]
>*Non* eseguire alcuna transazione di produzione nell'istanza di creata nel sito di ripristino di emergenza tramite il processo di **test di un failover**. Il comando azure_hana_test_dr_failover crea un set di volumi che non hanno alcuna relazione con il sito primario. Di conseguenza, *non* sarà possibile eseguire una nuova sincronizzazione con il sito primario. 

Se si desidera eseguire il test di più istanze di SAP HANA, eseguire lo script più volte. Quando richiesto, immettere il SID SAP HANA dell'istanza di che si desidera testare per il failover. 

>[!NOTE]
>Se è necessario eseguire il failover nel sito di ripristino di emergenza per salvare alcuni dati che sono stati eliminati ore fa ed è necessario che i volumi di ripristino di emergenza siano impostati su uno snapshot precedente, questa procedura viene applicata. 

1. Arrestare l'istanza di non produzione di HANA nell'unità di ripristino di emergenza di istanze large di HANA in esecuzione. Un'istanza di produzione HANA inattiva è preinstallata.
1. Assicurarsi che nessun processo SAP HANA sia più in esecuzione. Per questo controllo, usare il comando seguente:

      [https://login.microsoftonline.com/common/](`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`).

      L'output dovrebbe mostrare il processo **hdbdaemon** in uno stato arrestato e nessun altro processo HANA in esecuzione o con stato avviato.
1. Determinare con quale nome di snapshot o ID backup di SAP HANA si vuole ripristinare il sito di ripristino di emergenza. In casi di ripristino di emergenza reali, questo snapshot è in genere il più recente. Nel caso in cui sia necessario recuperare dati persi, selezionare uno snapshot precedente.
1. Contattare il supporto di Azure tramite una richiesta di supporto ad alta priorità. Richiedere il ripristino dello snapshot con il nome e la data dello snapshot o l'ID di backup HANA nel sito di ripristino di emergenza. Il valore predefinito prevede che il lato operazioni ripristini solo il volume /hana/data. Se si desidera disporre anche dei volumi/Hana/logbackups, è necessario specificarne lo stato. *Non ripristinare il volume /hana/shared.* Al contrario, scegliere file specifici, ad esempio Global. ini, dalla directory **. snapshot** e dalle relative sottodirectory dopo aver rimontato il volume/Hana/Shared per PRD. 

   Sul lato operazioni si verificano i passaggi seguenti:

   a. Viene arrestata la replica degli snapshot dal volume di produzione ai volumi di ripristino di emergenza. È possibile che questa interruzione si sia già verificata se un'interruzione di corrente del sito di produzione è il motivo per cui è stato necessario eseguire la procedura di ripristino di emergenza.
   
   b. Il nome dello snapshot di archiviazione o con l'ID di backup selezionato viene ripristinato nei volumi di ripristino di emergenza.
   
   c. Dopo il ripristino, i volumi di ripristino di emergenza sono disponibili per essere montati nelle unità HANA in istanze Large nell'area di ripristino di emergenza.
      
1. Montare i volumi di ripristino di emergenza nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 
1. Avviare l'istanza di produzione di SAP HANA inattiva.
1. Se si sceglie di copiare i log di backup del log delle transazioni per ridurre il tempo di RPO, unire i backup del log delle transazioni nella directory di ripristino di emergenza/Hana/logbackups appena montata. Non sovrascrivere i backup esistenti, Copiare i backup più recenti che non sono stati replicati con l'ultima replica di uno snapshot di archiviazione.
1. È anche possibile ripristinare singoli file da snapshot che non sono stati replicati nel volume nel/Hana/Shared/PRD nell'area di Azure di ripristino di emergenza.

Nei passaggi seguenti viene illustrato come ripristinare l'istanza di produzione SAP HANA in base allo snapshot di archiviazione ripristinato e ai backup del log delle transazioni disponibili.

1. Modificare il percorso di backup in **/hana/logbackups** con SAP HANA Studio.

   ![Modificare il percorso di backup per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA analizza i percorsi dei file di backup e suggerisce il backup del log delle transazioni più recente per il ripristino. L'analisi può richiedere alcuni minuti fino a quando non viene visualizzata una schermata simile alla seguente:

   ![Elenco dei backup del log delle transazioni per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modificare alcune delle impostazioni predefinite:

      - Deselezionare **Use Delta Backups** (Usa backup differenziali).
      - Selezionare **Initialize Log Area** (Inizializza area log).

   ![Impostare l'opzione Initialize log area (Inizializza area log)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selezionare **Fine**.

   ![Completare il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Dovrebbe venire visualizzata una finestra di stato, come quella illustrata qui. Tenere presente che l'esempio si riferisce a un ripristino di emergenza di una configurazione di SAP HANA con scalabilità orizzontale a tre nodi.

![Stato del ripristino](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se il ripristino smette di rispondere alla schermata **finale** e non Visualizza la schermata di stato, verificare che tutte le istanze SAP Hana nei nodi di lavoro siano in esecuzione. Se necessario, avviare manualmente le istanze di SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback da un sito di ripristino di emergenza a un sito di produzione
È possibile eseguire il failback dal ripristino di emergenza a un sito di produzione. Si consideri uno scenario in cui il failover nel sito di ripristino di emergenza è stato causato da problemi nell'area di Azure di produzione e non dalla necessità di recuperare dati persi. 

È stato eseguito il carico di lavoro di produzione SAP per un periodo di tempo nel sito di ripristino di emergenza. Quando i problemi relativi al sito di produzione vengono risolti, si vuole eseguire il failback nel sito di produzione. Poiché non si vogliono perdere dati, tuttavia, il ritorno al sito di produzione prevede diversi passaggi e una stretta collaborazione con il servizio operazioni di SAP HANA in Azure. Una volta risolti i problemi, è necessario sollecitare personalmente il team operativo perché avvii la sincronizzazione di nuovo nel sito di produzione.

A tale scopo, seguire questa procedura:

1. Il team operativo di SAP HANA in Azure avvia la sincronizzazione dei volumi di archiviazione di produzione dai volumi di archiviazione di ripristino di emergenza, che rappresentano ora lo stato di produzione. In questo stato, l'unità di istanze Large di HANA nel sito di produzione viene arrestata.
1. Il SAP HANA nel team operativo di Azure monitora la replica e verifica che venga rilevata prima di informare l'utente.
1. È necessario arrestare le applicazioni che usano l'istanza di produzione di HANA nel sito di ripristino di emergenza. Eseguire quindi un backup del log delle transazioni HANA. Arrestare quindi l'istanza di HANA in esecuzione nelle unità di istanze large di HANA nel sito di ripristino di emergenza.
1. Dopo che l'istanza di HANA in esecuzione nell'unità di istanze large di HANA nel sito di ripristino di emergenza è stata arrestata, il team operativo Sincronizza di nuovo manualmente i volumi dei dischi.
1. Il SAP HANA nel team operativo di Azure avvia di nuovo l'unità HANA in istanze large nel sito di produzione. Vengono consegnati all'utente. Assicurarsi che l'istanza di SAP HANA sia in uno stato di arresto al momento dell'avvio dell'unità di istanze large di HANA.
1. Eseguire gli stessi passaggi di ripristino del database eseguiti in precedenza durante il failover nel sito di ripristino di emergenza.

## <a name="monitor-disaster-recovery-replication"></a>Monitorare la replica di ripristino di emergenza

Per monitorare lo stato di avanzamento della replica di archiviazione, eseguire lo `azure_hana_replication_status`script. Questo comando deve essere eseguito da un'unità in esecuzione nella posizione di ripristino di emergenza per funzionare come previsto. Il comando funziona indipendentemente dal fatto che la replica sia attiva. Il comando può essere eseguito per ogni unità di istanze large di HANA del tenant nella posizione di ripristino di emergenza. Non può essere usato per ottenere informazioni dettagliate sul volume di avvio. 

Per ulteriori informazioni sul comando e sul relativo output, vedere "Get DR Replication Status-azure_hana_replication_status" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Passaggi successivi
- Vedere [monitorare e risolvere i problemi dal lato Hana](hana-monitor-troubleshoot.md).
