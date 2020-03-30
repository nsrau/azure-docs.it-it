---
title: Procedura di failover HANA in un sito di emergenza per SAP HANA in Azure (istanze di grandi dimensioni) Documenti Microsoft
description: Come eseguire il failover in un sito di ripristino di emergenza per SAP HANA in Azure (istanze di grandi dimensioni)How to perform failover to a disaster recovery site for SAP HANA on Azure (Large Instances)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617147"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura di failover di ripristino di emergenza


>[!IMPORTANT]
>Questo articolo non sostituisce la documentazione di amministrazione SAP HANA o le note SAP. Ci aspettiamo di avere una solida conoscenza e competenza nell'amministrazione e nelle operazioni SAP HANA, in particolare per il backup, il ripristino, la disponibilità elevata e il ripristino di emergenza (DR). In questo articolo vengono visualizzate le schermate di SAP HANA Studio.In this article, screenshots from SAP HANA Studio are shown. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

Esistono due casi da considerare quando si esegue il failover in un sito di ripristino di emergenza:There are two cases to consider when you fail over to a DR site:

- È necessario riportare il database SAP HANA allo stato più recente dei dati. In this case, there's a self-service script with which you can perform the failover without the need to contact Microsoft. Per il failback, è necessario lavorare con Microsoft.
- Si desidera ripristinare in uno snapshot di archiviazione diverso lo snapshot replicato più recente. In questo caso, è necessario operare insieme a Microsoft. 

>[!NOTE]
>I passaggi seguenti devono essere eseguiti nell'unità istanza HANA Large Instance, che rappresenta l'unità di ripristino di emergenza. 
 
Per ripristinare gli snapshot di archiviazione replicati più recenti, seguire la procedura descritta in "Eseguire il failover completo del ripristino di emergenza - azure_hana_dr_failover" in [Strumenti di snapshot Microsoft per SAP HANA in Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf) 

Se si desidera eseguire il failover di più istanze SAP HANA, eseguire più volte il comando azure_hana_dr_failover. Quando richiesto, immettere il SID SAP HANA di cui si desidera eseguire il failover e il ripristino. 


È inoltre possibile testare il failover di ripristino di emergenza senza influire sulla relazione di replica effettiva. Per eseguire un failover di test, seguire i passaggi descritti in "Eseguire un failover di ripristino di emergenza di prova - azure_hana_test_dr_failover" in [strumenti snapshot Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf). 

>[!IMPORTANT]
>*Non* eseguire transazioni di produzione nell'istanza creata nel sito di ripristino di emergenza tramite il processo di test di **un failover.** Il comando azure_hana_test_dr_failover crea un set di volumi che non hanno alcuna relazione con il sito primario. Di conseguenza, *non* sarà possibile eseguire una nuova sincronizzazione con il sito primario. 

Se si desidera disporre di più istanze SAP HANA da testare, eseguire lo script più volte. Quando richiesto, immettere il SID SAP HANA dell'istanza che si desidera testare per il failover. 

>[!NOTE]
>Se è necessario eseguire il failover al sito di ripristino di emergenza per salvare alcuni dati eliminati ore fa ed è necessario impostare i volumi di ripristino di emergenza su uno snapshot precedente, questa procedura è valida. 

1. Arrestare l'istanza non di produzione di HANA nell'unità di ripristino di emergenza delle istanze di grandi dimensioni HANA in esecuzione. È preinstallata un'istanza di produzione HANA inattività.
1. Assicurarsi che nessun processo SAP HANA sia più in esecuzione. Utilizzare il comando seguente per questo controllo:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      L'output dovrebbe mostrare il processo **hdbdaemon** in uno stato di arresto e nessun altro processo HANA in fase di esecuzione o avvio.
1. Determinare con quale nome di snapshot o ID backup di SAP HANA si vuole ripristinare il sito di ripristino di emergenza. In casi di ripristino di emergenza reali, questo snapshot è in genere il più recente. Nel caso in cui sia necessario recuperare dati persi, selezionare uno snapshot precedente.
1. Contattare il supporto di Azure tramite una richiesta di supporto ad alta priorità. Richiedere il ripristino dello snapshot con il nome e la data dello snapshot o l'ID di backup HANA nel sito di ripristino di emergenza. Il valore predefinito prevede che il lato operazioni ripristini solo il volume /hana/data. Se si desidera disporre anche dei volumi /hana/logbackups, è necessario indicare in modo specifico tale volume. *Non ripristinare il volume /hana/shared.* Scegliere invece file specifici come global.ini dalla directory **.snapshot** e dalle relative sottodirectory dopo aver rimontato il volume /hana/shared per PRD. 

   Sul lato operazioni si verificano i passaggi seguenti:

   a. Viene arrestata la replica degli snapshot dal volume di produzione ai volumi di ripristino di emergenza. È possibile che questa interruzione si sia già verificata se un'interruzione di corrente del sito di produzione è il motivo per cui è stato necessario eseguire la procedura di ripristino di emergenza.
   
   b. Il nome dello snapshot di archiviazione o con l'ID di backup selezionato viene ripristinato nei volumi di ripristino di emergenza.
   
   c. Dopo il ripristino, i volumi di ripristino di emergenza sono disponibili per essere montati nelle unità HANA in istanze Large nell'area di ripristino di emergenza.
      
