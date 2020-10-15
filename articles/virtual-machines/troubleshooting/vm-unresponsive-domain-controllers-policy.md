---
title: La macchina virtuale non risponde durante l'applicazione dei criteri dei controller di dominio predefiniti
titlesuffix: Azure Virtual Machines
description: Questo articolo illustra la procedura per risolvere i problemi in cui i criteri predefiniti dei controller di dominio impediscono l'avvio di una macchina virtuale di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 4f181a6dee7ed182150bd0cad2b51690b6c77d7a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963126"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>La macchina virtuale non risponde durante l'applicazione dei criteri dei controller di dominio predefiniti

Questo articolo illustra la procedura per risolvere i problemi in cui i criteri predefiniti dei controller di dominio impediscono l'avvio di una macchina virtuale (VM) di Azure.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che lo screenshot Visualizza il sistema operativo che smette di rispondere durante l'avvio con il **criterio controller di dominio predefinito**del messaggio.

  ![Nella figura 1 viene visualizzato il sistema operativo bloccato con il messaggio "criterio controller di dominio predefinito"](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Causa

Questo problema può essere dovuto a modifiche recenti apportate ai criteri dei controller di dominio predefiniti. In caso contrario, sarà necessario eseguire un'analisi dei file di dump della memoria per determinare la causa principale.

## <a name="solution"></a>Soluzione

Se di recente sono state apportate modifiche ai criteri dei controller di dominio predefiniti, è possibile annullare tali modifiche per risolvere il problema. Se non si è certi di quale sia la causa del problema, raccogliere un dump della memoria e inviare un ticket di supporto.

### <a name="collect-the-memory-dump-file"></a>Raccogli il file di dump della memoria

Per risolvere questo problema, è necessario innanzitutto raccogliere il file di dump della memoria per l'arresto anomalo e quindi contattare il supporto tecnico con il file di dump della memoria. Per raccogliere il file di dump, attenersi alla procedura seguente:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.

1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di unità assegnata al disco del sistema operativo collegato è `F`, è necessario passare a `F:\Windows`.

1. Individuare il file Memory. dmp, quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria.

1. Se si verificano problemi durante l'individuazione del file Memory. dmp, è possibile usare invece le [chiamate di interrupt non mascherabili (NMI) nella console seriale](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Seguire la guida per [generare un file di dump di arresto anomalo](/windows/client-management/generate-kernel-or-complete-crash-dump)del sistema usando le chiamate a NMI.