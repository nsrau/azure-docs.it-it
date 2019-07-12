---
title: Procedura di failover di HANA in un sito di emergenza per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Come eseguire il failover a un sito di ripristino di emergenza per SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6454c82e3d9c73d1b5a4b2224abf1ab63a798355
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709636"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura di failover di ripristino di emergenza


>[!IMPORTANT]
>Questo articolo non è una sostituzione per la documentazione relativa all'amministrazione di SAP HANA o SAP note. È probabile che si dispone di una solida comprensione ed esperienza nell'amministrazione di SAP HANA e le operazioni, in particolare per il backup, ripristino, disponibilità elevata e ripristino di emergenza (DR). In questo articolo, vengono visualizzate schermate da SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

Esistono due casi diversi da prendere in considerazione quando eseguire il failover a un sito di ripristino di emergenza:

- È necessario riportare il database SAP HANA allo stato più recente dei dati. In questo caso, vi è uno script self-service a cui è possibile eseguire il failover senza la necessità di contattare Microsoft. Per il failback, è necessario operare insieme a Microsoft.
- Si desidera ripristinare uno snapshot di archiviazione che non è lo snapshot più recente replicato. In questo caso, è necessario operare insieme a Microsoft. 

>[!NOTE]
>I passaggi seguenti devono essere eseguiti nell'unità di istanze Large di HANA, che rappresenta l'unità di ripristino di emergenza. 
 
Per ripristinare gli snapshot più recente archiviazione replicata, seguire la procedura descritta in "Esegui completa failover di ripristino di emergenza - azure_hana_dr_failover" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Se si desidera disporre di più istanze di SAP HANA effettuate il failover, eseguire il comando azure_hana_dr_failover più volte. Quando richiesto, immettere il SID di SAP HANA si vuole eseguire il failover e ripristino. 


È possibile testare anche il failover di ripristino di emergenza senza conseguenze per la relazione di replica effettiva. Per eseguire un failover di test, seguire la procedura descritta in "Eseguire un test di failover di ripristino di emergenza - azure_hana_test_dr_failover" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Effettuare *non* eseguire transazioni di produzione nell'istanza che è stato creato nel sito di ripristino di emergenza tramite il processo di **test di un failover**. Azure_hana_test_dr_failover il comando crea un set di volumi che non hanno relazione con il sito primario. Di conseguenza, *non* sarà possibile eseguire una nuova sincronizzazione con il sito primario. 

Se si desidera disporre di più istanze di SAP HANA per testare, eseguire lo script più volte. Quando richiesto, immettere il SID SAP HANA dell'istanza da testare per il failover. 

>[!NOTE]
>Se è necessario eseguire il failover al sito di ripristino di emergenza per salvare alcuni dati che sono stati eliminati ore fa ed è necessario i volumi di ripristino di emergenza sia impostato su uno snapshot precedente, questa procedura è valida. 

