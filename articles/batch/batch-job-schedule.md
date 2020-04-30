---
title: Pianificare i processi
description: Usare la pianificazione dei processi per gestire le attività.
ms.topic: article
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 49b2064d38f9f646c6189d859479d2414569ff60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116877"
---
# <a name="schedule-jobs-for-efficiency"></a>Pianificare i processi per l'efficienza

La pianificazione dei processi batch consente di assegnare una priorità ai processi che si desidera eseguire per primi tenendo conto delle attività che presentano dipendenze da altre attività. Pianificando i processi, è possibile assicurarsi di usare la quantità minima di risorse. È possibile rimuovere le autorizzazioni dei nodi quando non è necessario, le attività che dipendono da altre attività vengono riattivate solo nel tempo, ottimizzando i flussi di lavoro. Viene eseguito un solo processo alla volta. Una nuova non viene avviata fino a quando non viene completata quella precedente. È possibile impostare il processo per il completamento automatico. 

## <a name="benefit-of-job-scheduling"></a>Vantaggi della pianificazione dei processi

Il vantaggio della pianificazione dei processi è che è possibile specificare una pianificazione per la creazione di processi. Le attività pianificate mediante l'attività del gestore di processi sono associate a un processo. L'attività del gestore di processi creerà le attività per il processo. A tale scopo, è necessario che l'attività del gestore di processi esegua l'autenticazione con l'account batch. Usare il token di accesso AZ_BATCH_AUTHENTICATION_TOKEN. Il token consentirà l'accesso al resto del processo. 

## <a name="use-the-portal-to-schedule-a-job"></a>Usare il portale per pianificare un processo

   1. Accedere al [portale di Azure](https://portal.azure.com/).

   2. Selezionare l'account batch in cui si desidera pianificare i processi.

   3. Selezionare **Aggiungi** per creare una nuova pianificazione del processo e completare il **modulo di base**.



![Pianificare un processo][1]

**ID pianificazione processo**: identificatore univoco per la pianificazione del processo.

**Nome visualizzato**: il nome visualizzato per il processo non deve essere univoco, ma ha una lunghezza massima di 1024 caratteri.

**Non eseguire fino a**: specifica la prima volta che verrà eseguito il processo. Se non si imposta questa impostazione, la pianificazione diventa pronta per l'esecuzione immediata dei processi.

Non **eseguire dopo**: nessun processo viene eseguito dopo l'ora di impostazione. Se non si specifica un'ora, si crea una pianificazione del processo ricorrente che rimane attiva fino a quando non viene terminata in modo esplicito.

**Intervallo di ricorrenza**: è possibile specificare la quantità di tempo tra i processi. È possibile disporre di un solo processo alla volta pianificato, pertanto se è il momento di creare un nuovo processo in una pianificazione del processo, ma il processo precedente è ancora in esecuzione, il servizio batch non creerà il nuovo processo fino al completamento del processo precedente.  

**Finestra di avvio**: è possibile specificare l'intervallo di tempo, a partire dal momento in cui la pianificazione indica che è necessario creare un processo, fino al completamento. Se il processo corrente non viene completato durante la relativa finestra, il processo successivo non verrà avviato.

Nella parte inferiore del modulo di base, si specificherà il pool in cui si desidera eseguire il processo. Per trovare le informazioni sull'ID del pool, selezionare **Aggiorna**. 

![Specificare il pool][2]


**ID pool**: identificare il pool in cui si eseguirà il processo.

**Attività di configurazione del processo**: selezionare **Aggiorna** per assegnare un nome all'attività del gestore di processi, nonché le attività di preparazione e rilascio del processo, se usate.

**Priorità**: assegnare una priorità al processo.

**Tempo massimo di Clock Wall**: impostare la quantità massima di tempo per cui il processo può essere eseguito. Se non viene completato entro l'intervallo di tempo, batch termina il processo. Se non si imposta questa impostazione, non esiste alcun limite di tempo per il processo.

Numero **massimo tentativi attività**: specificare il numero di volte in cui un'attività può essere ritentata fino a un massimo di quattro volte. Non corrisponde al numero di tentativi che può avere una chiamata API.

Al **termine di tutte le attività**: il valore predefinito non è azione.

**Quando un'attività ha esito negativo**: il valore predefinito non è azione. Un'attività ha esito negativo se il numero di tentativi è esaurito o si è verificato un errore durante l'avvio dell'attività. 

Dopo aver selezionato **Salva**, se si passa a **pianificazioni processi** nel spostamento a sinistra, è possibile tenere traccia dell'esecuzione del processo selezionando **informazioni di esecuzione**.


## <a name="for-more-information"></a>Per ulteriori informazioni

Per gestire un processo usando l'interfaccia della riga di comando di Azure, vedere [AZ batch job-Schedule](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Passaggi successivi

[Creare relazioni tra attività per eseguire attività che dipendono da altre attività](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


