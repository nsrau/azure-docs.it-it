---
title: Procedura di failover di HANA a un sito di emergenza per SAP HANA di Azure (Istanze large) | Microsoft Docs
description: Come eseguire il failover a un sito di ripristino di emergenza per SAP HANA in Azure (Istanze large)
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
ms.openlocfilehash: 3f3b37a6336c578ed25d8ab9553bc1ea9c79872f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117208"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura di failover di ripristino di emergenza


>[!IMPORTANT]
>Questo articolo non sostituisce la documentazione relativa all'amministrazione di SAP HANA o le note su SAP. Si presuppone che si abbia una solida comprensione ed esperienza in termini di amministrazione e operazioni di SAP HANA, in particolare per quanto riguarda gli aspetti di backup, ripristino, disponibilità elevata e ripristino di emergenza. Gli screenshot di questo articolo fanno riferimento a SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

Per il failover a un sito di ripristino di emergenza, esistono due casi da considerare:

- È necessario riportare il database SAP HANA allo stato più recente dei dati. In questo caso, è disponibile uno script self-service che consente di eseguire il failover senza la necessità di contattare Microsoft. Per il failback è invece necessario collaborare con Microsoft.
- Si vuole ripristinare uno snapshot di archiviazione che non è quello più recente replicato. In questo caso, è necessario operare insieme a Microsoft. 

>[!NOTE]
>È necessario eseguire i passaggi seguenti nell'unità di istanze Large di HANA, che rappresenta l'unità di ripristino di emergenza. 
 