1. Arrestare l'istanza non di produzione di HANA in unità di ripristino di emergenza di istanze Large di HANA in esecuzione. Un'istanza di produzione HANA inattiva preinstallata.
1. Assicurarsi che nessun processo SAP HANA sia più in esecuzione. A questo scopo, usare il comando seguente:

      [https://login.microsoftonline.com/common/](`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`).

      L'output dovrebbe mostrare il processo **hdbdaemon** in uno stato arrestato e nessun altro processo HANA in esecuzione o con stato avviato.
1. Determinare con quale nome di snapshot o ID backup di SAP HANA si vuole ripristinare il sito di ripristino di emergenza. In casi di ripristino di emergenza reali, questo snapshot è in genere il più recente. Nel caso in cui sia necessario recuperare dati persi, selezionare uno snapshot precedente.
1. Contattare il supporto di Azure tramite una richiesta di supporto ad alta priorità. Richiedere il ripristino dello snapshot con il nome e data di snapshot o l'ID di backup HANA nel sito di ripristino di emergenza. Il valore predefinito prevede che il lato operazioni ripristini solo il volume /hana/data. Se si desidera disporre anche i volumi hana/logbackups, è necessario in modo specifico. *Non ripristinare il volume /hana/shared.* Al contrario, scegliere file specifici, ad esempio Global il **snapshot** directory e dalle relative sottodirectory dopo rimontato il volume /Hana/Shared per PRD. 

   Sul lato operazioni si verificano i passaggi seguenti:

   a. Viene arrestata la replica degli snapshot dal volume di produzione ai volumi di ripristino di emergenza. È possibile che questa interruzione si sia già verificata se un'interruzione di corrente del sito di produzione è il motivo per cui è stato necessario eseguire la procedura di ripristino di emergenza.
   
   b. Il nome dello snapshot di archiviazione o con l'ID di backup selezionato viene ripristinato nei volumi di ripristino di emergenza.
   
   c. Dopo il ripristino, i volumi di ripristino di emergenza sono disponibili per essere montati nelle unità HANA in istanze Large nell'area di ripristino di emergenza.
      
1. Montare i volumi di ripristino di emergenza nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 
1. Avviare l'istanza di produzione di SAP HANA inattiva.
1. Se si sceglie di copiare i log di backup del log delle transazioni per ridurre l'obiettivo del punto, unire i backup del log delle transazioni nella directory appena montata di ripristino di emergenza/hana/logbackups. Non sovrascrivere i backup esistenti, Copiare i backup più recente che non sono stati replicati con l'ultima replica di uno snapshot di archiviazione.
1. È anche possibile ripristinare singoli file di snapshot non sono stati replicati nel volume /hana/shared/PRD nell'area di Azure di ripristino di emergenza.

La procedura seguente illustra come ripristinare l'istanza di produzione di SAP HANA basato su snapshot di archiviazione ripristinato e i backup del log delle transazioni disponibili.

1. Modificare il percorso di backup in **/hana/logbackups** con SAP HANA Studio.

   ![Modificare il percorso di backup per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA analizza i percorsi dei file di backup e suggerisce il backup del log delle transazioni più recente per il ripristino. L'analisi può richiedere alcuni minuti fino a una schermata simile viene visualizzato quanto segue:

   ![Elenco di backup del log delle transazioni per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modificare alcune delle impostazioni predefinite:

      - Deselezionare **Use Delta Backups** (Usa backup differenziali).
      - Selezionare **Initialize Log Area** (Inizializza area log).

   ![Impostare l'opzione Initialize log area (Inizializza area log)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selezionare **Fine**.

   ![Completare il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Dovrebbe venire visualizzata una finestra di stato, come quella illustrata qui. Tenere presente che l'esempio si riferisce a un ripristino di emergenza di una configurazione di SAP HANA con scalabilità orizzontale a tre nodi.

![Stato del ripristino](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se il ripristino si blocca la **fine** schermata e non viene visualizzata la schermata di stato, verificare che tutte le istanze di SAP HANA nei nodi di lavoro sono in esecuzione. Se necessario, avviare manualmente le istanze di SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback da un sito di ripristino di emergenza a un sito di produzione
È possibile eseguire il failback dal ripristino di emergenza a un sito di produzione. Si consideri uno scenario in cui il failover nel sito di ripristino di emergenza è stato causato da problemi nell'area di Azure di produzione e non dalla necessità di recuperare dati persi. 

Si usa il carico di lavoro di produzione di SAP per un periodo di tempo nel sito di ripristino di emergenza. Quando i problemi relativi al sito di produzione vengono risolti, si vuole eseguire il failback nel sito di produzione. Poiché non si vogliono perdere dati, tuttavia, il ritorno al sito di produzione prevede diversi passaggi e una stretta collaborazione con il servizio operazioni di SAP HANA in Azure. Una volta risolti i problemi, è necessario sollecitare personalmente il team operativo perché avvii la sincronizzazione di nuovo nel sito di produzione.

Attenersi ai passaggi riportati di seguito.

1. Il team operativo di SAP HANA in Azure avvia la sincronizzazione dei volumi di archiviazione di produzione dai volumi di archiviazione di ripristino di emergenza, che rappresentano ora lo stato di produzione. In questo stato, l'unità di istanze Large di HANA nel sito di produzione viene arrestata.
1. SAP HANA in team operativo di Azure monitora la replica e assicura che si sia aggiornato prima che essi informare l'utente.
1. È necessario arrestare le applicazioni che usano l'istanza di produzione di HANA nel sito di ripristino di emergenza. Eseguire quindi un backup del log delle transazioni HANA. Arrestare quindi l'istanza di HANA che è in esecuzione nelle unità di istanze Large di HANA nel sito di ripristino di emergenza.
1. Dopo che l'istanza di HANA che è in esecuzione nell'unità di istanze Large di HANA nel sito di ripristino di emergenza viene arrestata, il team operativo Sincronizza manualmente nuovamente i volumi dei dischi.
1. SAP HANA in team operativo di Azure viene riavviata l'unità di istanze Large di HANA nel sito di produzione. Essi consegna all'utente. Assicurarsi che l'istanza di SAP HANA sia in uno stato di chiusura in fase di avvio dell'unità di istanze Large di HANA.
1. Eseguire gli stessi passaggi di ripristino di database che è stato fatto quando precedentemente eseguito il failover al sito di ripristino di emergenza.

## <a name="monitor-disaster-recovery-replication"></a>Monitorare la replica di ripristino di emergenza

Per monitorare lo stato di avanzamento della replica di archiviazione, eseguire lo script `azure_hana_replication_status`. Questo comando deve essere eseguito da un'unità che viene eseguito nella posizione di ripristino di emergenza a funzionare come previsto. Il comando funziona indipendentemente dal fatto che la replica sia attiva. Il comando può essere eseguito per ogni unità di istanze Large di HANA del tenant nella posizione di ripristino di emergenza. Non è utilizzabile per ottenere informazioni dettagliate sul volume di avvio. 

Per altre informazioni sul comando e il relativo output, vedere "Ottieni lo stato della replica di ripristino di emergenza - azure_hana_replication_status" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Passaggi successivi
- Visualizzare [monitoraggio e risoluzione dei problemi dal lato HANA](hana-monitor-troubleshoot.md).
