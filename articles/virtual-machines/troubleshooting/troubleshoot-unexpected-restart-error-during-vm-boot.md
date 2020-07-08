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
ms.openlocfilehash: daefaca45adb061295928c64b6a0e328a12d8a3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85268761"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Avvio del sistema operativo: il computer è stato riavviato in modo imprevisto o ha rilevato un errore imprevisto

Questo articolo illustra i passaggi per risolvere i problemi in cui si verifica un errore o un riavvio imprevisto della macchina virtuale durante l'installazione di Windows.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare lo screenshot della macchina virtuale, si noterà che la schermata Visualizza l'installazione di Windows non riuscita con l'errore seguente:

**Il computer è stato riavviato in modo imprevisto o ha rilevato un errore imprevisto. Impossibile continuare l'installazione di Windows. Per installare Windows, fare clic su "OK" per riavviare il computer, quindi riavviare l'installazione.**

![Errore durante l'installazione di Windows. il computer è stato riavviato in modo imprevisto o si è verificato un errore imprevisto. Impossibile continuare l'installazione di Windows. Per installare Windows, fare clic su "OK" per riavviare il computer, quindi riavviare l'installazione.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Errore durante l'avvio del programma di installazione di Windows: il computer è stato riavviato in modo imprevisto o si è verificato un errore imprevisto. Impossibile continuare l'installazione di Windows. Per installare Windows, fare clic su "OK" per riavviare il computer, quindi riavviare l'installazione.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Causa

Il computer sta provando a eseguire un avvio iniziale di un' [immagine generalizzata](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), ma incontra problemi a causa di un file di risposte personalizzato (unattend.xml) in fase di elaborazione. I file di risposte personalizzati non sono supportati in Azure. 

Il file di risposte è costituito da un file XML speciale che contiene le definizioni e i valori delle impostazioni di configurazione che si desidera automatizzare durante l'installazione di un'installazione del sistema operativo Windows Server. Le opzioni di configurazione includono le istruzioni su come partizionare i dischi, dove trovare l'immagine di Windows da installare, i codici Product Key da applicare e gli altri comandi che si desidera eseguire.

In Azure, i file di risposte personalizzati non sono supportati. Se è stato specificato un file di **Unattend.xml** personalizzato utilizzando l' `sysprep /unattend:<your file’s name>` opzione, questo errore può verificarsi.

In Azure, usare l'opzione di immissione di un **sistema (configurazione guidata)** in **Sysprep.exe**o usare `sysprep /oobe` anziché il file di Unattend.xml.

Questo problema viene spesso creato quando si usa **Sysprep.exe** con una macchina virtuale locale per caricare una macchina virtuale generalizzata in Azure. In questa situazione potrebbe essere interessante anche il modo in cui caricare correttamente una macchina virtuale generalizzata.

## <a name="solution"></a>Soluzione

### <a name="replace-unattended-answer-file-option"></a>Opzione Sostituisci file di risposte automatico

Questa situazione si verifica quando un'immagine è stata preparata per l'uso in Azure, ma è stato usato un file di risposte personalizzato, che non è supportato in Azure, ed è stato usato **Sysprep** con un flag simile al seguente comando:

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- Nel comando precedente sostituire `<NameOfYourAnswerFile.XML>` con il nome del file.

Per risolvere questo problema, seguire [le istruzioni di Azure per preparare/acquisire un'immagine](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) e preparare una nuova immagine generalizzata. Durante Sysprep, non usare `/unattend:<answerfile>` flag. Usare invece solo i flag seguenti:

`sysprep /oobe /generalize /shutdown`

- La configurazione guidata è l'impostazione supportata per le macchine **virtuali di Azure** .

È anche possibile usare l' **interfaccia utente grafica dell'utilità preparazione sistema** per eseguire la stessa attività del comando precedente selezionando le opzioni riportate di seguito:

- Immettere l'esperienza predefinita
- Generalizzazione
- Shutdown
 
![Finestra degli strumenti di preparazione del sistema con opzioni OOBE, generalizzate e di arresto selezionate.](./media/unexpected-restart-error-during-vm-boot/3.png)
