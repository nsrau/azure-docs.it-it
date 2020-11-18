---
title: 'Errore di arresto di Windows: eccezione del thread di sistema 0x0000007E non gestita'
description: Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo guest incontra un problema e vuole riavviare la macchina virtuale di Azure. Il messaggio dimostrerà che "un'eccezione del thread di sistema non è stata gestita".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681891"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Errore di arresto di Windows: eccezione del thread di sistema 0x0000007E non gestita

Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo guest rileva un problema e tenta di riavviare la macchina virtuale (VM) di Azure. Il messaggio di errore visualizzato indica che "un'eccezione del thread di sistema non è stata gestita".

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare una schermata dell'output della macchina virtuale, si noterà che Windows deve essere riavviato con il codice di arresto "eccezione thread di sistema non gestita" o il codice di errore "0x0000007E".

![Screenshot che mostra le finestre bloccate durante l'avvio con "il PC ha avuto un problema e deve essere riavviato. Verrà riavviato per l'utente ". messaggio di errore e codice di arresto "eccezione THREAD di sistema non gestita".](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Causa

Non è possibile determinare la cause fino a quando non viene analizzato un file di dump della memoria. Continuare a raccogliere il file di dump della memoria.

## <a name="solution"></a>Soluzione

Per risolvere il problema, è necessario innanzitutto raccogliere il file di dump della memoria per l'arresto anomalo e quindi inviare il file al supporto tecnico Microsoft. Per raccogliere il file di dump, seguire le istruzioni riportate nelle due sezioni successive.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Per preparare una macchina virtuale di ripristino, seguire i passaggi 1-3 dei [comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Connettersi alla macchina virtuale di ripristino usando Connessione Desktop remoto.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino passare alla cartella Windows sul disco del sistema operativo collegato. Ad esempio, se la lettera di unità assegnata al disco del sistema operativo collegato è denominata *F*, passare a `F:\Windows` .
1. Cercare il file *Memory. dmp* , quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria allegato.
1. Se si verificano problemi durante l'individuazione del file *Memory. dmp* , seguire la guida per [generare un file di dump di arresto anomalo del sistema usando chiamate a interrupt non mascherabili (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Per ulteriori informazioni sulle chiamate a NMI, vedere la guida dell'utente sulle [chiamate a NMI in Azure serial console](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Risolvere gli errori di avvio della macchina virtuale di Azure](./boot-error-troubleshoot.md)
