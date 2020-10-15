---
title: Processo del sistema di stato di arresto dell'errore di Windows terminato
description: Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo rileva l'errore di arresto 0xC000021A, che impedisce l'avvio di una macchina virtuale di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b13b61aff819271ed1722572f251f9a6d14b17ab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976998"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Errore di arresto di Windows-processo del sistema di stato 0xC000021A terminato

Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo rileva l'errore di arresto 0xC000021A, che impedisce l'avvio di una macchina virtuale (VM) di Azure.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, nella schermata viene visualizzato il messaggio che indica che il sistema operativo ha rilevato un errore durante l'avvio, con il messaggio seguente:

**Il computer ha riscontrato un problema e deve essere riavviato. Stiamo solo raccogliendo alcune informazioni sull'errore, quindi puoi riavviare. (# #% completato) Per ulteriori informazioni, è possibile eseguire la ricerca online in un secondo momento per questo errore: 0xC000021a**.

  ![Nella figura 1 viene visualizzato il codice di errore #0xC000021A il messaggio "il computer ha riscontrato un problema e deve essere riavviato. Stiamo solo raccogliendo alcune informazioni sull'errore, quindi potrai riavviare ".](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Causa

Errore 0xC000021A indica **STATUS_SYSTEM_PROCESS_TERMINATED**.

Questo errore si verifica quando un processo critico, ad esempio WinLogon (winlogon.exe) o il server client Run-Time sottosistema (csrss.exe) ha esito negativo. Quando il kernel rileva che uno di questi servizi è stato arrestato, genera l'errore **Stop 0xC000021A** . Questo errore può avere diverse cause, tra cui:

- Sono stati installati file di sistema non corrispondenti.
- L'installazione di un Service Pack o di un aggiornamento KB non è riuscita.
- Un programma di backup utilizzato per ripristinare un disco rigido non ha corretto il ripristino dei file che potrebbero essere in uso.
- È stato installato un programma di terze parti incompatibile.

## <a name="solution"></a>Soluzione

### <a name="collect-the-memory-dump-file"></a>Raccogli il file di dump della memoria

Per risolvere questo problema, sarà necessario analizzare il dump di arresto anomalo del sistema. Raccogliere il file di dump della memoria per l'arresto anomalo e contattare il supporto tecnico. Per raccogliere il file di dump, attenersi alla procedura seguente:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1.  Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
2.  Utilizzando **Connessione desktop remoto**, connettersi alla macchina virtuale di ripristino.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1.  Nella macchina virtuale di ripristino andare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di driver assegnata al disco del sistema operativo collegato è F, passare a F:\Windows.
2.  Individuare il file Memory. dmp, quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria.
3.  Se si verificano problemi durante l'individuazione del file Memory. dmp, è possibile usare invece le [chiamate di interrupt non mascherabili (NMI) nella console seriale](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . È possibile seguire la guida per [generare un file di dump di arresto anomalo del sistema usando le chiamate NMI qui](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla risoluzione dei problemi, vedere [risoluzione dei problemi di avvio comuni](./boot-error-troubleshoot.md) o [come risolvere i problemi di una VM Windows connettendo il disco del sistema operativo a una VM di ripristino](./troubleshoot-recovery-disks-windows.md). È inoltre necessario acquisire familiarità con [l'utilizzo della diagnostica di avvio per risolvere i problemi relativi a una macchina virtuale](./boot-diagnostics.md).
- Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Se non è possibile connettersi alla VM, vedere [risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](./troubleshoot-rdp-connection.md).