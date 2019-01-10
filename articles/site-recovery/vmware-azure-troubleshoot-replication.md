---
title: Risolvere i problemi di replica per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni sulla risoluzione dei problemi di replica comunemente riscontrati durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 1c37b764b47856d3a369228d3f224f2a464029bb
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790657"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Risolvere i problemi di replica per macchine virtuali VMware e server fisici

Potrebbe essere visualizzato un messaggio di errore specifico durante la protezione delle macchine virtuali VMware o dei server fisici con Azure Site Recovery. Questo articolo descrive alcuni problemi comuni che possono verificarsi durante l'esecuzione della replica di macchine virtuali VMware e server fisici locali in Azure usando [Azure Site Recovery](site-recovery-overview.md).


## <a name="initial-replication-issues"></a>Problemi di replica iniziale

In molti casi gli errori di replica iniziale segnalati al supporto tecnico sono dovuti a problemi di connettività tra il server di origine e il server di elaborazione oppure tra il server di elaborazione e Azure. Nella maggior parte dei casi, è possibile risolvere questi problemi autonomamente seguendo i passaggi elencati di seguito.

### <a name="verify-the-source-machine"></a>Verificare il computer di origine
* Dalla riga di comando del computer del server di origine, usare Telnet per effettuare il ping del server di elaborazione con la porta HTTPS (impostazione predefinita 9443) come illustrato di seguito, per stabilire se sono presenti problemi di connettività di rete o problemi di blocco delle porte del firewall.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Usare Telnet e non usare PING per testare la connettività.  Se Telnet non è installato, seguire la procedura [qui](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) indicata.

Se non è possibile connettersi, consentire la porta in ingresso 9443 nel server di elaborazione e controllare se il problema persiste. Si sono verificati alcuni casi in cui il problema era dovuto al posizionamento del server di elaborazione dietro una rete perimetrale.

* Controllare lo stato del servizio `InMage Scout VX Agent – Sentinel/OutpostStart` se non è in esecuzione e controllare se il problema persiste.   

### <a name="verify-the-process-server"></a>Verificare il server di elaborazione

* **Controllare se il server di elaborazione effettua attivamente il push dei dati in Azure**

Dal computer del server di elaborazione aprire Gestione attività (premere CTRL+MAIUSC-ESC). Passare alla scheda Prestazioni e fare clic sul collegamento 'Apri Monitoraggio risorse'. Da Monitoraggio risorse passare alla scheda Rete. Controllare se cbengine.exe 'Processi con attività rete' sta inviando attivamente volumi elevati di dati (in Mbps).

![Abilitare la replica](./media/vmware-azure-troubleshoot-replication/cbengine.png)

Se non è questo il caso, seguire questa procedura:

* **Controllare se il server di elaborazione è in grado di connettersi al servizio BLOB di Azure**: Selezionare e controllare cbengine.exe per visualizzare le 'Connessioni TCP' e verificare se è disponibile la connettività dal server di elaborazione all'URL del BLOB del servizio di archiviazione di Azure.

![Abilitare la replica](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

Se non è questo il caso, passare a Pannello di controllo > Servizi e controllare se i servizi seguenti sono in esecuzione:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Ri)avviare gli eventuali servizi non in esecuzione e verificare se il problema persiste.

* **Controllare se il server di elaborazione è in grado di connettersi all'indirizzo IP pubblico di Azure tramite la porta 443**

Aprire il file CBEngineCurr.errlog più recente da `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` e cercare: 443 e tentativo di connessione non riuscito.

![Abilitare la replica](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Se sono presenti problemi, dalla riga di comando del server di elaborazione usare Telnet per effettuare il ping dell'indirizzo IP pubblico di Azure (mascherato nell'immagine precedente) indicato nel file CBEngineCurr.currLog per la porta 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Se non si riesce a connettersi, verificare se il problema di accesso è causato dal firewall o da un proxy, come descritto nel passaggio successivo.


* **Controllare che l'accesso non sia bloccato da un firewall basato su indirizzi IP nel server di elaborazione**: Se si usano regole del firewall basate su indirizzi IP nel server, scaricare l'elenco completo degli intervalli IP dei data center di Microsoft Azure da [qui](https://www.microsoft.com/download/details.aspx?id=41653) e aggiungerli alla configurazione del firewall per assicurarsi che consentano le comunicazioni con Azure e la porta HTTPS (443).  Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).

* **Controllare che l'accesso non sia bloccato da un firewall basato su URL nel server di elaborazione**:  Se si usano regole del firewall basate su URL nel server, assicurarsi di aggiungere gli URL seguenti alla configurazione del firewall.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

* **Controllare che le impostazioni del proxy nel server di elaborazione non blocchino l'accesso**.  Se si usa un server proxy, assicurarsi che il nome del server proxy venga risolto dal server DNS.
Per controllare le impostazioni specificate al momento dell'installazione del server di configurazione. Passare alla chiave del Registro di sistema

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Verificare ora che le stesse impostazioni vengano usate dall'agente di Azure Site Recovery per l'invio dei dati.
Cercare Backup di Microsoft Azure

Aprire il servizio e fare clic su Azione > Modifica proprietà. Nella scheda Configurazione proxy dovrebbe essere visualizzato l'indirizzo del proxy, che dovrebbe essere lo stesso indicato nelle impostazioni del Registro di sistema. In caso contrario, modificarlo per impostare lo stesso indirizzo.


* **Controllare che la limitazione della larghezza di banda non sia vincolata nel server di elaborazione**:  Aumentare la larghezza di banda e controllare se il problema persiste.

## <a name="source-machine-to-be-protected-through-site-recovery-is-not-listed-on-azure-portal"></a>La macchina di origine da proteggere con Site Recovery non è elencata nel portale di Azure

Quando si tenta di scegliere la macchina di origine per cui abilitare la replica tramite Azure Site Recovery, è possibile che la macchina non sia disponibile per i motivi seguenti:

* In vCenter sono presenti due macchine virtuali con lo stesso UUID di istanza e nel portale viene visualizzata solo la prima macchina virtuale individuata dal server di configurazione. Per risolvere questo problema, verificare che uno stesso UUID di istanza non sia assegnato a due macchine virtuali.
* Assicurarsi di aver aggiunto le credenziali di vCenter corrette durante la configurazione tramite il modello OVF o l'installazione unificata. Per verificare le credenziali aggiunte, fare riferimento alle linee guida condivise [qui](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* Se alle autorizzazioni fornite per accedere a vCenter non sono associati privilegi sufficienti, è possibile che l'individuazione delle macchine virtuali non riesca. Verificare quindi che all'account utente di vCenter siano state aggiunte le autorizzazioni specificate [qui](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery).
* Se la macchina virtuale è già protetta tramite Site Recovery, non risulterà disponibile per la protezione. Assicurarsi quindi che la macchina virtuale che si sta cercando nel portale non sia già protetta da un altro utente o con altre sottoscrizioni.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Le macchine virtuali protette risultano disattivate nel portale

Le macchine virtuali replicate in Site Recovery sono disattivate se nel sistema sono presenti voci duplicate. Fare riferimento alle linee guida condivise [qui](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) per eliminare le voci non aggiornate e risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi
Se è necessaria ulteriore assistenza, pubblicare la domanda nel [forum per Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). La community è molto attiva e uno dei tecnici Microsoft sarà in grado di offrire il supporto richiesto.
