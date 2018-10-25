---
title: Installare o aggiornare manualmente le estensioni delle associazioni di Funzioni di Azure
description: Informazioni su come installare o aggiornare le estensioni di associazione di Funzioni di Azure per le app per le funzioni distribuite.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funzioni di Azure, funzioni, estensioni di associazione, NuGet, aggiornamenti
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: 77b863bc32442261e220b5dd3f11c0bd33b4fa7a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807258"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Installare o aggiornare manualmente le estensioni delle associazioni di Funzioni di Azure dal portale

Il runtime di Funzioni di Azure versione 2.x usa le estensioni delle associazioni per implementare il codice per i trigger e le associazioni. Le estensioni delle associazioni sono incluse nei pacchetti NuGet. Per registrare un'estensione, essenzialmente si installa un pacchetto. Quando si sviluppano le funzioni, il modo in cui si installano le estensioni delle associazioni dipende dall'ambiente di sviluppo. Per altre informazioni, vedere [Registrare le estensioni delle associazioni](functions-triggers-bindings.md#register-binding-extensions) nell'articolo relativo ai trigger e alle associazioni.

In alcuni casi è necessario installare o aggiornare manualmente le estensioni delle associazioni nel portale di Azure. Potrebbe essere necessario, ad esempio, eseguire l'aggiornamento di un'associazione registrata a una versione più recente. Potrebbe essere anche necessario registrare un'associazione supportata che non può essere installata nella scheda **Integrazione** nel portale.

## <a name="install-a-binding-extension"></a>Installare le estensioni delle associazioni

Usare la procedura seguente per installare o aggiornare manualmente le estensioni dal portale.

1. Nel [portale di Azure](https://portal.azure.com) trovare l'app per le funzioni e selezionarla. Scegliere la scheda **Panoramica** e selezionare **Arresta**.  L'arresto dell'app per le funzioni sblocca i file in modo che sia possibile apportare modifiche.

1. Scegliere la scheda **Funzionalità della piattaforma** e in **Strumenti di sviluppo** selezionare **Strumenti avanzati (Kudu)**. L'endpoint Kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) si apre in una nuova finestra.

1. Nella finestra Kudu selezionare **Console di debug** > **CMD**.  

1. Nella finestra di comando passare a `D:\home\site\wwwroot` e scegliere l'icona di eliminazione accanto a `bin` per eliminare la cartella. Selezionare **OK** per confermare l'eliminazione.

1. Scegliere l'icona di modifica accanto al file `extensions.csproj` che definisce le estensioni delle associazioni dell'app per le funzioni. Il file di progetto viene aperto nell'editor online.

1. Apportare le aggiunte e gli aggiornamenti richiesti degli elementi **PackageReference** in **ItemGroup**, quindi selezionare **Salva**. L'elenco corrente delle versioni supportate del pacchetto è reperibile nell'articolo del wiki [What packages do I need?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) (Quali sono i pacchetti necessari?). Tutte e tre le associazioni di Archiviazione di Azure richiedono il pacchetto Microsoft.Azure.WebJobs.Extensions.Storage.

1. Dalla cartella `wwwroot` eseguire il comando seguente per ricompilare gli assembly a cui si fa riferimento nella cartella `bin`.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Nella scheda **Panoramica** nel portale scegliere **Avvia** per riavviare l'app per le funzioni.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
