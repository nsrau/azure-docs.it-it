---
title: Pianificare i processi
description: Usare la pianificazione dei processi per gestire le attività.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 89881808e48b3fbe44fbcd5352a15afc4eb427ef
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964346"
---
# <a name="schedule-jobs-for-efficiency"></a>Pianificare i processi per l'efficienza

La pianificazione dei processi Batch consente di assegnare una priorità ai processi da eseguire per primi tenendo conto delle attività che presentano dipendenze da altre attività. Pianificando i processi, è possibile assicurarsi di usare la quantità minima di risorse. È possibile rimuovere i nodi quando non sono necessari; le attività che dipendono da altre attività vengono riattivate in tempo per garantire l'ottimizzazione dei flussi di lavoro. Viene eseguito un solo processo alla volta. Non viene avviato alcun nuovo processo fino a quando non viene completato quello precedente. È possibile impostare il processo per il completamento automatico. 

## <a name="benefit-of-job-scheduling"></a>Vantaggi della pianificazione dei processi

La pianificazione dei processi permette di specificare una pianificazione per la creazione di processi. Le attività pianificate mediante l'attività del gestore di processi sono associate a un processo. L'attività del gestore di processi crea le attività per il processo. A tale scopo, è necessario che l'attività del gestore di processi esegua l'autenticazione con l'account Batch. Usare il token di accesso AZ_BATCH_AUTHENTICATION_TOKEN. Il token consentirà l'accesso al resto del processo. 

## <a name="use-the-portal-to-schedule-a-job"></a>Usare il portale per pianificare un processo

   1. Accedere al [portale di Azure](https://portal.azure.com/).

   2. Selezionare l'account Batch in cui pianificare i processi.

   3. Selezionare **Aggiungi** per creare una nuova pianificazione processo e completare il **modulo Basic**.



![Pianificare un processo][1]

**ID pianificazione processi**: Identificatore univoco per la pianificazione di un determinato processo.

**Nome visualizzato**: Il nome visualizzato per il processo non deve essere univoco, ma deve avere una lunghezza massima di 1024 caratteri.

**Non eseguire fino a**: Specifica la prima data possibile per l'esecuzione del processo. Se questa impostazione non viene configurata, la pianificazione diventa pronta per l'esecuzione immediata dei processi.

**Non eseguire dopo**: Nessun processo viene eseguito dopo l'ora impostata qui. Se non si specifica un'ora, si crea una pianificazione del processo ricorrente che rimane attiva fino a quando non viene terminata in modo esplicito.

**Intervallo di ricorrenza**: È possibile specificare l'intervallo di tempo tra i processi. È possibile disporre di un solo processo pianificato alla volta; pertanto, se deve essere creato un nuovo processo in una pianificazione, ma il processo precedente è ancora in esecuzione, il servizio Batch non creerà il nuovo processo fino al completamento del processo precedente.  

**Finestra iniziale**: Qui è possibile specificare l'intervallo di tempo che decorre dal momento in cui la pianificazione indica che è necessario creare un processo e termina al completamento previsto. Se il processo corrente non viene completato durante la relativa finestra, il processo successivo non viene avviato.

Nella parte inferiore del modulo di base, si specificherà il pool in cui eseguire il processo. Per trovare le informazioni sull'ID pool, selezionare **Aggiorna**. 

![Specificare il pool][2]


**ID pool**: Individuare il pool in cui verrà eseguito il processo.

**Attività di configurazione processo**: Selezionare **Aggiorna** per assegnare un nome all'attività del gestore di processi, nonché alle attività di preparazione e release del processo, se usate.

**Priorità**: Assegnare una priorità al processo.

**Tempo massimo**: Consente di impostare il tempo massimo per cui il processo può essere eseguito. Se il processo non viene completato entro l'intervallo di tempo, Batch lo termina. Se questa impostazione non viene configurata, non esiste alcun limite di tempo per il processo.

**Numero massimo di tentativi attività**: Specificare il numero di volte in cui un'attività può essere ritentata fino a un massimo di quattro volte. Non corrisponde al numero di tentativi che può avere una chiamata API.

**Quando tutte le attività vengono completate**: Il valore predefinito è NO ACTION.

**Quando un'attività ha esito negativo**: Il valore predefinito è NO ACTION. Un'attività ha esito negativo se il numero di tentativi è esaurito o si è verificato un errore durante l'avvio dell'attività. 

Dopo avere selezionato **Salva**, se si passa a **Pianificazioni processi** nel pannello di navigazione a sinistra, è possibile tenere traccia dell'esecuzione del processo selezionando **informazioni sull'esecuzione**.


## <a name="for-more-information"></a>Per ulteriori informazioni

Per gestire un processo usando l'interfaccia della riga di comando di Azure, vedere [Processo batch AZ - Pianificazione](/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Passaggi successivi

[Creare relazioni tra attività per eseguire attività che dipendono da altre attività](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


