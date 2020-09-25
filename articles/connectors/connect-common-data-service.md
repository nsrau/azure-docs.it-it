---
title: Connetti a Common Data Service
description: Creare e gestire record di Common Data Service usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/08/2020
tags: connectors
ms.openlocfilehash: de85a61cbd699ec9ac2669f8abb6217254038de9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334583"
---
# <a name="create-and-manage-records-in-common-data-service-by-using-azure-logic-apps"></a>Creare e gestire i record in Common Data Service usando app per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il [connettore Common Data Service](/connectors/commondataservice/)è possibile creare flussi di lavoro automatizzati per la gestione dei record nel database [Common Data Service](/powerapps/maker/common-data-service/data-platform-intro) . Questi flussi di lavoro possono creare record, aggiornare record ed eseguire altre operazioni. È anche possibile ottenere informazioni dal database Common Data Service e rendere disponibile l'output per altre azioni da usare nell'app per la logica. Ad esempio, quando un record viene aggiornato nel database di Common Data Service, è possibile inviare un messaggio di posta elettronica usando il connettore Office 365 Outlook.

Questo articolo illustra come creare un'app per la logica che crea un record di attività ogni volta che viene creato un nuovo record lead.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* [Ambiente Common Data Service](/power-platform/admin/environments-overview), ovvero uno spazio in cui l'organizzazione archivia, gestisce e condivide i dati aziendali e un database Common Data Service. Per ulteriori informazioni, vedere le risorse:<p>

  * [Informazioni: Introduzione a Common Data Service](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Panoramica di Power Platform-environments](/power-platform/admin/environments-overview)

* Informazioni di base su [come creare app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) e l'app per la logica da cui si vuole accedere ai record nel database di Common Data Service. Per avviare l'app per la logica con un trigger di Common Data Service, è necessaria un'app per la logica vuota. Se non si ha familiarità con app per la logica di Azure, vedere [avvio rapido: creare il primo flusso di lavoro usando app per la logica di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Aggiungi Common Data Service trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Per questo esempio, aggiungere il trigger Common Data Service che viene attivato quando viene creato un nuovo record.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica vuota in Progettazione app per la logica, se non è già aperta.

