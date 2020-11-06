---
title: La macchina virtuale non risponde durante l'attesa del gestore sessioni locale
description: Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo guest è bloccato in attesa del completamento dell'elaborazione da parte di gestione sessioni locali durante l'avvio di una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: ae3d35bea1c75c797447100316ceab9a27fcbe48
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424238"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>La macchina virtuale non risponde durante l'attesa del gestore sessioni locale

Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo guest è bloccato in attesa del completamento dell'elaborazione da parte di gestione sessioni locali durante l'avvio di una macchina virtuale (VM) di Azure.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della VM, si noterà che nella schermata viene visualizzato un messaggio di richiesta con il messaggio: " *attendere la gestione della sessione locale* "

![La schermata mostra il sistema operativo guest bloccato con il messaggio "Attendi il gestore sessioni locale" in Windows Server 2012 R2.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Causa

Potrebbero esserci diversi motivi per cui una macchina virtuale è bloccata in attesa di gestione sessioni locali. Se l'attesa del gestore sessioni locale è un problema persistente, è necessario raccogliere un dump della memoria per l'analisi.

## <a name="solution"></a>Soluzione

In alcuni casi, la semplice attesa di un tempo sufficiente per il completamento del processo consente di risolvere il problema. Se la macchina virtuale è bloccata nella schermata di attesa per più di un'ora, è necessario raccogliere un dump della memoria e quindi contattare il supporto tecnico Microsoft.

### <a name="collect-the-memory-dump-file"></a>Raccogli il file di dump della memoria

Per risolvere il problema, è necessario innanzitutto raccogliere il file di dump della memoria per l'arresto anomalo e quindi contattare il supporto tecnico con il file di dump della memoria. Per raccogliere il file di dump, attenersi alla procedura seguente:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Seguire [i passaggi 1-3 dei comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
2. Connettersi alla macchina virtuale di ripristino usando Connessione Desktop remoto.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di driver assegnata al disco del sistema operativo collegato è denominata *F* , è necessario passare a `F:\Windows` .
2. Individuare il file **Memory. dmp** , quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria.
3. Se si verificano problemi durante l'individuazione del file **Memory. dmp** , seguire la guida per [generare un file di dump di arresto anomalo del sistema usando chiamate di interrupt non mascherabili (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Per ulteriori informazioni sulle chiamate a NMI, vedere la pagina relativa alle [chiamate di interrupt non mascherabili (NMI) nel](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) manuale dell'utente della console seriale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Risolvere gli errori di avvio della macchina virtuale di Azure](boot-error-troubleshoot.md)