---
title: Risoluzione dei problemi di protezione per VMware/server fisici in Azure | Microsoft Docs
description: Questo articolo descrive i problemi comuni di replica delle macchine virtuali VMware e come risolverli
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/26/2017
ms.author: asgang
ms.openlocfilehash: 6ebec2e06566b1e2d6834fdd81c0d8b2801b80b9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-on-premises-vmwarephysical-server-replication-issues"></a>Risolvere i problemi di replica per macchine virtuali VMware/server fisici locali
Potrebbe essere visualizzato un messaggio di errore specifico durante la protezione delle macchine virtuali VMware o dei server fisici con Azure Site Recovery. Questo articolo illustra nei dettagli alcuni dei più comuni messaggi di errore visualizzati e spiega le procedure per la risoluzione dei problemi relativi a tali messaggi.


## <a name="initial-replication-is-stuck-at-0"></a>Replica iniziale bloccata allo 0%
La maggior parte degli errori di replica iniziale segnalata al supporto tecnico è dovuta a problemi di connettività tra il server di origine e il server di elaborazione oppure tra il server di elaborazione e Azure.
Nella maggior parte dei casi, è possibile risolvere questi problemi autonomamente seguendo i passaggi elencati di seguito.

###<a name="check-the-following-on-source-machine"></a>Controllare quanto segue nel COMPUTER DI ORIGINE
* Dalla riga di comando del computer del server di origine, usare Telnet per effettuare il ping del server di elaborazione con la porta HTTPS (impostazione predefinita 9443) come illustrato di seguito, per stabilire se sono presenti problemi di connettività di rete o problemi di blocco delle porte del firewall.
     
    `telnet <PS IP address> <port>`
> [!NOTE]
    > Usare Telnet e non usare PING per testare la connettività.  Se Telnet non è installato, seguire la procedura [qui](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) indicata.

Se non è possibile connettersi, consentire la porta in ingresso 9443 nel server di elaborazione e controllare se il problema persiste. Si sono verificati alcuni casi in cui il problema era dovuto al posizionamento del server di elaborazione dietro una rete perimetrale.

* Controllare lo stato del servizio `InMage Scout VX Agent – Sentinel/OutpostStart` se non è in esecuzione e controllare se il problema persiste.   
 
###<a name="check-the-following-on-process-server"></a>Controllare quanto segue nel SERVER DI ELABORAZIONE

* **Controllare se il server di elaborazione effettua attivamente il push dei dati in Azure** 

Dal computer del server di elaborazione aprire Gestione attività (premere CTRL+MAIUSC-ESC). Passare alla scheda Prestazioni e fare clic sul collegamento 'Apri Monitoraggio risorse'. Da Monitoraggio risorse passare alla scheda Rete. Controllare se cbengine.exe 'Processi con attività rete' sta inviando attivamente volumi elevati di dati (in Mbps).

![Abilitare la replica](./media/site-recovery-protection-common-errors/cbengine.png)

Se non è questo il caso, seguire questa procedura:

* **Controllare se il server di elaborazione è in grado di connettersi al servizio BLOB di Azure**: selezionare e controllare cbengine.exe per visualizzare le 'Connessioni TCP' e verificare se è disponibile la connettività dal server di elaborazione all'URL del BLOB del servizio di archiviazione di Azure.

![Abilitare la replica](./media/site-recovery-protection-common-errors/rmonitor.png)

Se non è questo il caso, passare a Pannello di controllo > Servizi e controllare se i servizi seguenti sono in esecuzione:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     * 
(Ri)avviare gli eventuali servizi non in esecuzione e verificare se il problema persiste.

* **Controllare se il server di elaborazione è in grado di connettersi all'indirizzo IP pubblico di Azure tramite la porta 443**

Aprire il più recente CBEngineCurr.errlog da `%programfiles%\Microsoft Azure Recovery Services Agent\Temp`, quindi cercare :443 e un messaggio di errore che indica un tentativo di connessione non riuscito.

![Abilitare la replica](./media/site-recovery-protection-common-errors/logdetails1.png)

Se sono presenti problemi, dalla riga di comando del server di elaborazione usare Telnet per effettuare il ping dell'indirizzo IP pubblico di Azure (mascherato nell'immagine precedente) indicato nel file CBEngineCurr.currLog per la porta 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Se non si riesce a connettersi, verificare se il problema di accesso è causato dal firewall o da un proxy, come descritto nel passaggio successivo.


* **Controllare che l'accesso non sia bloccato da un firewall basato su indirizzi IP nel server di elaborazione**: se si usano regole del firewall basate su indirizzi IP nel server, scaricare l'elenco completo degli intervalli IP dei data center di Microsoft Azure da [qui](https://www.microsoft.com/download/details.aspx?id=41653) e aggiungerli alla configurazione del firewall per assicurarsi che consentano le comunicazioni con Azure e la porta HTTPS (443).  Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).

* **Controllare che l'accesso non sia bloccato da un firewall basato su URL nel server di elaborazione**: se si usano regole del firewall basate su URL nel server, assicurarsi di aggiungere gli URL seguenti alla configurazione del firewall. 
     
  `*.accesscontrol.windows.net:`, usato per il controllo di accesso e la gestione delle identità.

  `*.backup.windowsazure.com:`, usato per l'orchestrazione e il trasferimento dei dati di replica.

  `*.blob.core.windows.net:`, usato per l'accesso all'account di archiviazione in cui sono archiviati i dati replicati.

  `*.hypervrecoverymanager.windowsazure.com:`, usato per l'orchestrazione e le operazioni di gestione della replica.

  `time.nist.gov` e `time.windows.com`, usati per controllare la sincronizzazione tra ora di sistema e ora globale.

URL per il **cloud di Azure per enti pubblici**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us` 

* **Controllare che le impostazioni del proxy nel server di elaborazione non blocchino l'accesso**.  Se si usa un server proxy, assicurarsi che il nome del server proxy venga risolto dal server DNS.
Per controllare le impostazioni specificate al momento dell'installazione del server di configurazione. Passare alla chiave del Registro di sistema

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Verificare ora che le stesse impostazioni vengano usate dall'agente di Azure Site Recovery per l'invio dei dati.
Cercare Backup di Microsoft Azure 

![Abilitare la replica](./media/site-recovery-protection-common-errors/mab.png)

Aprire il servizio e fare clic su Azione > Modifica proprietà. Nella scheda Configurazione proxy dovrebbe essere visualizzato l'indirizzo del proxy, che dovrebbe essere lo stesso indicato nelle impostazioni del Registro di sistema. In caso contrario, modificarlo per impostare lo stesso indirizzo.

![Abilitare la replica](./media/site-recovery-protection-common-errors/mabproxy.png)

* **Controllare che la larghezza di banda non sia vincolata nel server di elaborazione**: aumentare la larghezza di banda e controllare se il problema persiste.

##<a name="next-steps"></a>Passaggi successivi
Se è necessaria ulteriore assistenza, pubblicare la domanda nel [forum per ASR](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). La community è molto attiva e uno dei tecnici Microsoft sarà in grado di offrire il supporto richiesto.