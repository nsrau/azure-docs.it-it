---
title: Come specificare le versioni del runtime per Funzioni di Azure
description: La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni su come specificare la versione del runtime di un'app per le funzioni ospitata in Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77151956"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Come specificare le versioni del runtime per Funzioni di Azure

L'app per le funzioni viene eseguita su una versione specifica del runtime di Funzioni di Azure. Sono disponibili tre versioni principali: [1. x, 2. x e 3. x](functions-versions.md). Per impostazione predefinita, le app per le funzioni vengono create nella versione 2. x del runtime. In questo articolo viene illustrato come configurare l'esecuzione di un'app per le funzioni in Azure con la versione scelta. Per informazioni su come configurare un ambiente di sviluppo locale per una versione specifica, vedere [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Aggiornamenti di versione automatici e manuali

Funzioni di Azure consente di specificare come destinazione una versione specifica del runtime usando l' `FUNCTIONS_EXTENSION_VERSION` impostazione dell'applicazione in un'app per le funzioni. L'app per le funzioni viene mantenuta nella versione principale specificata fino a quando non si sceglie esplicitamente di spostarla in una nuova versione.

Se si specifica solo la versione principale, l'app per le funzioni viene aggiornata automaticamente alle nuove versioni secondarie del runtime quando diventano disponibili. Le nuove versioni secondarie non devono introdurre modifiche di rilievo. Se si specifica una versione secondaria, ad esempio "2.0.12345", l'app per le funzioni viene bloccata alla versione specifica fino a quando non viene modificata in modo esplicito.

> [!NOTE]
> Se si aggiunge una versione specifica di funzioni di Azure e quindi si prova a eseguire la pubblicazione in Azure tramite Visual Studio, viene visualizzata una finestra di dialogo che richiede di eseguire l'aggiornamento alla versione più recente o di annullare la pubblicazione. Per evitare questo problema, aggiungere la `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` proprietà nel `.csproj` file.

Quando una nuova versione risulta disponibile pubblicamente, nel portale viene visualizzata una richiesta che consente di eseguire l'aggiornamento alla nuova versione. Dopo il passaggio a una nuova versione, è sempre possibile usare l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione per tornare a una versione precedente.

La tabella seguente mostra i `FUNCTIONS_EXTENSION_VERSION` valori per ogni versione principale per abilitare gli aggiornamenti automatici:

| Versione principale | Valore della proprietà `FUNCTIONS_EXTENSION_VERSION` |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Ogni modifica alla versione del runtime comporta il riavvio dell'app per le funzioni.

## <a name="view-and-update-the-current-runtime-version"></a>Visualizzare e aggiornare la versione corrente del runtime

È possibile modificare la versione del runtime usata dall'app per le funzioni. A causa del potenziale di modifiche di rilievo, è possibile modificare solo la versione di runtime prima di creare funzioni nell'app per le funzioni. 

> [!IMPORTANT]
> Anche se la versione del runtime è determinata dall'impostazione `FUNCTIONS_EXTENSION_VERSION`, è necessario apportare questa modifica nel portale di Azure e non modificando l'impostazione direttamente, perché il portale convalida le modifiche e apporta altre modifiche correlate in base alle esigenze.

### <a name="from-the-azure-portal"></a>Dal portale di Azure

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Utilizzando la portale di Azure, non è possibile modificare la versione di runtime per un'app per le funzioni che contiene già funzioni.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Dall'interfaccia della riga di comando di Azure

È anche possibile visualizzare e configurare `FUNCTIONS_EXTENSION_VERSION` dall'interfaccia della riga di comando di Azure.

>[!NOTE]
>Poiché le altre impostazioni potrebbero essere interessate dalla versione del runtime, è necessario modificare la versione nel portale. Il portale esegue automaticamente gli altri aggiornamenti necessari, ad esempio la versione di Node.js e lo stack di runtime, quando si modificano le versioni del runtime.  

Tramite l'interfaccia della riga di comando di Azure, visualizzare la versione corrente del runtime con il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

In questo codice sostituire `<function_app>` con il nome dell'app per le funzioni. Sostituire anche `<my_resource_group>` con il nome del gruppo di risorse per l'app per le funzioni. 

Verrà visualizzato `FUNCTIONS_EXTENSION_VERSION` nell'output seguente, troncato per maggiore chiarezza:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

È possibile aggiornare l'impostazione `FUNCTIONS_EXTENSION_VERSION` nell'app per le funzioni con il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Sostituire `<function_app>` con il nome dell'app per le funzioni. Sostituire anche `<my_resource_group>` con il nome del gruppo di risorse per l'app per le funzioni. Sostituire anche `<version>` con una versione valida del runtime 1.x oppure `~2` per la versione 2.x.

È possibile eseguire questo comando da [Azure Cloud Shell](../cloud-shell/overview.md) scegliendo **Prova** nell'esempio di codice precedente. È anche possibile usare l'[interfaccia della riga di comando di Azure in locale](/cli/azure/install-azure-cli) per eseguire questo comando dopo l'esecuzione del comando [az login](/cli/azure/reference-index#az-login) per eseguire l'accesso.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare il runtime 2.0 nell'ambiente di sviluppo locale](functions-run-local.md)

> [!div class="nextstepaction"]
> [Vedere le note sulla versione per le versioni del runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)
