---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6c430f22a9d4fa0fad95bcaa41675545fffd91ec
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227219"
---
Il modello di progetto Funzioni di Azure in Visual Studio crea un progetto che può essere pubblicato in un'app per le funzioni in Azure. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per la gestione, la distribuzione e la condivisione delle risorse.

1. Nel menu **File** in Visual Studio selezionare **Nuovo** > **Progetto**.

2. Nella finestra di dialogo **Nuovo progetto** selezionare **Installati** > **Visual C#** > **Cloud** > **Funzioni di Azure**. Immettere un nome per il progetto e fare clic su **OK**. Il nome dell'app per le funzioni deve essere valido come spazio dei nomi C#, quindi non usare caratteri di sottolineatura, trattini o altri caratteri non alfanumerici.

    ![Finestra di dialogo Nuovo progetto per creare una funzione in Visual Studio](./media/functions-vstools-create/functions-vs-new-project.png)

3. Usare le impostazioni specificate nella tabella disponibile sotto l'immagine.

    ![Finestra di dialogo Nuova funzione in Visual Studio](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Impostazione      | Valore consigliato  | DESCRIZIONE                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versione** | Funzioni di Azure 2.x <br />(.NET Core) | Questa impostazione crea un progetto per le funzioni che usa la versione 2.x del runtime di Funzioni di Azure e supporta .NET Core. Funzioni di Azure 1.x supporta .NET Framework. Per altre informazioni, vedere [Come specificare le versioni del runtime per Funzioni di Azure](../articles/azure-functions/functions-versions.md).   |
    | **Modello** | Trigger HTTP | Questa impostazione crea una funzione attivata da una richiesta HTTP. |
    | **Account di archiviazione**  | Emulatore di archiviazione | Un trigger HTTP non usa la connessione dell'account di archiviazione di Azure. Tutti gli altri tipi di trigger richiedono una stringa di connessione dell'account di archiviazione valida. |
    | **Diritti di accesso** | Anonima | Viene creata una funzione che può essere attivata da qualsiasi client senza dover fornire una chiave. Questa impostazione di autorizzazione consente di testare più facilmente la nuova funzione. Per altre informazioni sulle chiavi e l'autorizzazione, vedere [Chiavi autorizzazione](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) in [Associazioni HTTP e webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Assicurarsi di impostare i **diritti di accesso** su `Anonymous`. Se si sceglie il livello predefinito di `Function`, è necessario fornire la [chiave di funzione](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) nelle richieste di accesso all'endpoint della funzione.
    
4. Selezionare **OK** per creare il progetto di funzione e la funzione attivata da HTTP.
