---
title: Risolvere i problemi relativi all'estensione VM di Azure per il ripristino di emergenza con Azure Site Recovery
description: Risolvere i problemi relativi all'estensione della macchina virtuale di Azure per il ripristino di emergenza con Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184619"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Risolvere i problemi relativi all'estensione della macchina virtuale di Azure

Questo articolo illustra i passaggi per la risoluzione dei problemi che consentono di risolvere Azure Site Recovery errori correlati all'agente di macchine virtuali e all'estensione.

## <a name="low-system-resources"></a>Risorse di sistema insufficienti

Questo problema si verifica quando la memoria disponibile nel sistema non è sufficiente e non è in grado di allocare memoria per l'installazione del servizio Mobility. Verificare che sia stata liberata una quantità di memoria sufficiente perché l'installazione continui e venga completata correttamente.

## <a name="azure-site-recovery-extension-time-out"></a>Timeout dell'estensione Azure Site Recovery  

Messaggio di errore: "Task execution has timed out while tracking for extension operation to be started" (Timeout dell'esecuzione dell'attività in attesa dell'avvio dell'operazione di estensione)<br>
Codice errore: "151076"

 Azure Site Recovery installata un'estensione nella macchina virtuale come parte di un processo di abilitazione della protezione. Una delle condizioni seguenti potrebbe impedire l'attivazione della protezione e causare l'esito negativo del processo. Completare questa procedura di risoluzione dei problemi e provare a eseguire di nuovo l'operazione:

- [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [L'agente installato nella VM Linux non è aggiornato (per VM Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Non è possibile aggiornare o caricare l'estensione di Site Recovery](#the-site-recovery-extension-fails-to-update-or-load)

Messaggio di errore: "l'operazione di estensione Site Recovery precedente sta impiegando più tempo del previsto".<br>
Codice errore: "150066"

- [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [L'agente installato nella VM Linux non è aggiornato (per VM Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Lo stato dell'estensione di Site Recovery non è corretto](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>L'abilitazione della protezione non riesce perché l'agente di macchine virtuali non risponde

Messaggio di errore: "Task execution has timed out while tracking for extension operation to be started" (Timeout dell'esecuzione dell'attività in attesa dell'avvio dell'operazione di estensione).<br>
Codice errore: "151099"

Questo errore può verificarsi se l'agente guest di Azure nella macchina virtuale non è nello stato pronto.

È possibile controllare lo stato dell'agente guest di Azure nella [portale di Azure](https://portal.azure.com/). Passare alla macchina virtuale che si sta tentando di proteggere e controllare lo stato **VM**in  >  **Impostazioni**VM  >  **Proprietà**  >  **stato agente**. Nella maggior parte dei casi, lo stato dell'agente è pronto dopo il riavvio della macchina virtuale. Tuttavia, se non è possibile riavviare il computer o se il problema persiste, completare i passaggi seguenti per la risoluzione dei problemi:

- [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [L'agente installato nella VM Linux non è aggiornato (per VM Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Messaggio di errore: "Task execution has timed out while tracking for extension operation to be started" (Timeout dell'esecuzione dell'attività in attesa dell'avvio dell'operazione di estensione).<br>
Codice errore: "151095"

Questo errore si verifica quando la versione dell'agente nel computer Linux non è aggiornata. Completare il passaggio seguente per la risoluzione dei problemi:

- [L'agente installato nella VM Linux non è aggiornato (per VM Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Cause e soluzioni

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)

#### <a name="solution"></a>Soluzione
L'agente di macchine virtuali può essere danneggiato o il servizio può essere stato arrestato. Reinstallando l'agente di macchine virtuali è possibile ottenere la versione più recente. In questo modo sarà anche possibile riavviare la comunicazione con il servizio.

1. Determinare se il servizio agente guest di Microsoft Azure è in esecuzione nei servizi delle macchine virtuali (services.msc). Riavviare il servizio agente guest di Windows Azure.    
1. Se il servizio agente guest di Windows Azure non è visibile nei servizi, aprire il pannello di controllo. Passare a **programmi e funzionalità** per verificare se il servizio agente guest di Windows è installato.
1. Se il servizio agente guest di Microsoft Azure è elencato in **Programmi e funzionalità**, disinstallarlo.
1. Scaricare e installare la [versione più recente del file MSI dell'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari i diritti di amministratore.
1. Verificare che il servizio agente guest di Microsoft Azure venga visualizzato in servizi.
1. Riavviare il processo di protezione.

Verificare anche che [Microsoft .NET 4.5 sia installato](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nella macchina virtuale. Per comunicare con il servizio, è necessario .NET 4,5 per l'agente di macchine virtuali.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>L'agente installato nella VM Linux non è aggiornato (per VM Linux)

#### <a name="solution"></a>Soluzione
La maggior parte degli errori relativi ad agenti o estensioni nelle macchine virtuali Linux è dovuta a problemi correlati ad agenti di macchine virtuali non aggiornati. Per risolvere il problema, seguire queste indicazioni generali:

1. Seguire le istruzioni per l'[aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > È *fortemente consigliato* aggiornare l'agente solo tramite un repository di distribuzione. Non è consigliabile scaricare il codice dell'agente direttamente da GitHub e aggiornarlo. Se l'agente più recente per la distribuzione non è disponibile, contattare il supporto per la distribuzione per istruzioni su come installarlo. Per cercare l'agente più recente, passare alla pagina dell'[agente Linux di Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) nel repository GitHub.

1. Assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale eseguendo il comando seguente: `ps -e`

   Se il processo non è in esecuzione, riavviarlo usando i comandi seguenti:

   - Per Ubuntu: `service walinuxagent start`
   - Per altre distribuzioni: `service waagent start`

1. [Configurare l'agente di riavvio automatico](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Abilitare la protezione della macchina virtuale.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Non è possibile aggiornare o caricare l'estensione di Site Recovery

Lo stato dell'estensione è "Empty", "" nobattistrada "o" transitioning ".

#### <a name="solution"></a>Soluzione

Disinstallare l'estensione e ripetere l'operazione.

Per disinstallare l'estensione:

1. Nella [portale di Azure](https://portal.azure.com/)passare alla macchina virtuale in cui si è verificato un errore di backup.
1. Selezionare **Impostazioni**.
1. Selezionare **Estensioni**.
1. Selezionare **Estensione Site Recovery**.
1. Selezionare **Disinstalla**.

Per la VM Linux, se l'estensione VMSnapshot non viene visualizzata nel portale di Azure, [aggiornare l'agente Linux di Azure](../virtual-machines/extensions/update-linux-agent.md). Eseguire quindi la protezione.

Quando si completano questi passaggi, l'estensione viene reinstallata durante la protezione.
