---
title: Come specificare le versioni del runtime per Funzioni di Azure | Microsoft Docs
description: "La soluzione Funzioni supporta più versioni del runtime. Informazioni su come specificare la versione del runtime di un'app per le funzioni ospitata in Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Come specificare le versioni del runtime per Funzioni di Azure

La versione del runtime della soluzione Funzioni di Azure implementa l'esecuzione senza server del codice in Azure. Questa versione del runtime è disponibile in vari ambienti diversi da quella ospitata in Azure. Gli [strumenti di base di Funzioni di Azure](functions-run-local.md) implementano questa versione del runtime nel computer di sviluppo. Il [runtime di Funzioni di Azure](functions-runtime-overview.md) consente di usare la soluzione Funzioni in ambienti locali. 

La soluzione Funzioni supporta più versioni principali del runtime. Un aggiornamento per le versioni principali può introdurre modifiche di rilievo. In questo argomento viene descritto come specificare le app per le funzioni per una versione del runtime specifica ospitata in Azure. 

La soluzione Funzioni consente di indicare una versione principale specifica del runtime usando l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione nell'app per le funzioni. Questo approccio è applicabile alle versioni pubbliche e di anteprima. L'app per le funzioni viene mantenuta nella versione di runtime principale fino a quando non si sceglie esplicitamente di spostarla in una nuova versione. L'app per le funzioni viene aggiornata a nuove versioni secondarie minori del runtime non appena risultano disponibili. Gli aggiornamenti delle versioni secondarie non introducono modifiche di rilievo.  

Quando una nuova versione principale risulta disponibile pubblicamente, si ha la possibilità di passare a tale versione quando si visualizza l'app per le funzioni nel portale. Dopo il passaggio a una nuova versione, è sempre possibile usare l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione per tornare a una versione precedente del runtime.

Ogni modifica alla versione del runtime comporta il riavvio dell'app per le funzioni. Le note sulla versione per tutte le versioni del runtime (principali e secondarie) vengono pubblicate nel [repository GitHub](https://github.com/Azure/azure-webjobs-sdk-script/releases).   
## <a name="view-the-current-runtime-version"></a>Visualizzare la versione corrente del runtime

Seguire questa procedura per visualizzare la versione specifica del runtime usata attualmente dall'app per le funzioni. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'app per le funzioni e in **Funzionalità configurate** scegliere **Impostazioni dell'app per le funzioni**. 

    ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-versions/add-update-app-setting.png)

2. Nella scheda **Impostazioni dell'app per le funzioni** individuare la **Versione runtime**. Notare la versione specifica del runtime e la versione principale richiesta. Nell'esempio seguente la versione principale è impostata su `~1.0`.
 
   ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>Specificare come destinazione la versione 2.0 del runtime di Funzioni

>[!IMPORTANT]   
> Il runtime 2.0 di Funzioni di Azure è disponibile in anteprima e attualmente non tutte le funzionalità di Funzioni di Azure sono supportate. Per altre informazioni, vedere [Problemi noti del runtime 2.0 di Funzioni di Azure](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

È possibile spostare l'app per le funzioni alla versione 2.0 di anteprima del runtime nel portale di Azure. Nella scheda **Impostazioni dell'app per le funzioni** scegliere **beta** in **Versione runtime**.  

   ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-versions/function-app-view-version.png)

Questa impostazione equivale a configurare l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione su `beta`. Scegliere il pulsante **~1** per tornare alla versione principale supportata pubblicamente corrente. È anche possibile usare l'interfaccia della riga di comando di Azure per aggiornare questa impostazione dell'applicazione. 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>Specificare come destinazione una versione specifica del runtime dal portale

Quando è necessario specificare come destinazione una versione principale diversa dalla versione principale corrente o dalla versione 2.0, è necessario configurare l'impostazione `FUNCTIONS_EXTENSION_VERSION` dell'applicazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'app per le funzioni e in **Funzionalità configurate** scegliere **Impostazioni dell'applicazione**.

    ![Selezionare Impostazioni dell'app per le funzioni](./media/functions-versions/add-update-app-setting1a.png)

2. Nella scheda **Impostazioni dell'applicazione** trovare l'impostazione `FUNCTIONS_EXTENSION_VERSION` e modificare il valore specificando una versione principale valida del runtime 1.x oppure `beta` per la versione 2.0. 

    ![Configurare l'impostazione dell'app per le funzioni](./media/functions-versions/add-update-app-setting2.png)

3. Fare clic su **Salva** per salvare l'aggiornamento dell'impostazione applicazione. 

## <a name="target-a-specific-version-using-azure-cli"></a>Specificare come destinazione una versione specifica tramite l'interfaccia della riga di comando di Azure

 È anche possibile configurare `FUNCTIONS_EXTENSION_VERSION` dall'interfaccia della riga di comando di Azure. Usare l'interfaccia della riga di comando di Azure per aggiornare l'impostazione dell'applicazione nell'app per le funzioni con il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
In questo codice sostituire `<function_app>` con il nome dell'app per le funzioni. Sostituire anche `<my_resource_group>` con il nome del gruppo di risorse per l'app per le funzioni. Sostituire `<version>` con una versione principale valida del runtime 1.x oppure `beta` per la versione 2.0. 

È possibile eseguire questo comando da [Azure Cloud Shell](../cloud-shell/overview.md) scegliendo **Prova** nell'esempio di codice precedente. È anche possibile usare l'[interfaccia della riga di comando di Azure in locale](/cli/azure/install-azure-cli) per eseguire questo comando dopo l'esecuzione del comando [az login](/cli/azure#az_login) per eseguire l'accesso.
