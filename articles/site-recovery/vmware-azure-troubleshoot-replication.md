---
title: Risolvere i problemi di replica per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di replica comunemente riscontrati durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: c53dc81da9469c0628adbd3751dc818997fa4d05
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063679"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Risolvere i problemi di replica per macchine virtuali VMware e server fisici

Potrebbe essere visualizzato un messaggio di errore specifico durante la protezione delle macchine virtuali VMware o dei server fisici con Azure Site Recovery. Questo articolo descrive alcuni problemi comuni che possono verificarsi durante l'esecuzione della replica di macchine virtuali VMware e server fisici locali in Azure usando [Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problemi di replica iniziale

Spesso gli errori di replica iniziale sono causati da problemi di connettività tra il server di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. Nella maggior parte dei casi è possibile risolvere questi problemi completando i passaggi elencati nelle sezioni seguenti.

### <a name="check-the-source-machine"></a>Controllare il computer di origine

L'elenco seguente mostra i modi in cui è possibile controllare il computer di origine:

*  Nella riga di comando del server di origine usare Telnet per effettuare il ping del server di elaborazione tramite la porta HTTPS (la porta HTTPS predefinita è 9443) eseguendo il comando seguente. Il comando verifica la presenza di problemi di connettività di rete così come di problemi che blocchino la porta del firewall.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Usare Telnet per testare la connettività. Non usare `ping`. Se Telnet non è installato, completare i passaggi elencati in [Install Telnet Client](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) (Installare il client Telnet).

   Se non è possibile connettersi al server di elaborazione, consentire la porta in ingresso 9443 nel server di elaborazione. Ad esempio potrebbe essere necessario consentire la porta in ingresso 9443 nel server di elaborazione se la rete dispone di una rete perimetrale (o screened subnet). Quindi verificare se il problema persiste.

*  Controllare lo stato del servizio **InMage Scout VX Agent – Sentinel/OutpostStart**. Se il servizio non è in esecuzione, avviarlo e quindi verificare se il problema persiste.   

### <a name="check-the-process-server"></a>Controllare il server di elaborazione

L'elenco seguente mostra i modi in cui è possibile controllare il server di elaborazione:

