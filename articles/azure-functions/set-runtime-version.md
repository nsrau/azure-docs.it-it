---
title: Come specificare le versioni del runtime per Funzioni di Azure
description: "La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni su come specificare la versione del runtime di un'app per le funzioni ospitata in Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 6fc84642050f4b7acfa2e3c5b4518135d6a97171
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Come specificare le versioni del runtime per Funzioni di Azure

L'app per le funzioni viene eseguita su una versione specifica del runtime di Funzioni di Azure. Sono disponibili due versioni principali: [1.x and 2.x](functions-versions.md). In questo articolo viene illustrato come configurare l'esecuzione di un'app per le funzioni in Azure con la versione scelta. Per informazioni su come configurare un ambiente di sviluppo locale per una versione specifica, vedere [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md).

>[!IMPORTANT]   
> Il runtime 2.0 di funzioni di Azure è disponibile in anteprima e attualmente non tutte le funzionalità di Funzioni di Azure sono supportate. Per altre informazioni, vedere [Panoramica delle versioni del runtime per Funzioni di Azure](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Aggiornamenti di versione automatici e manuali

La soluzione Funzioni consente di indicare una versione specifica del runtime usando l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione nell'app per le funzioni. L'app per le funzioni viene mantenuta nella versione principale specificata fino a quando non si sceglie esplicitamente di spostarla in una nuova versione.

Se si specifica solo la versione principale ("~1" per 1.x o "beta" per 2.x), l'app per le funzioni viene automaticamente aggiornata alle nuove versioni secondarie del runtime non appena risultano disponibili. Le versioni secondarie non introducono modifiche di rilievo. Se si specifica una versione secondaria, ad esempio "1.0.11360", l'app per le funzioni viene conservata in tale versione fino alla successiva modifica esplicita. 

Quando una nuova versione risulta disponibile pubblicamente, nel portale viene visualizzata una richiesta che consente di eseguire l'aggiornamento alla nuova versione. Dopo il passaggio a una nuova versione, è sempre possibile usare l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione per tornare a una versione precedente.

Ogni modifica alla versione del runtime comporta il riavvio dell'app per le funzioni.

I valori che è possibile impostare nell'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione per abilitare gli aggiornamenti automatici attualmente sono "~1" per il runtime 1.x e "beta" per la versione 2.x.

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

2. Nella scheda **Impostazioni dell'applicazione** trovare l'impostazione `FUNCTIONS_EXTENSION_VERSION` e modificare il valore specificando una versione valida del runtime 1.x oppure `beta` per la versione 2.0. Una tilde con versione principale indica l’uso della versione più recente di quella versione principale (ad esempio, "~ 1"). 

    ![Configurare l'impostazione dell'app per le funzioni](./media/functions-versions/add-update-app-setting2.png)

3. Fare clic su **Salva** per salvare l'aggiornamento dell'impostazione applicazione. 

## <a name="target-a-version-using-azure-cli"></a>Usare una versione tramite l'interfaccia della riga di comando di Azure

 È anche possibile configurare `FUNCTIONS_EXTENSION_VERSION` dall'interfaccia della riga di comando di Azure. Usare l'interfaccia della riga di comando di Azure per aggiornare l'impostazione dell'applicazione nell'app per le funzioni con il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
In questo codice sostituire `<function_app>` con il nome dell'app per le funzioni. Sostituire anche `<my_resource_group>` con il nome del gruppo di risorse per l'app per le funzioni. Sostituire `<version>` con una versione valida del runtime 1.x oppure con `beta` per la versione 2.0. 

È possibile eseguire questo comando da [Azure Cloud Shell](../cloud-shell/overview.md) scegliendo **Prova** nell'esempio di codice precedente. È anche possibile usare l'[interfaccia della riga di comando di Azure in locale](/cli/azure/install-azure-cli) per eseguire questo comando dopo l'esecuzione del comando [az login](/cli/azure/reference-index#az_login) per eseguire l'accesso.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare il runtime 2.0 nell'ambiente di sviluppo locale](functions-run-local.md)

> [!div class="nextstepaction"]
> [Vedere le note sulla versione per le versioni del runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)