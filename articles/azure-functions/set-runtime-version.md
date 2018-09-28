---
title: Come specificare le versioni del runtime per Funzioni di Azure
description: La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni su come specificare la versione del runtime di un'app per le funzioni ospitata in Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 889a5a40409238462ee81d3bbd51ac6b77d28173
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947489"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Come specificare le versioni del runtime per Funzioni di Azure

L'app per le funzioni viene eseguita su una versione specifica del runtime di Funzioni di Azure. Sono disponibili due versioni principali: [1.x and 2.x](functions-versions.md). In questo articolo viene illustrato come configurare l'esecuzione di un'app per le funzioni in Azure con la versione scelta. Per informazioni su come configurare un ambiente di sviluppo locale per una versione specifica, vedere [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Aggiornamenti di versione automatici e manuali

La soluzione Funzioni consente di indicare una versione specifica del runtime usando l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione nell'app per le funzioni. L'app per le funzioni viene mantenuta nella versione principale specificata fino a quando non si sceglie esplicitamente di spostarla in una nuova versione.

Se si specifica solo la versione principale ("~2" per 2.x o "~1" per 1.x), l'app per le funzioni viene aggiornata automaticamente alle nuove versioni secondarie del runtime non appena queste risultano disponibili. Le versioni secondarie non introducono modifiche di rilievo. Se si specifica una versione secondaria, ad esempio "2.0.12345", viene mantenuta tale versione dell'app per le funzioni fino alla successiva modifica esplicita. 

Quando una nuova versione risulta disponibile pubblicamente, nel portale viene visualizzata una richiesta che consente di eseguire l'aggiornamento alla nuova versione. Dopo il passaggio a una nuova versione, è sempre possibile usare l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione per tornare a una versione precedente.

Ogni modifica alla versione del runtime comporta il riavvio dell'app per le funzioni.

I valori che è possibile inserire nell'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'app per abilitare gli aggiornamenti automatici attualmente sono "~1" per il runtime 1.x e "~2" per il runtime 2.x.

## <a name="view-the-current-runtime-version"></a>Visualizzare la versione corrente del runtime

Eseguire la procedura seguente per visualizzare la versione del runtime usata attualmente dall'app per le funzioni. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'app per le funzioni e in **Funzionalità configurate** scegliere **Impostazioni dell'app per le funzioni**. 

    ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-versions/add-update-app-setting.png)

2. Nella scheda **Impostazioni dell'app per le funzioni** individuare la **Versione runtime**. Notare la versione specifica del runtime e la versione principale richiesta. Nell'esempio seguente l'impostazione FUNCTIONS\_EXTENSION\_VERSION dell'applicazione è impostata su `~1`.
 
   ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Usare una versione tramite il portale

Quando è necessario usare una versione diversa dalla versione principale corrente o dalla versione 2.0, configurare l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'app per le funzioni e in **Funzionalità configurate** scegliere **Impostazioni dell'applicazione**.

    ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-versions/add-update-app-setting1a.png)

2. Nella scheda **Impostazioni dell'applicazione** trovare l'impostazione `FUNCTIONS_EXTENSION_VERSION` e modificare il valore specificando una versione valida del runtime 1.x oppure `~2` per la versione 2.0. Una tilde con versione principale indica l’uso della versione più recente di quella versione principale (ad esempio, "~ 1"). 

    ![Configurare l'impostazione dell'app per le funzioni](./media/functions-versions/add-update-app-setting2.png)

3. Fare clic su **Salva** per salvare l'aggiornamento dell'impostazione applicazione. 

## <a name="target-a-version-using-azure-cli"></a>Usare una versione tramite l'interfaccia della riga di comando di Azure

 È anche possibile configurare `FUNCTIONS_EXTENSION_VERSION` dall'interfaccia della riga di comando di Azure. Usare l'interfaccia della riga di comando di Azure per aggiornare l'impostazione dell'applicazione nell'app per le funzioni con il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
In questo codice sostituire `<function_app>` con il nome dell'app per le funzioni. Sostituire anche `<my_resource_group>` con il nome del gruppo di risorse per l'app per le funzioni. Sostituire `<version>` con una versione valida del runtime 1.x oppure con `~2` per la versione 2.x. 

È possibile eseguire questo comando da [Azure Cloud Shell](../cloud-shell/overview.md) scegliendo **Prova** nell'esempio di codice precedente. È anche possibile usare l'[interfaccia della riga di comando di Azure in locale](/cli/azure/install-azure-cli) per eseguire questo comando dopo l'esecuzione del comando [az login](/cli/azure/reference-index#az-login) per eseguire l'accesso.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare il runtime 2.0 nell'ambiente di sviluppo locale](functions-run-local.md)

> [!div class="nextstepaction"]
> [Vedere le note sulla versione per le versioni del runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)