*  **Controllare se il server di elaborazione esegue attivamente il push dei dati in Azure**.

   1. Nel server di elaborazione aprire Gestione attività (premere CTRL+MAIUSC+ESC).
   2. Selezionare la scheda **Prestazioni** e fare clic sul collegamento **Apri Monitoraggio risorse**. 
   3. Nella pagina di **Monitoraggio risorse** selezionare la scheda **Rete**. In **Processi con attività di rete** controllare se **cbengine.exe** sta inviando attivamente volumi di dati di grandi dimensioni.

        ![Screenshot che mostra i volumi in Processi con attività di rete](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Se cbengine.exe non invia una quantità notevole di dati, completare i passaggi illustrati nelle sezioni seguenti.

*  **Verificare se il server di elaborazione riesce a connettersi ad Archiviazione BLOB di Azure**.

   Selezionare **cbengine.exe**. In **Connessioni TCP** verificare se vi è connettività tra il server di elaborazione e l'URL di Archiviazione BLOB di Azure.

   ![Screenshot che mostra la connettività tra cbengine.exe e l'URL di Archiviazione BLOB di Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   Se non vi è connettività tra il server di elaborazione e l'URL di Archiviazione BLOB di Azure, selezionare **Servizi** in Pannello di controllo. Verificare se sono in esecuzione i servizi seguenti:

   *  cxprocessserver
   *  InMage Scout VX Agent – Sentinel/Outpost
   *  Agente di Servizi di ripristino di Microsoft Azure
   *  Servizio Microsoft Azure Site Recovery
   *  tmansvc

   Avviare o riavviare qualsiasi servizio che non sia in esecuzione. Quindi verificare se il problema persiste.

*  **Verificare se il server di elaborazione riesce a connettersi all'indirizzo IP pubblico di Azure usando la porta 443**.

   In %programfiles%\Microsoft Azure Recovery Services Agent\Temp aprire il file CBEngineCurr.errlog più recente. Nel file cercare **443** o la stringa **connection attempt failed**.

   ![Screenshot che mostra il registro degli errori nella cartella Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   Se si visualizzano dei problemi, nella riga di comando del server di elaborazione usare Telnet per effettuare il ping dell'indirizzo IP pubblico di Azure (nell'immagine precedente l'indirizzo IP è mascherato). È possibile trovare l'indirizzo IP pubblico di Azure nel file CBEngineCurr.currLog usando la porta 443:

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   In caso non sia possibile connettersi, verificare se il problema di accesso è causato dalle impostazioni del firewall o del proxy come descritto nel passaggio successivo.

*  **Controllare che il firewall basato su indirizzo IP nel server di elaborazione non blocchi l'accesso**.

   Se si utilizzano le regole del firewall basato su indirizzo IP nel server, scaricare l'elenco completo degli [intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Aggiungere gli intervalli di indirizzi IP alla configurazione del firewall per assicurarsi che il firewall consenta le comunicazioni con Azure (e con la porta HTTPS predefinita 443). Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (area usata per il controllo di accesso e la gestione delle identità).

*  **Controllare se il firewall basato su URL nel server di elaborazione blocca l'accesso**.

   Se si usa una regola del firewall basato su URL nel server, aggiungere gli URL elencati nella tabella seguente per la configurazione del firewall:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Controllare se le impostazioni del proxy nel server di elaborazione bloccano l'accesso**.

   Se si usa un server proxy, assicurarsi che il nome del server proxy venga risolto dal server DNS. Per controllare il valore fornito quando si configura il server di configurazione, passare alla chiave del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.

   Quindi verificare che le stesse impostazioni siano usate dall'agente di Azure Site Recovery per l'invio dei dati: 
      
   1. Cercare **Backup di Microsoft Azure**. 
   2. Aprire **Backup di Microsoft Azure**, quindi selezionare **Azione** > **Modifica proprietà**. 
   3. Nella scheda **Configurazione proxy** si dovrebbe visualizzare l'indirizzo del proxy. L'indirizzo del proxy dovrebbe essere uguale all'indirizzo del proxy che viene visualizzato nelle impostazioni del Registro di sistema. In caso contrario, modificarlo per impostare lo stesso indirizzo.

*  **Controllare se la limitazione della larghezza di banda è vincolata nel server di elaborazione**.

   Aumentare la larghezza di banda e controllare se il problema persiste.

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>La macchina di origine non è elencata nel portale di Azure

Quando si tenta di scegliere la macchina di origine per cui abilitare la replica tramite Site Recovery, è possibile che la macchina non sia disponibile per uno dei motivi seguenti:

*  Se In vCenter due macchine virtuali hanno lo stesso UUID di istanza, nel portale di Azure viene visualizzata solo la prima macchina virtuale individuata dal server di configurazione. Per risolvere questo problema, verificare che uno stesso UUID di istanza non sia assegnato a due macchine virtuali.
*  Assicurarsi di aver aggiunto le credenziali di vCenter corrette durante la configurazione del server di configurazione usando il modello OVF o l'installazione unificata. Per verificare le credenziali aggiunte durante la configurazione, consultare [Modificare le credenziali per l'individuazione automatica](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
*  Se le autorizzazioni fornite per accedere a vCenter non dispongono dei privilegi necessari, potrebbero verificarsi errori nell'individuazione delle macchine virtuali. Assicurarsi che le autorizzazioni descritte in [Preparare un account per l'individuazione automatica](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) vengano aggiunte all'account utente di vCenter.
*  Se la macchina virtuale è già protetta tramite Site Recovery, non risulterà disponibile per la protezione nel portale. Assicurarsi che la macchina virtuale che si sta cercando nel portale non sia già protetta da un altro utente o con altre sottoscrizioni.

## <a name="protected-virtual-machines-arent-available-in-the-portal"></a>Le macchine virtuali protette non sono disponibili nel portale

Le macchine virtuali replicate in Site Recovery non sono disponibili nel portale di Azure se nel sistema sono presenti voci duplicate. Per informazioni su come eliminare le voci non aggiornate e risolvere il problema, consultare [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) (Da VMware ad Azure con Azure Site Recovery: come eseguire la pulizia delle voci duplicate o non aggiornate).

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria assistenza ulteriore, pubblicare una domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). La community è molto attiva e uno dei tecnici Microsoft potrà fornire l'assistenza richiesta.
