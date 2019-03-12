---
title: Il test nella convalida dello Stack di Azure come un servizio di verifica di caratteristiche interattive | Microsoft Docs
description: Informazioni su come creare test di verifica della funzionalità interattiva per Azure Stack con convalida di un servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d3db8ea8639f73f3522ddaa358195e7c9ef2f9a9
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766003"
---
# <a name="interactive-feature-verification-testing"></a>Test di verifica di caratteristiche interattive  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

È possibile usare il framework di test di verifica di caratteristiche interattive per richiedere i test per il sistema. Quando si richiede un test, Microsoft Usa il framework per preparare i test che richiedono passaggi manuali interattivi. Microsoft è possibile usare il framework per concatenare più autonomo automatizzata test.

Questo articolo descrive uno scenario semplice manuale. Il test verifica sostituzione di un disco in Azure Stack. Il framework raccoglie i log di diagnostica a ogni passaggio. È possibile eseguire il debug di problemi come si trovarli. Il framework inoltre consente la condivisione dei log generati da altri strumenti o processi e consente di fornire commenti e suggerimenti sullo scenario.

> [!Important]  
> Questo articolo fa riferimento la procedura per utilizzare l'identificazione del disco. Si tratta semplicemente una dimostrazione, come i risultati raccolti dal flusso di lavoro superamento Test non possono essere utilizzati per la verifica della nuova soluzione.

## <a name="overview-of-interactive-testing"></a>Panoramica di test interattiva

Un test per la sostituzione del disco è uno scenario comune. In questo esempio, il test ha cinque passaggi:

1. Creare una nuova **superamento Test** flusso di lavoro.
2. Selezionare il **Test di identificazione del disco**.
3. Completare il passaggio manuale quando richiesto.
4. Controllare il risultato dello scenario.
5. Invia il risultato del test per Microsoft.

## <a name="create-a-new-test-pass"></a>Creare un nuovo test

Se non è un test esistente passare disponibili, seguire le istruzioni per il [pianificazione di un test](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Pianificare il test

1. Selezionare **Test di identificazione del disco**.

    > [!Note]  
    > La versione del test verrà incrementa man mano che vengono apportati miglioramenti per il materiale di test. A meno che non Microsoft indica in caso contrario, utilizzare sempre la versione più recente.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Fornire il nome utente amministratore di dominio e la password selezionando **modifica**.

1. Selezionare il test appropriati esecuzione agente/DVM per avviare il test su.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Selezionare **Submit** per avviare il test.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Accedere all'interfaccia utente per il test interattivo dall'agente selezionato nel passaggio precedente.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Seguire le **documentazione** e **convalida** collegamenti per esaminare le istruzioni disponibili nel Microsoft su come eseguire questo scenario.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Selezionare **Avanti**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Seguire le istruzioni per eseguire lo script relativi alle verifiche preliminari.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Al termine dello script relativi alle verifiche preliminari, è possibile eseguire lo scenario manuale (sostituzione del disco) come per il **documentazione** e **convalida** collegamenti dal **informazioni**scheda.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Non chiudere la finestra di dialogo mentre si esegue lo scenario manuale.

1. Quando si è finito eseguendo lo scenario manuale, seguire le istruzioni per eseguire lo script di controllo post.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. Al completamento dello scenario manuale (sostituzione del disco), selezionare **successivo**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Se si chiude la finestra, il test verrà interrotta prima che sia completato.

1. Fornire commenti e suggerimenti per l'esperienza di test. Queste domande consentirà a Microsoft di valutare la qualità di frequenza e rilascio di esito positivo dello scenario.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Collegare i file di log che si desidera inviare a Microsoft.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Accettare l'EULA di invio di commenti e suggerimenti.

1. Selezionare **Submit** per inviare i risultati a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)
