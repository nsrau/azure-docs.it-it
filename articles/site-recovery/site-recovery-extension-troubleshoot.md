---
title: Risolvere i problemi relativi all'agente di Azure Site Recovery | Microsoft Docs
description: Questo articolo offre informazioni su sintomi, cause e soluzioni degli errori dell'agente di Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5ea701682c03370cea46f9126ecf78427a776371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61280672"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Risolvere i problemi relativi all'agente di Azure Site Recovery

Questo articolo illustra le procedure di risoluzione dei problemi che possono essere utili per risolvere gli errori di Azure Site Recovery correlati all'agente di macchine virtuali e all'estensione.


## <a name="azure-site-recovery-extension-time-out"></a>Timeout dell'estensione di Azure Site Recovery  

Messaggio di errore: "Task execution has timed out while tracking for extension operation to be started" (Timeout dell'esecuzione dell'attività in attesa dell'avvio dell'operazione di estensione)<br>
Codice errore: "151076"

 Azure Site Recovery installa un'estensione nella macchina virtuale come parte del processo di abilitazione della protezione. Una delle condizioni seguenti potrebbe impedire l'attivazione della protezione e causare l'esito negativo del processo. Completare questa procedura di risoluzione dei problemi e provare a eseguire di nuovo l'operazione:

**Causa 1: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [Non è possibile aggiornare o caricare l'estensione di Site Recovery](#the-site-recovery-extension-fails-to-update-or-load)**  

Messaggio di errore: "Previous site recovery extension operation is taking more time than expected" (L'operazione di estensione di Site Recovery precedente sta impiegando più tempo del previsto).<br>
Codice errore: "150066"<br>

**Causa 1: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [Lo stato dell'estensione di Site Recovery non è corretto](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>L'abilitazione della protezione non riesce perché l'agente di macchine virtuali non risponde

Messaggio di errore: "Task execution has timed out while tracking for extension operation to be started" (Timeout dell'esecuzione dell'attività in attesa dell'avvio dell'operazione di estensione).<br>
Codice errore: "151099"<br>

Questo errore può verificarsi se l'agente guest di Azure nella macchina virtuale non è nello stato Pronto.
È possibile verificare lo stato dell'agente guest di Azure nel [portale di Azure](https://portal.azure.com/). Passare alla macchina virtuale che si vuole proteggere e verificarne lo stato in "VM > Impostazioni > Proprietà > Stato agente". Nella maggior parte dei casi lo stato dell'agente diventa pronto dopo il riavvio della macchina virtuale. Se però il riavvio non è un'opzione possibile o se il problema persiste, completare la procedura di risoluzione dei problemi seguente.

**Causa 1: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Messaggio di errore: "Task execution has timed out while tracking for extension operation to be started" (Timeout dell'esecuzione dell'attività in attesa dell'avvio dell'operazione di estensione).<br>
Codice errore: "151095"<br>

Questo errore si verifica quando la versione dell'agente nella macchina virtuale Linux è obsoleta. Completare la procedura di risoluzione dei problemi seguente.<br>
  **Causa 1: [L'agente installato nella macchina virtuale non è aggiornato (per macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Cause e soluzioni

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)

#### <a name="solution"></a>Soluzione
L'agente di macchine virtuali può essere danneggiato o il servizio può essere stato arrestato. Reinstallando l'agente di macchine virtuali è possibile ottenere la versione più recente. In questo modo sarà anche possibile riavviare la comunicazione con il servizio.

1. Determinare se il servizio agente guest di Microsoft Azure è in esecuzione nei servizi delle macchine virtuali (services.msc). Provare a riavviare il servizio agente guest di Microsoft Azure.    
2. Se il servizio agente guest di Microsoft Azure non è visibile tra i servizi, nel Pannello di controllo passare a **Programmi e funzionalità** per controllare se è installato.
4. Se il servizio agente guest di Microsoft Azure è elencato in **Programmi e funzionalità**, disinstallarlo.
5. Scaricare e installare la [versione più recente del file MSI dell'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari i diritti di amministratore.
6. Verificare che il servizio agente guest di Microsoft Azure sia visualizzato tra i servizi.
7. Riavviare il processo di protezione.

Verificare anche che [Microsoft .NET 4.5 sia installato](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nella macchina virtuale. .NET 4.5 è necessario per la comunicazione dell'agente di macchine virtuali con il servizio.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>L'agente installato nella VM Linux non è aggiornato (per VM Linux)

#### <a name="solution"></a>Soluzione
La maggior parte degli errori relativi ad agenti o estensioni nelle macchine virtuali Linux è dovuta a problemi correlati ad agenti di macchine virtuali non aggiornati. Per risolvere il problema, seguire queste indicazioni generali:

1. Seguire le istruzioni per l'[aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > È *fortemente consigliato* aggiornare l'agente solo tramite un repository di distribuzione. Non è consigliabile scaricare il codice dell'agente direttamente da GitHub e aggiornarlo. Se l'agente più recente per la distribuzione non è disponibile, contattare il supporto per la distribuzione per istruzioni su come installarlo. Per cercare l'agente più recente, passare alla pagina dell'[agente Linux di Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) nel repository GitHub.

2. Assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale eseguendo il comando seguente: `ps -e`

   Se il processo non è in esecuzione, riavviarlo usando i comandi seguenti:

   * Per Ubuntu: `service walinuxagent start`
   * Per altre distribuzioni: `service waagent start`

3. [Configurare l'agente per il riavvio automatico](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Abilitare la protezione della macchina virtuale.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Non è possibile aggiornare o caricare l'estensione di Site Recovery
Se lo stato dell'estensione è "Vuoto", "Non pronto" o "Transizione in corso".

#### <a name="solution"></a>Soluzione

Disinstallare l'estensione e ripetere l'operazione.

Per disinstallare l'estensione:

1. Nel [portale di Azure](https://portal.azure.com/) passare alla macchina virtuale in cui si è verificato l'errore di backup.
2. Selezionare **Impostazioni**.
3. Selezionare **Estensioni**.
4. Selezionare **Estensione Site Recovery**.
5. Selezionare **Disinstalla**.

Per le macchine virtuali Linux, se l'estensione VMSnapshot non è visualizzata nel portale di Azure, [aggiornare l'agente Linux di Azure](../virtual-machines/linux/update-agent.md) e quindi eseguire il processo di protezione. 

Con il completamento di questa procedura, l'estensione viene reinstallata durante il processo di protezione.


