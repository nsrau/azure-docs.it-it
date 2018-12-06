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
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: dc0c6a37e11574cef857a7af773a9d90bea57357
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972066"
---
# <a name="interactive-feature-verification-testing"></a>Test di verifica di caratteristiche interattive  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

È possibile usare il framework di test di verifica di caratteristiche interattive per richiedere i test per il sistema. Quando si richiede un test, Microsoft Usa il framework per preparare i test che richiedono passaggi manuali interattivi. Microsoft è possibile usare il framework per concatenare più autonomo automatizzata test.

Questo articolo descrive uno scenario semplice manuale. Il test verifica sostituzione di un disco in Azure Stack. Il framework raccoglie i log di diagnostica a ogni passaggio. È possibile eseguire il debug di problemi come si trovarli. Il framework inoltre consente la condivisione dei log generati da altri strumenti o processi e consente di fornire commenti e suggerimenti sullo scenario.

## <a name="overview-of-a-test-pass"></a>Panoramica dei passi del test

Un test per la sostituzione del disco è uno scenario comune. In questo esempio, il test ha sette passaggi:

1.  Creare una nuova **superamento Test** flusso di lavoro.
2.  Selezionare il **Test di identificazione del disco**.
3.  Avviare il test.
4.  Scegliere le azioni nello scenario verifica interattiva.
5.  Controllare il risultato dello scenario.
6.  Invia il risultato del test per Microsoft.
7.  Controllare lo stato nel portale di VaaS.

## <a name="create-a-new-test-pass"></a>Creare un nuovo test

1.  Passare il [portale di Azure Stack convalida](https://www.azurestackvalidation.com) ed eseguire l'accesso.

2.  Creare una nuova soluzione o sceglierne uno esistente.

3.  Selezionare **avviare** nel **superamento Test** riquadro.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  Immettere un nome per il **superamento Test** flusso di lavoro.

5.  Immettere l'ambiente e i parametri comuni di test, seguire le istruzioni riportate nel [parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio](azure-stack-vaas-parameters.md) articolo.

6.  La stringa di connessione di diagnostica deve seguire il formato specificato nella [parametri di Test](azure-stack-vaas-parameters.md#test-parameters) sezione il [parametri comuni del flusso di lavoro](azure-stack-vaas-parameters.md) articolo.

7.  Immettere i parametri necessari per il test.

8.  Selezionare l'agente per eseguire l'esecuzione dei test interattiva.

> [!Note]  
> Per test di verifica caratteristiche interattive di identificazione del disco è necessario specificare la password e utente amministratore di dominio.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>Selezionare il test

1.  Selezionare **Test di identificazione del disco\<versione >**.

    > [!Note]  
    > La versione del test verrà incrementa man mano che vengono apportati miglioramenti per il materiale di test. A meno che non Microsoft indica in caso contrario, utilizzare sempre la versione più recente.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  Specificare l'utente amministratore di dominio e una password selezionando **modifica**.

3.  Selezionare il test appropriati esecuzione agente/DVM per avviare il test su.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  Selezionare **Submit** per avviare il test.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>Avviare il test

Le istruzioni di prova di identificazione del disco Visualizza nel computer che esegue l'agente VaaS. In genere si tratta di DVM o Jumpbox per l'istanza di Azure Stack.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>Scegliere le azioni

1.  Seguire le **documentazione** e **convalida** collegamenti per esaminare le istruzioni disponibili nel Microsoft su come eseguire questo scenario.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  Selezionare **Avanti**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Seguire le istruzioni per eseguire lo script relativi alle verifiche preliminari.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  Dopo aver completato lo script relativi alle verifiche preliminari viene completato correttamente, eseguire lo scenario manuale (sostituzione del disco) in base il **documentazione** e **convalida** si collega il **informazioni**scheda.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  Non chiudere la finestra di dialogo mentre si esegue lo scenario manuale.

6.  Quando si è finito eseguendo lo scenario manuale, seguire le istruzioni per eseguire lo script di controllo post.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  Al completamento dello scenario manuale (sostituzione del disco), selezionare **successivo**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> Se si chiude la finestra, il test verrà interrotta prima che sia completato.

## <a name="check-the-status"></a>Controllare lo stato

1.  Una volta completato il test, verrà richiesto di fornire commenti e suggerimenti.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  Queste domande consentirà a Microsoft di valutare la qualità di frequenza e rilascio di esito positivo dello scenario.

## <a name="send-the-test-result"></a>Invia il risultato del test

1.  Collegare i file di log che si desidera inviare a Microsoft.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  Accettare l'EULA di invio di commenti e suggerimenti.

3.  Selezionare **Submit** per inviare i risultati a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i test nel portale di VaaS](azure-stack-vaas-monitor-test.md)
