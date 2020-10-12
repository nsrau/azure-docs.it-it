---
title: La macchina virtuale di Azure non risponde durante l'applicazione dei criteri di sicurezza al sistema
description: Questo articolo illustra la procedura per risolvere i problemi in cui la schermata di caricamento è bloccata quando la macchina virtuale non risponde durante l'applicazione dei criteri di sicurezza al sistema in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84908067"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>La macchina virtuale di Azure non risponde durante l'applicazione dei criteri di sicurezza al sistema

Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo si blocca e non risponde durante l'applicazione di criteri di sicurezza in una macchina virtuale di Azure.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che lo screenshot Visualizza il sistema operativo bloccato durante l'avvio con il messaggio:

> "Applicazione dei criteri di sicurezza al sistema".

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Screenshot della schermata di avvio di Windows Server 2012 R2 bloccato.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Screenshot della schermata di avvio di Windows Server 2012 R2 bloccato.":::

## <a name="cause"></a>Causa

Questo problema è costituito da numerose cause potenziali. Non sarà possibile conoscerne l'origine fino a quando non viene eseguita un'analisi del dump della memoria.

## <a name="resolution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. [Creare e accedere a una macchina virtuale di ripristino](#create-and-access-a-repair-vm)
2. [Abilitare la raccolta di immagini di memoria e console seriale](#enable-serial-console-and-memory-dump-collection)
3. [Ricreare la macchina virtuale](#rebuild-the-vm)
4. [Raccogli il file di dump della memoria](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Seguire i [passaggi da 1 a 3 dei comandi di ripristino della macchina virtuale](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) per preparare una macchina virtuale di ripristino.
2. Usare Connessione Desktop remoto connettersi alla macchina virtuale di ripristino.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Abilitare la raccolta di immagini di memoria e console seriale

Per abilitare la raccolta di dump della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Elencare i dati dell'archivio BCD e determinare l'identificatore del caricatore di avvio, che verrà usato nel passaggio successivo.

     1. Per una macchina virtuale di prima generazione, immettere il comando seguente e annotare l'identificatore elencato:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        Nel comando sostituire \<BOOT PARTITON> con la lettera della partizione nel disco collegato che contiene la cartella di avvio.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Screenshot della schermata di avvio di Windows Server 2012 R2 bloccato." /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Scaricare il disco del sistema operativo non funzionante:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>Ricreare la macchina virtuale

Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) per riassemblare la macchina virtuale.

### <a name="collect-the-memory-dump-file"></a>Raccogli il file di dump della memoria

Per risolvere il problema, è necessario innanzitutto raccogliere il file di dump della memoria per l'arresto anomalo e contattare il supporto tecnico con il file di dump della memoria. Per raccogliere il file di dump, attenersi alla procedura seguente:

1. Alleghi il disco del sistema operativo a una nuova macchina virtuale di ripristino:

    - Usare [i passaggi 1-3 dei comandi di ripristino della macchina virtuale](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) per preparare una nuova macchina virtuale di ripristino.
    - Usare Connessione Desktop remoto connettersi alla macchina virtuale di ripristino.

2. Individuare il file dump e inviare un ticket di supporto:

    - Nella macchina virtuale di ripristino andare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di unità assegnata al disco del sistema operativo collegato è `F`, è necessario passare a `F:\Windows`.
    - Individuare il file Memory. dmp, quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria.
    - Se si verificano problemi durante l'individuazione del file Memory. dmp, è possibile usare invece le [chiamate di interrupt non mascherabili (NMI) nella console seriale](serial-console-windows.md#use-the-serial-console-for-nmi-calls) . È possibile seguire la guida per [generare un file di dump di arresto anomalo](/windows/client-management/generate-kernel-or-complete-crash-dump)del sistema usando le chiamate a NMI.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi quando si applicano i criteri per utenti e gruppi locali, [la macchina virtuale non risponde quando si applicano criteri di gruppo criteri per utenti e gruppi locali](unresponsive-vm-apply-group-policy.md)