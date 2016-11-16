---
title: Creare una funzione di Azure associata a un servizio di Azure | Documentazione Microsoft
description: Compilare una funzione di Azure, un&quot;applicazione senza server, che interagisce con altri servizi di Azure.
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Creare una funzione di Azure associata a un servizio di Azure
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

Questo breve video illustra come creare una funzione di Azure che ascolta i messaggi in una coda di Azure e li copia in un BLOB di Azure.

## <a name="watch-the-video"></a>Video
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>Creare una funzione trigger della coda di input
L'obiettivo di questa funzione è scrivere un messaggio in una coda ogni 10 secondi. A tale scopo, è necessario creare le code dei messaggi e della funzione e aggiungere il codice per la scrittura dei messaggi nelle code appena create.

1. Passare al portale di Azure e trovare l'app per le funzioni di Azure.
2. Fare clic su **Nuova funzione** > **TimerTrigger - Node**. Assegnare alla funzione il nome **FunctionsBindingsDemo1**
3. Immettere un valore di "0/10 * * * * *" per la pianificazione. Questo valore ha la forma di un'espressione Cron e permette di pianificare l'esecuzione del timer ogni 10 secondi.
4. Fare clic sul pulsante **Crea** per creare la funzione.
   
    ![Aggiungere una funzione timer trigger](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. Verificare il funzionamento della funzione visualizzando l'attività nel log. Per visualizzare il riquadro dei log, potrebbe essere necessario fare clic sul collegamento **Log** nell'angolo superiore destro.
   
   ![Verificare il funzionamento della funzione visualizzando il log](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>Aggiungere una coda dei messaggi
1. Passare alla scheda **Integrazione**.
2. Scegliere **Nuovo output** > **Coda di archiviazione di Azure** > **Seleziona**.
3. Immettere **myQueueItem** nella casella di testo **Nome del parametro del messaggio**.
4. Selezionare un account di archiviazione. Se non è disponibile, fare clic su **nuovo** per crearne uno.
5. Immettere **functions-bindings** nella casella di testo **Nome coda**.
6. Fare clic su **Save**.  
   
   ![Aggiungere una funzione timer trigger](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>Scrivere nella coda dei messaggi
1. Tornare alla scheda **Sviluppo** e aggiungere il codice seguente alla funzione dopo il codice esistente:
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. Modificare il codice della funzione esistente per chiamare il codice aggiunto nel passaggio 1. Inserire il codice seguente attorno alla riga 9 della funzione, dopo l'istruzione *if*.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    Questo codice crea un oggetto **myQueueItem** e ne imposta la proprietà **time** sul valore corrente di timeStamp. Aggiunge quindi il nuovo elemento della coda all'associazione myQueue del contesto.
3. Fare clic su **Salva ed esegui**.
4. Per verificare il funzionamento del codice, visualizzare la coda in Visual Studio.
   
   * Aprire Visual Studio e passare a **Visualizza** > **Cloud** **Explorer**.
   * Trovare l'account di archiviazione e la coda **functions-bindings** usata durante la creazione della coda myQueue. Dovrebbero essere visualizzate righe di dati di log. Potrebbe essere necessario accedere ad Azure tramite Visual Studio.  

## <a name="create-an-output-queue-trigger-function"></a>Creare una funzione trigger della coda di output
1. Fare clic su **Nuova funzione** > **QueueTrigger - C#**. Assegnare alla funzione il nome **FunctionsBindingsDemo2**. Si noti che è possibile combinare più linguaggi nella stessa app per le funzioni, in questo caso Node e C#.
2. Immettere **functions-bindings** nel campo **Nome coda**.
3. Selezionare un account di archiviazione da usare o crearne uno nuovo.
4. Fare clic su **Crea**
5. Verificare il funzionamento della nuova funzione visualizzando sia il log della funzione che Visual Studio per gli aggiornamenti. Il log della funzione mostra che la funzione è in esecuzione e gli elementi vengono rimossi dalla coda. Dato che la funzione è associata alla coda di output **functions-bindings** come trigger di input, aggiornando la coda **functions-bindings** in Visual Studio si dovrebbe vedere che gli elementi sono spariti perché rimossi dalla coda.   
   
   ![Aggiungere una funzione timer della coda di output](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>Modificare il tipo di elemento della coda da JSON in oggetto
1. Sostituire il codice in **FunctionsBindingsDemo2** con il codice seguente:    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    Questo codice aggiunge due classi, **TableItem** e **QItem**, che consentono di leggere e scrivere nelle code. Anche la funzione **Run** è stata modificata in modo da accettare i parametri **QItem** e **TraceWriter** anziché **stringa** e **TraceWriter**. 
2. Fare clic sul pulsante **Salva** .
3. Verificare il funzionamento del codice controllando il log. Si noti che le funzioni di Azure serializzano e deserializzano automaticamente l'oggetto, semplificando l'accesso alla coda in una modalità orientata agli oggetti per lo scambio di dati. 

## <a name="store-messages-in-an-azure-table"></a>Archiviare i messaggi in una tabella di Azure
Ora che entrambe le code funzionano è possibile aggiungere una tabella di Azure per l'archiviazione permanente dei dati delle code.

1. Passare alla scheda **Integrazione**.
2. Creare una tabella di archiviazione di Azure per l'output e denominarla **myTable**.
3. Alla domanda su quale tabella usare per la scrittura dei dati, rispondere **functionsbindings**.
4. Modificare l'impostazione di **PartitionKey** da **{project-id}** in **{partition}**.
5. Scegliere un account di archiviazione da usare o crearne uno nuovo.
6. Fare clic su **Save**.
7. Passare alla scheda **Sviluppo**.
8. Creare una classe **TableItem** per rappresentare una tabella di Azure e modificare la funzione Run in modo che accetti l'oggetto TableItem appena creato. Perché possa funzionare è necessario usare le proprietà **PartitionKey** e **RowKey**.
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. Fare clic su **Save**.
10. Verificare il funzionamento del codice sia visualizzando i log della funzione che in Visual Studio. Per verificare in Visual Studio, usare **Cloud Explorer** per passare alla tabella di Azure **functionsbindings** e verificare che contenga righe.

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