1. Nella casella di ricerca immettere `common data service`. Per questo esempio, sotto l'elenco di trigger selezionare questo trigger: **quando viene creato un record**

   ![Selezionare il trigger "quando viene creato un record"](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Se richiesto, accedere al Common Data Service.

1. Nel trigger, fornire informazioni sull'ambiente in cui si desidera monitorare i nuovi record "Lead", ad esempio:

   ![Attivare le informazioni per l'ambiente da monitorare](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Environment** | Sì | Ambiente da monitorare, ad esempio, "Fabrikam Sales Production". Per altre informazioni, vedere [Panoramica di Power Platform-environments](/power-platform/admin/environments-overview). |
   | **Nome entità** | Sì | Entità da monitorare, ad esempio, "Lead" |
   | **Ambito** | Sì | Origine che ha creato il nuovo record, ad esempio un utente nella propria business unit o qualsiasi utente dell'organizzazione. Questo esempio USA "business unit". |
   ||||

## <a name="add-common-data-service-action"></a>Aggiungi Common Data Service azione

Aggiungere ora una Common Data Service azione che crea un record di attività per un nuovo record "Lead".

1. Nel trigger **quando viene creato un record** selezionare **nuovo passaggio**.

1. Nella casella di ricerca immettere `common data service`. Nell'elenco di azioni selezionare l'azione **Crea un nuovo record**

   ![Selezionare l'azione "crea un nuovo record"](./media/connect-common-data-service/select-create-new-record-action.png)

1. Nell'azione fornire le informazioni sull'ambiente in cui si desidera creare il nuovo record di attività. Se disponibile, anche altre proprietà vengono visualizzate in base all'entità selezionata per questa azione, ad esempio:

   ![Informazioni sull'azione per l'ambiente in cui creare il record](./media/connect-common-data-service/create-new-record-action-details.png)

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome organizzazione** | Sì | L'ambiente in cui si desidera creare il record, che non deve necessariamente essere lo stesso ambiente del trigger, ma è "Fabrikam Sales Production" in questo esempio |
   | **Nome entità** | Sì | Entità in cui si desidera creare il record, ad esempio "Attività" |
   | **Oggetto** | Sì, in base all'entità selezionata in questo esempio | Breve descrizione dell'obiettivo di questa attività |
   ||||

   1. Per la proprietà **Subject** immettere questo testo con uno spazio finale:

      `Follow up with new lead:`

   1. Mantenere il puntatore all'interno della casella **oggetto** in modo che l'elenco di contenuto dinamico rimanga visibile.
   
   1. Nell'elenco, dalla sezione **quando viene creato un record** , selezionare gli output del trigger che si desidera includere nel record attività, ad esempio:

      ![Selezionare i trigger output da usare nel record attività](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Output del trigger | Descrizione |
      |----------------|-------------|
      | **Nome** | Il primo nome del record principale da usare come contatto principale nel record dell'attività |
      | **Cognome** | Cognome del record principale da usare come contatto principale nel record dell'attività |
      | **Descrizione** | Altri output da includere nel record dell'attività, ad esempio indirizzo di posta elettronica e numero di telefono dell'ufficio |
      |||

   Al termine, l'azione potrebbe essere simile a questo esempio:

   ![Azione "crea un nuovo record" completata](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

1. Per avviare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione selezionare **Esegui**. Per testare l'app per la logica, creare un nuovo record "Lead".

## <a name="trigger-only-on-updated-attributes"></a>Attiva solo sugli attributi aggiornati

Per i trigger che vengono eseguiti quando vengono aggiornati i record, ad esempio l'azione **quando un record viene aggiornato** , è possibile usare gli attributi di filtro in modo che l'app per la logica venga eseguita solo quando gli attributi specificati vengono aggiornati. Questa funzionalità consente di evitare l'esecuzione di app per la logica non necessarie.

1. Nel trigger selezionare **filtri attributi**nell'elenco **Aggiungi nuovo parametro** .

   ![Screenshot che mostra l'azione "quando un record viene aggiornato" e l'elenco aperto "Aggiungi nuovo parametro" con la proprietà "filtri attributo" selezionata.](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Per ogni **elemento filtri attributi**selezionare l'attributo che si desidera monitorare per gli aggiornamenti, ad esempio:

   ![Aggiungere la proprietà "filtri attributo"](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Elencare i record in base a un filtro

Per le azioni che restituiscono record, ad esempio l'azione **elenco record** , è possibile usare una query OData che restituisce record basati sul filtro specificato. Ad esempio, l'elenco azione contiene solo i record per gli account attivi.

1. Nell'azione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare la proprietà **filtro query** .

   ![Aggiungere la proprietà "Filter query"](./media/connect-common-data-service/list-records-action-filter-query.png)

1. Nella proprietà **filtro query** che ora appare nell'azione immettere questa query di filtro OData: `statuscode eq 1`

   ![Immettere una query di filtro ODATA per filtrare i record](./media/connect-common-data-service/list-records-action-filter-query-value.png)

Per ulteriori informazioni sulle `$filter` Opzioni di query di sistema, vedere [Common Data Service-filtrare i risultati](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Elencare i record in base a un ordine

Per le azioni che restituiscono record, ad esempio l'azione **elenco record** , è possibile usare una query OData che restituisce i record in un ordine specificato, che varia in base ai record restituiti dall'azione. Ad esempio, è possibile fare in modo che l'azione elenchi i record in base al nome dell'account.

1. Nell'azione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare la proprietà **Order by** .

   ![Aggiungere la proprietà "order by"](./media/connect-common-data-service/list-records-action-order-by.png)

1. Nella proprietà **Order by** che ora appare nell'azione immettere questa query del filtro OData: `name`

   ![Immettere una query di filtro ODATA per l'ordinamento dei record](./media/connect-common-data-service/list-records-action-order-by-value.png)

Per ulteriori informazioni sulle `$orderby` Opzioni di query di sistema, vedere [risultati dell'ordine Common Data Service](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results).

## <a name="field-data-types"></a>Tipi di dati dei campi

Indipendentemente dal fatto che si immetta manualmente un valore o si selezioni un valore dall'elenco di contenuto dinamico per un campo in un trigger o un'azione, il tipo di dati del valore deve corrispondere al tipo di dati obbligatorio del campo.

In questa tabella vengono descritti alcuni tipi di campo e i tipi di dati richiesti da tali campi per i relativi valori.

| Campo | Tipo di dati | Descrizione |
|-------|-----------|-------------|
| Campo di testo | Riga di testo singola | Richiede una singola riga di testo o contenuto dinamico con tipo di dati text, ad esempio, queste proprietà: <p><p>- **Descrizione** <br>- **Categoria** |
| Campo Integer | Numero intero | Richiede un intero o contenuto dinamico con tipo di dati Integer, ad esempio, queste proprietà: <p><p>- **Percentuale di completamento** <br>- **Durata** |
| Campo Data | Data e ora | Richiede una data in formato MM/GG/YYY o contenuto dinamico con il tipo di dati date, ad esempio, queste proprietà: <p><p>- **Data creazione** <br>- **Data di inizio** <br>- **Inizio effettivo** <br>- **Fine effettiva** <br>- **Scadenza** |
| Campo che fa riferimento a un altro record di entità | Chiave primaria | Richiede sia un ID record, ad esempio un GUID, sia un tipo di ricerca, il che significa che i valori dell'elenco di contenuto dinamico non funzionano, ad esempio, queste proprietà: <p><p>- **Proprietario**: deve essere un ID utente valido o un ID record del team. <br>- **Proprietario Type**: deve essere un tipo di ricerca, ad esempio `systemusers` o `teams` , rispettivamente. <p><p>- **Per quanto riguarda**, deve essere un ID record valido, ad esempio un ID account o un ID record contatto. <br>- **Relativa al tipo**: deve essere un tipo di ricerca `accounts` , ad esempio o `contacts` , rispettivamente. <p><p>- **Customer**: deve essere un ID record valido, ad esempio un ID account o un ID record di contatto. <br>- **Customer Type**: deve essere il tipo di ricerca, rispettivamente,, ad esempio `accounts` o `contacts` . |
||||

Questo esempio Mostra come l'azione **Crea un nuovo record** crea un nuovo record "Tasks" associato ad altri record di entità, in particolare un record utente e un record di account. L'azione specifica gli ID e i tipi di ricerca per i record di entità utilizzando valori che corrispondono ai tipi di dati previsti per le proprietà pertinenti.

* In base alla proprietà **owner** , che specifica un ID utente e alla proprietà **Type del proprietario** , che specifica il `systemusers` tipo di ricerca, l'azione associa il nuovo record "Tasks" a un utente specifico.

* In base alla **Proprietà about** , che specifica un ID record e la proprietà **relativa al tipo** , che specifica il `accounts` tipo di ricerca, l'azione associa il nuovo record "Tasks" a un account specifico.

![Creare un record "Tasks" associato agli ID e ai tipi di ricerca](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche sulla base della descrizione di spavalderia del connettore, ad esempio trigger, azioni, limiti e altri dettagli, vedere la [pagina di riferimento del connettore](/connectors/commondataservice/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori per App per la logica di Azure](../connectors/apis-list.md)