Per ripristinare gli ultimi snapshot di archiviazione replicati, seguire la procedura descritta in "Eseguire il failover completo di ripristino di emergenza - azure_hana_dr_failover" in [Strumenti di snapshot Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Se si vuole eseguire il failover di più istanze di SAP HANA, è necessario eseguire il comando azure_hana_dr_failover diverse volte. Quando richiesto, immettere il SID di SAP HANA di cui eseguire il failover e il ripristino. 


È possibile eseguire anche il test del failover del ripristino di emergenza senza influire sulla relazione di replica effettiva. Per eseguire un failover di test, seguire la procedura descritta in "Eseguire un failover di ripristino di emergenza di test - azure_hana_test_dr_failover" in [Strumenti di snapshot Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf). 

>[!IMPORTANT]
>*Non* eseguire transazioni di produzione nell'istanza creata nel sito di ripristino di emergenza tramite il processo di **test di un failover**. Il comando azure_hana_test_dr_failover crea un set di volumi che non hanno relazioni con il sito primario. Di conseguenza, *non* sarà possibile eseguire una nuova sincronizzazione con il sito primario. 

Se si vogliono testare più istanze di SAP HANA, eseguire lo script più volte. Quando richiesto, immettere il SID di SAP HANA dell'istanza da testare per il failover. 

>[!NOTE]
>Se è necessario eseguire il failover nel sito di ripristino di emergenza per salvare alcuni dati eliminati diverse ore prima e di conseguenza i volumi di ripristino di emergenza devono essere impostati su uno snapshot meno recente, si applica questa procedura. 

1. Arrestare l'istanza di HANA non di produzione nell'unità di ripristino di emergenza di istanze Large di HANA in esecuzione. Un'istanza di produzione HANA inattiva è preinstallata.
1. Assicurarsi che nessun processo SAP HANA sia più in esecuzione. A questo scopo, usare il comando seguente:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      L'output dovrebbe mostrare il processo **hdbdaemon** in uno stato arrestato e nessun altro processo HANA in esecuzione o con stato avviato.
1. Determinare con quale nome di snapshot o ID backup di SAP HANA si vuole ripristinare il sito di ripristino di emergenza. In casi di ripristino di emergenza reali, questo snapshot è in genere il più recente. Nel caso in cui sia necessario recuperare dati persi, selezionare uno snapshot precedente.
1. Contattare il supporto di Azure tramite una richiesta di supporto ad alta priorità. Richiedere il ripristino dello snapshot (con il nome e la data) o l'ID backup di HANA nel sito di ripristino di emergenza. Il valore predefinito prevede che il lato operazioni ripristini solo il volume /hana/data. Se si vogliono ripristinare anche i volumi /hana/logbackups, è necessario indicarlo in modo specifico. *Non ripristinare il volume /hana/shared.* È preferibile invece scegliere file specifici, ad esempio global.ini, dalla directory **.snapshot** e dalle relative sottodirectory dopo aver rimontato il volume /hana/shared per l'ambiente di produzione. 

   Sul lato operazioni si verificano i passaggi seguenti:

   a. Viene arrestata la replica degli snapshot dal volume di produzione ai volumi di ripristino di emergenza. È possibile che questa interruzione si sia già verificata se un'interruzione di corrente del sito di produzione è il motivo per cui è stato necessario eseguire la procedura di ripristino di emergenza.
   
   b. Il nome dello snapshot di archiviazione o con l'ID di backup selezionato viene ripristinato nei volumi di ripristino di emergenza.
   
   c. Dopo il ripristino, i volumi di ripristino di emergenza sono disponibili per essere montati nelle unità HANA in istanze Large nell'area di ripristino di emergenza.
      
1. Montare i volumi di ripristino di emergenza nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 
1. Avviare l'istanza di produzione di SAP HANA inattiva.
1. Se si sceglie di copiare i log di backup del log delle transazioni per ridurre il valore RPO, è necessario unire questi backup nella nuova directory di ripristino di emergenza /hana/logbackups appena montata. Non sovrascrivere i backup esistenti, ma copiare quelli più recenti che non sono stati replicati con l'ultima replica di uno snapshot di archiviazione.
1. È anche possibile ripristinare singoli file degli snapshot replicati nel volume /hana/shared/PRD nell'area di Azure di ripristino di emergenza.

I passaggi seguenti illustrano come ripristinare l'istanza di produzione di SAP HANA in base allo snapshot di archiviazione ripristinato e ai backup del log delle transazioni disponibili.

1. Modificare il percorso di backup in **/hana/logbackups** con SAP HANA Studio.

   ![Cambiare il percorso di backup per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA analizza i percorsi dei file di backup e suggerisce il backup del log delle transazioni più recente per il ripristino. L'analisi può richiedere alcuni minuti, fino a quando non viene visualizzata una schermata simile alla seguente:

   ![Elenco dei backup del log delle transazioni per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modificare alcune delle impostazioni predefinite:

      - Deselezionare **Use Delta Backups** (Usa backup differenziali).
      - Selezionare **Initialize Log Area** (Inizializza area log).

   ![Impostare l'opzione Initialize log area (Inizializza area log)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selezionare **Fine**.

   ![Completare il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Dovrebbe venire visualizzata una finestra di stato, come quella illustrata qui. Tenere presente che l'esempio si riferisce a un ripristino di emergenza di una configurazione di SAP HANA con scalabilità orizzontale a tre nodi.

![Stato del ripristino](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se il ripristino smette di rispondere nella schermata di **completamento** e non viene visualizzata la schermata di stato, verificare che tutte le istanze di SAP HANA nei nodi di lavoro siano in esecuzione. Se necessario, avviare manualmente le istanze di SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback da un sito di ripristino di emergenza a un sito di produzione
È possibile eseguire il failback dal ripristino di emergenza a un sito di produzione. Si consideri uno scenario in cui il failover nel sito di ripristino di emergenza è stato causato da problemi nell'area di Azure di produzione e non dalla necessità di recuperare dati persi. 

Per un periodo di tempo il carico di lavoro di produzione SAP è stato eseguito nel sito di ripristino di emergenza. Quando i problemi relativi al sito di produzione vengono risolti, si vuole eseguire il failback nel sito di produzione. Poiché non si vogliono perdere dati, tuttavia, il ritorno al sito di produzione prevede diversi passaggi e una stretta collaborazione con il servizio operazioni di SAP HANA in Azure. Una volta risolti i problemi, è necessario sollecitare personalmente il team operativo perché avvii la sincronizzazione di nuovo nel sito di produzione.

A tale scopo, seguire questa procedura:

1. Il team operativo di SAP HANA in Azure avvia la sincronizzazione dei volumi di archiviazione di produzione dai volumi di archiviazione di ripristino di emergenza, che rappresentano ora lo stato di produzione. In questo stato, l'unità di istanze Large di HANA nel sito di produzione viene arrestata.
1. Il team operativo di SAP HANA in Azure monitora la replica e verifica il completamento dell'aggiornamento prima di informare il cliente.
1. È necessario arrestare le applicazioni che usano l'istanza di produzione di HANA nel sito di ripristino di emergenza. Eseguire quindi un backup del log delle transazioni HANA. Arrestare quindi l'istanza di HANA in esecuzione nelle unità di istanze Large di HANA nel sito di ripristino di emergenza.
1. Dopo l'arresto dell'istanza di HANA in esecuzione nell'unità di istanze Large di HANA nel sito di ripristino di emergenza, il team operativo sincronizza di nuovo i volumi dei dischi manualmente.
1. Il team operativo di SAP HANA in Azure avvia di nuovo l'unità di istanze Large di HANA nel sito di produzione e la consegna al cliente. Assicurarsi che l'istanza di SAP HANA sia in uno stato di arresto nel momento in cui viene avviata l'unità di istanze Large di HANA.
1. Eseguire gli stessi passaggi di ripristino del database completati in precedenza per il failover nel sito di ripristino di emergenza.

## <a name="monitor-disaster-recovery-replication"></a>Monitorare la replica di ripristino di emergenza

Per monitorare lo stato di avanzamento della replica di archiviazione, eseguire lo script `azure_hana_replication_status`. Per il corretto funzionamento, questo comando deve essere eseguito da un'unità in esecuzione nel sito di ripristino di emergenza. Il comando funziona indipendentemente dal fatto che la replica sia attiva e può essere eseguito per ogni unità di istanze Large di HANA del tenant nel sito di ripristino di emergenza. Non può essere usato per ottenere informazioni dettagliate sul volume di avvio. 

Per altre informazioni sul comando e sul relativo output, vedere "Ottenere lo stato della replica del ripristino di emergenza - azure_hana_replication_status" in [Strumenti di snapshot Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf).


## <a name="next-steps"></a>Passaggi successivi
- Vedere [Monitoraggio e risoluzione dei problemi dal lato HANA](hana-monitor-troubleshoot.md).
