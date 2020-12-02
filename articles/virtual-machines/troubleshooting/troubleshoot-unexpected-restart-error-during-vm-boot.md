---
title: 'Avvio del sistema operativo: il computer è stato riavviato in modo imprevisto o ha rilevato un errore imprevisto'
description: Questo articolo illustra la procedura per risolvere i problemi in cui si verifica un errore o un riavvio imprevisto della macchina virtuale durante l'installazione di Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: cfeb040893ae2be5842959ed8458bd713bebe6ee
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512138"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Avvio del sistema operativo: il computer è stato riavviato in modo imprevisto o ha rilevato un errore imprevisto

Questo articolo illustra i passaggi per risolvere i problemi in cui si verifica un errore o un riavvio imprevisto della macchina virtuale durante l'installazione di Windows.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che la schermata Visualizza l'installazione di Windows non riuscita con l'errore seguente:

**Il computer è stato riavviato in modo imprevisto o ha rilevato un errore imprevisto. Impossibile continuare l'installazione di Windows. Per installare Windows, fare clic su "OK" per riavviare il computer, quindi riavviare l'installazione.**

![Errore durante l'installazione di Windows. il computer è stato riavviato in modo imprevisto o si è verificato un errore imprevisto. Impossibile continuare l'installazione di Windows. Per installare Windows, fare clic su "OK" per riavviare il computer, quindi riavviare l'installazione.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Errore durante l'avvio del programma di installazione di Windows: il computer è stato riavviato in modo imprevisto o si è verificato un errore imprevisto. Impossibile continuare l'installazione di Windows. Per installare Windows, fare clic su "OK" per riavviare il computer, quindi riavviare l'installazione.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Causa

Il computer sta provando a eseguire un avvio iniziale di un' [immagine generalizzata](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), ma incontra problemi a causa di un file di risposte personalizzato (Unattend.xml) in fase di elaborazione. **I file di risposte personalizzati non sono supportati in Azure**. 

Il file di risposte è costituito da un file XML speciale che contiene le definizioni e i valori delle impostazioni di configurazione che si desidera automatizzare durante l'installazione di un'installazione del sistema operativo Windows Server. Le opzioni di configurazione includono le istruzioni su come partizionare i dischi, dove trovare l'immagine di Windows da installare, i codici Product Key da applicare e gli altri comandi che si desidera eseguire.

Anche in questo caso, i file di risposte personalizzati non sono supportati in Azure. Questa situazione si verifica quindi quando un'immagine è stata preparata per l'uso in Azure, ma è stato specificato un file di Unattend.xml personalizzato usando **Sysprep** con un flag simile al seguente comando:

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

In Azure, usare l'opzione di **immissione del sistema (configurazione guidata)** nell' **interfaccia utente grafica di System Preparation Tool** oppure usare `sysprep /oobe` anziché il file di Unattend.xml.

Questo problema viene spesso creato durante l'uso di Sysprep con una macchina virtuale locale per caricare una macchina virtuale generalizzata in Azure. In questa situazione potrebbe essere interessante anche il modo in cui caricare correttamente una macchina virtuale generalizzata.

## <a name="solution"></a>Soluzione

### <a name="do-not-use-unattendxml"></a>Non usare Unattend.xml

Per risolvere questo problema, seguire [le istruzioni di Azure per preparare/acquisire un'immagine](../windows/upload-generalized-managed.md) e preparare una nuova immagine generalizzata. Durante Sysprep, **non usare `/unattend:<your file’s name>` flag**. Usare invece solo i flag seguenti:

`sysprep /oobe /generalize /shutdown`

- La configurazione guidata è l'impostazione supportata per le macchine virtuali di Azure.

È anche possibile usare l' **interfaccia utente grafica dell'utilità preparazione sistema** per eseguire la stessa attività del comando precedente selezionando le opzioni riportate di seguito:

- Immettere l'esperienza predefinita
- Generalizzazione
- Shutdown
 
![Finestra degli strumenti di preparazione del sistema con opzioni OOBE, generalizzate e di arresto selezionate.](./media/unexpected-restart-error-during-vm-boot/3.png)