1. Montare i volumi di ripristino di emergenza nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 
1. Avviare l'istanza di produzione di SAP HANA inattiva.
1. Se si è scelto di copiare i log di backup del log delle transazioni per ridurre il tempo RPO, unire i backup del log delle transazioni nella directory /hana/logbackups di ripristino di emergenza appena montata. Non sovrascrivere i backup esistenti, Copiare i backup più recenti che non sono stati replicati con la replica più recente di uno snapshot di archiviazione.
1. È anche possibile ripristinare singoli file dagli snapshot che non sono stati replicati nel volume /hana/shared/PRD nell'area di Azure di ripristino di emergenza.

I passaggi seguenti illustrano come ripristinare l'istanza di produzione SAP HANA in base allo snapshot di archiviazione ripristinato e ai backup del log delle transazioni disponibili.

1. Modificare il percorso di backup in **/hana/logbackups** con SAP HANA Studio.

   ![Modificare il percorso di backup per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA analizza i percorsi dei file di backup e suggerisce il backup del log delle transazioni più recente per il ripristino. La scansione può richiedere alcuni minuti prima che venga visualizzata una schermata simile alla seguente:

   ![Elenco dei backup del log delle transazioni per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modificare alcune delle impostazioni predefinite:

      - Deselezionare **Use Delta Backups** (Usa backup differenziali).
      - Selezionare **Initialize Log Area** (Inizializza area log).

   ![Impostare l'opzione Initialize log area (Inizializza area log)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Fare clic su **Fine**.

   ![Completare il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Dovrebbe venire visualizzata una finestra di stato, come quella illustrata qui. Tenere presente che l'esempio si riferisce a un ripristino di emergenza di una configurazione di SAP HANA con scalabilità orizzontale a tre nodi.

![Stato del ripristino](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se il ripristino si blocca nella schermata **Fine** e non visualizza la schermata di stato, verificare che tutte le istanze SAP HANA nei nodi di lavoro siano in esecuzione. Se necessario, avviare manualmente le istanze di SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback da un sito di ripristino di emergenza a un sito di produzione
È possibile eseguire il failback dal ripristino di emergenza a un sito di produzione. Si consideri uno scenario in cui il failover nel sito di ripristino di emergenza è stato causato da problemi nell'area di Azure di produzione e non dalla necessità di recuperare dati persi. 

Il carico di lavoro di produzione SAP è stato eseguito per un po' di tempo nel sito di ripristino di emergenza. Quando i problemi relativi al sito di produzione vengono risolti, si vuole eseguire il failback nel sito di produzione. Poiché non si vogliono perdere dati, tuttavia, il ritorno al sito di produzione prevede diversi passaggi e una stretta collaborazione con il servizio operazioni di SAP HANA in Azure. Una volta risolti i problemi, è necessario sollecitare personalmente il team operativo perché avvii la sincronizzazione di nuovo nel sito di produzione.

A tale scopo, seguire questa procedura:

1. Il team operativo di SAP HANA in Azure avvia la sincronizzazione dei volumi di archiviazione di produzione dai volumi di archiviazione di ripristino di emergenza, che rappresentano ora lo stato di produzione. In questo stato, l'unità di istanze Large di HANA nel sito di produzione viene arrestata.
1. Il team operativo SAP HANA in Azure monitora la replica e verifica che venga aggiornata prima di informarla.
1. È necessario arrestare le applicazioni che usano l'istanza di produzione di HANA nel sito di ripristino di emergenza. Eseguire quindi un backup del log delle transazioni HANA. Successivamente, si arresta l'istanza HANA in esecuzione sulle unità di istanza di grandi dimensioni HANA nel sito di ripristino di emergenza.
1. Dopo l'arresto dell'istanza HANA in esecuzione nell'unità di istanza di grandi dimensioni HANA nel sito di ripristino di emergenza, il team operativo sincronizza nuovamente manualmente i volumi del disco.
1. Il team operativo SAP HANA in Azure avvia nuovamente l'unità di istanza di grandi dimensioni HANA nel sito di produzione. Te lo consegnano. Assicurarsi che l'istanza SAP HANA è in uno stato di arresto al momento dell'avvio dell'unità di istanza di grandi dimensioni HANA.
1. Si eseguono gli stessi passaggi di ripristino del database eseguiti quando in precedenza è stato eseguito il failover al sito di ripristino di emergenza.

## <a name="monitor-disaster-recovery-replication"></a>Monitorare la replica di ripristino di emergenza

Per monitorare lo stato dello stato `azure_hana_replication_status`della replica di archiviazione, eseguire lo script . Questo comando deve essere eseguito da un'unità che viene eseguita nel percorso di ripristino di emergenza per funzionare come previsto. Il comando funziona indipendentemente dal fatto che la replica sia attiva. Il comando può essere eseguito per ogni unità di istanza HANA Large Del tenant nel percorso di ripristino di emergenza. Non può essere utilizzato per ottenere dettagli sul volume di avvio. 

Per altre informazioni sul comando e sul relativo output, vedere "Ottenere lo stato di replica di ripristino di emergenza - azure_hana_replication_status" in [Strumenti snapshot Microsoft per SAP HANA in Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)


## <a name="next-steps"></a>Passaggi successivi
- Consultate [Monitorare e risolvere i problemi dal lato HANA.](hana-monitor-troubleshoot.md)
