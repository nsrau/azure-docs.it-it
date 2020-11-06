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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424230"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Errore di arresto di Windows: eccezione del thread di sistema 0x0000007E non gestita

Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo guest incontra un problema e vuole riavviare la macchina virtuale di Azure. Il messaggio dimostrerà che "un'eccezione del thread di sistema non è stata gestita".

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che lo screenshot Visualizza le finestre che devono essere riavviate con l'eccezione del thread di sistema Stop Code **non gestita** o il codice di errore **0x0000007E**.

![Lo screenshot mostra che Windows è bloccato durante l'avvio con il messaggio: "il PC ha avuto un problema e deve essere riavviato. Verrà riavviato per l'utente ". Interrompi codice: "eccezione THREAD di sistema non gestita"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Causa

Non è possibile determinare la cause fino a quando non viene analizzato un file di dump della memoria. Continuare a raccogliere il file di dump della memoria.

## <a name="solution"></a>Soluzione

### <a name="collect-the-memory-dump-file"></a>Raccogli il file di dump della memoria

Per risolvere il problema, è necessario innanzitutto raccogliere il file di dump della memoria per l'arresto anomalo e quindi contattare il supporto tecnico con il file di dump della memoria. Per raccogliere il file di dump, attenersi alla procedura seguente:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Seguire [i passaggi 1-3 dei comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
2. Connettersi alla macchina virtuale di ripristino usando Connessione Desktop remoto.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di driver assegnata al disco del sistema operativo collegato è denominata *F* , è necessario passare a `F:\Windows` .
2. Individuare il file **Memory. dmp** , quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria.
3. Se si verificano problemi durante l'individuazione del file **Memory. dmp** , seguire la guida per [generare un file di dump di arresto anomalo del sistema usando chiamate di interrupt non mascherabili (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Per ulteriori informazioni sulle chiamate a NMI, vedere la guida dell'utente per le [chiamate di interrupt non mascherabili (NMI) nella console seriale](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Risolvere gli errori di avvio della macchina virtuale di Azure](./boot-error-troubleshoot.md)