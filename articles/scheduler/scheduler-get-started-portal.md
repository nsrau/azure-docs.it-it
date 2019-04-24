---
title: Creare un processo pianificato con l'Utilità di pianificazione di Azure - Portale di Azure | Documentazione di Microsoft
description: Informazioni su come creare, pianificare ed eseguire il primo processo automatizzato con l’utilità di pianificazione di Azure nel portale di Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 3b2cfc932c6322df8237ec7cdf820fc4242bfa72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531888"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Crea e pianifica il primo processo con l’utilità di pianificazione di Azure - Portale di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà l'Utilità di pianificazione di Azure di cui è in corso il ritiro. Per pianificare i processi, [provare App per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Questa esercitazione illustra quanto è facile creare, pianificare, monitorare e gestire un processo. 

Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

## <a name="create-job"></a>Creare il processo

1. Accedere al [portale di Azure](https://portal.azure.com/).  

1. Nel menu principale di Azure scegliere **Crea una risorsa**. Nella casella di ricerca immettere “utilità di pianificazione”. Nell'elenco dei risultati, selezionare **utilità di pianificazione**, quindi scegliere **Crea**.

   ![Creare la risorsa dell'Utilità di pianificazione di Microsoft Azure](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   A questo punto creare un processo per inviare una richiesta GET a questo URL: `https://www.microsoft.com/` 

1. Sotto **Processo dell'utilità di pianificazione**, immettere queste informazioni:

   | Proprietà | Valore di esempio | DESCRIZIONE |
   |----------|---------------|-------------| 
   | **Nome** | getMicrosoft | Il nome del processo | 
   | **Raccolta di processi** | <*nome-raccolta-processi*> | Creare una raccolta di processi o selezionare una raccolta esistente. | 
   | **Sottoscrizione** | <*nome sottoscrizione di Azure*> | Nome della sottoscrizione di Azure | 
   |||| 

1. Selezionare **Configura impostazioni azione**, fornire queste informazioni e quindi scegliere **OK**:

   | Proprietà | Valore di esempio | DESCRIZIONE |
   |----------|---------------|-------------| 
   | **Azione** | **Http** | Tipo di azione da eseguire | 
   | **Metodo** | **GET** | Il metodo da chiamare | 
   | **URL** | **https://www.microsoft.com** | URL di destinazione | 
   |||| 
   
   ![Definire il processo](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Selezionare **Configura pianificazione**, definire la pianificazione e quindi selezionare **OK**:

   Sebbene sia possibile creare un processo singolo, in questo esempio viene configurata una pianificazione ricorrente.

   | Proprietà | Valore di esempio | DESCRIZIONE |
   |----------|---------------|-------------| 
   | **Ricorrenza** | **Ricorrente** | Immettere un processo singolo o ricorrente | 
   | **Data di inizio** | <*data-odierna*> | La data di inizio del processo | 
   | **Ricorre ogni** | **1 ora(e)** | L'intervallo e la frequenza di ricorrenza | 
   | **Fine** | **Termina entro** due giorni dalla data odierna | La data di fine del processo | 
   | **Differenza dall'ora UTC** | **UTC +08:00** | La differenza oraria tra il fuso orario UTC (Coordinated Universal Time) e il fuso orario della tua posizione | 
   |||| 

   ![Definire una pianificazione](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Al termine, scegliere **Crea**.

   Dopo aver creato il processo, Azure distribuisce il processo, che viene visualizzato nel dashboard di Azure. 

1. Quando Azure visualizza una notifica che la distribuzione ha avuto esito positivo, scegliere **Aggiungi al dashboard**. In caso contrario, scegliere l’icona **Notifiche** (campanello) sulla barra degli strumenti di Azure, quindi scegliere **Aggiungi al dashboard**.

## <a name="monitor-and-manage-jobs"></a>Monitoraggio e gestione dei processi

Per esaminare, monitorare e gestire il processo, scegliere il processo nel dashboard di Azure. Sotto **impostazioni** sono indicate le aree che è possibile esaminare e gestire per il processo:

![Impostazioni del processo](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Per altre informazioni su queste aree, selezionare un’area:

* [**Proprietà**](#properties)
* [**Impostazioni azione**](#action-settings)
* [**Pianificazione**](#schedule)
* [**Cronologia**](#history)
* [**Utenti**](#users)

<a name="properties"></a>

### <a name="properties"></a>Properties

Per visualizzare le proprietà di sola lettura che descrivono i metadati di gestione per il processo, selezionare **Proprietà**.

![Visualizzare le proprietà del processo](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Impostazioni azione

Per modificare le impostazioni avanzate del processo, selezionare **Impostazioni azione**. 

![Esaminare le Impostazioni dell'azione](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Tipo di azione | DESCRIZIONE | 
|-------------|-------------| 
| Tutti i tipi | È possibile modificare i **Criteri di ripetizione** e l'**Azione di errore** per tutti i tipi di azione. | 
| HTTP e HTTPS | È possibile modificare **Metodo** a qualsiasi metodo consentito. Inoltre, è possibile aggiungere, eliminare o modificare le intestazioni e le informazioni di autenticazione di base. | 
| Coda di archiviazione| È possibile modificare account di archiviazione, nome della coda, token SAS e corpo. | 
| Bus di servizio | È possibile modificare lo spazio dei nomi, il percorso della coda o dell'argomento, le impostazioni di autenticazione, il tipo di trasporto, le proprietà del messaggio e il corpo del messaggio. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Pianificazione

Se si configura una pianificazione tramite la procedura guidata del processo, è possibile modificare tale pianificazione, ad esempio data e ora di inizio, pianificazione della ricorrenza e data e ora di fine per i processi ricorrenti.
Inoltre, è possibile creare [pianificazioni più complesse e ricorrenze avanzate](scheduler-advanced-complexity.md).

Per cambiare visualizzazione o modificare la pianificazione del processo, selezionare **Pianificazione**:

![Visualizza pianificazione processi](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Cronologia

Per visualizzare le metriche relative a ogni esecuzione per un processo selezionato, selezionare **Cronologia**. Queste metriche forniscono valori in tempo reale sull'integrità del processo, ad esempio stato, numero di tentativi, numero di occorrenze, ora di inizio e ora di fine.

![Visualizzare la cronologia e le metriche del processo](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Per visualizzare i dettagli della cronologia per ogni esecuzione, ad esempio la risposta completa per ogni esecuzione, selezionare ogni esecuzione sotto **Cronologia**. 

![Visualizzare i dettagli della cronologia del processo](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Utenti

È possibile gestire l'accesso all'utilità di pianificazione di Azure per ogni utente a un livello granulare tramite il controllo degli accessi in base al ruolo (RBAC). Per informazioni su come configurare l'accesso basato sui ruoli, vedere [Gestire l'accesso tramite RBAC](../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [concetti, terminologia e gerarchia di entità](scheduler-concepts-terms.md)
* [Creare pianificazioni complesse e ricorrenze avanzate](scheduler-advanced-complexity.md)
* Informazioni sui [livelli elevati di disponibilità e affidabilità nell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)
* Informazioni su [limiti, quote, valori predefiniti e codici di errore](scheduler-limits-defaults-errors.md)
