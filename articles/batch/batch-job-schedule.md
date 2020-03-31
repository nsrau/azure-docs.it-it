---
title: Pianifica i tuoi lavori
description: Utilizzare la pianificazione dei processi per gestire le attività.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672433"
---
# <a name="schedule-jobs-for-efficiency"></a>Pianificare i processi per l'efficienza

La pianificazione dei processi batch consente di assegnare priorità ai processi che si desidera eseguire per primi tenendo conto delle attività con dipendenze da altre attività. Pianificando i processi, è possibile assicurarsi di utilizzare la quantità minima di risorse. I nodi possono essere rimossi quando non sono necessari, le attività che dipendono da altre attività vengono rimosse nel tempo ottimizzando i flussi di lavoro. Viene eseguito un solo processo alla volta. Uno nuovo non inizierà fino al completamento di quello precedente. È possibile impostare il processo su completamento automatico. 

## <a name="benefit-of-job-scheduling"></a>Vantaggio della programmazione dei processi

Il vantaggio della programmazione dei processi consiste nel specificare una programmazione per la creazione di processi. Le attività pianificate tramite l'attività di gestione processi sono associate a un processo. L'attività di gestione dei processi creerà attività per il processo. A tale scopo, l'attività di gestione dei processi deve eseguire l'autenticazione con l'account Batch. Utilizzare il token di accesso AZ_BATCH_AUTHENTICATION_TOKEN. Il token consentirà l'accesso al resto del processo. 

## <a name="use-the-portal-to-schedule-a-job"></a>Utilizzare il portale per pianificare un processo

   1. Accedere al portale di [Azure](https://portal.azure.com/).

   2. Selezionare l'account Batch in cui si desidera programmare i processi.

   3. Selezionare **Aggiungi** per creare una nuova pianificazione del processo e completare il **modulo Base.**



![Pianificare un processo][1]

**ID pianificazione processo:** identificatore univoco per questa pianificazione del processo.

**Nome visualizzato**: Il nome visualizzato per il lavoro non deve essere univoco ma ha una lunghezza massima di 1024 caratteri.

**Non eseguire fino a :** specifica la prima esecuzione del processo. Se non si imposta questa impostazione, la pianificazione diventa pronta per l'esecuzione immediata dei processi.

**Non eseguire dopo:** nessun processo viene eseguito dopo il tempo impostato qui. Se non si specifica un'ora, si sta creando una pianificazione di processi ricorrente che rimane attiva fino a quando non viene terminata in modo esplicito.

**Intervallo di ricorrenza**: È possibile specificare la quantità di tempo tra i processi. È possibile avere un solo processo a un'ora pianificata, pertanto se è il momento di creare un nuovo processo in una pianificazione del processo, ma il processo precedente è ancora in esecuzione, il servizio Batch non creerà il nuovo processo fino al completamento del processo precedente.  

**Finestra di avvio**: Qui è possibile specificare l'intervallo di tempo, a partire dal momento in cui la pianificazione indica che un processo deve essere creato, fino al completamento. Se il processo corrente non viene completato durante la relativa finestra, il processo successivo non verrà avviato.

Nella parte inferiore del modulo di base verrà specificato il pool in cui si desidera eseguire il processo. Per trovare le informazioni sull'ID del pool, selezionare **Aggiorna**. 

![Specificare il pool][2]


**ID pool**: Identificare il pool in cui verrà eseguito il processo.

**Attività di configurazione processo:** selezionare **Aggiorna** per assegnare un nome all'attività di Gestione processi, nonché le attività Preparazione processo e rilascio, se in uso.

**Priorità**: Assegnare priorità al lavoro.

**Tempo massimo**di clock muro : Impostare la quantità massima di tempo per cui il processo può essere eseguito. Se non viene completato entro l'intervallo di tempo, Batch termina il processo. Se non si imposta questa impostazione, non vi è alcun limite di tempo per il lavoro.

**Numero massimo tentativi attività**: specificare il numero di volte in cui un'attività può essere ritentata fino a un massimo di quattro volte. Questo non corrisponde al numero di tentativi che una chiamata API potrebbe avere.

**Al termine di tutte le attività:** l'impostazione predefinita è nessuna azione.

**Quando un'attività ha esito negativo**: L'impostazione predefinita è nessuna azione. Un'attività ha esito negativo se il numero di tentativi è esaurito o si è verificato un errore durante l'avvio dell'attività. 

Dopo aver selezionato **Salva**, se si passa a **Pianificazioni processi** nel riquadro di spostamento sinistro, è possibile tenere traccia dell'esecuzione del processo selezionando **Informazioni di esecuzione**.


## <a name="for-more-information"></a>Per ulteriori informazioni

Per gestire un processo tramite l'interfaccia della riga di comando di Azure, vedere [az batch job-schedule](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Passaggi successivi

[Creare dipendenze tra attività per eseguire attività che dipendono da altre attività.](batch-task-dependencies.md)





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


