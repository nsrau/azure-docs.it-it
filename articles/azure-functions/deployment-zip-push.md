---
title: Distribuzione push con file ZIP per Funzioni di Azure | Documentazione Microsoft
description: "Per pubblicare Funzioni di Azure, usare le funzionalità di distribuzione con file ZIP del servizio di distribuzione Kudu."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/06/2017
ms.author: glenga
ms.openlocfilehash: faddb73522200f60f18294dc43e8d235943f8bbb
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Distribuzione push con file ZIP per Funzioni di Azure 
Questo articolo descrive come distribuire i file di progetto dell'app per le funzioni in Azure da un file ZIP (compresso). Vengono fornite informazioni su come eseguire una distribuzione push sia utilizzando l'interfaccia della riga di comando di Azure sia le API REST. 

Funzioni di Azure include l'intera gamma di opzioni di integrazione e distribuzione continua offerte dal Servizio app di Azure. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md). 

Per velocizzare l'iterazione durante lo sviluppo, è spesso più facile distribuire i file di progetto dell'app per le funzioni direttamente da un file ZIP compresso. La distribuzione tramite file ZIP utilizza lo stesso servizio Kudu usato per le distribuzioni basate su integrazione continua, che include:

+ Eliminazione di file rimasti da distribuzioni precedenti.
+ Personalizzazione della distribuzione, inclusa l'esecuzione di script di distribuzione.
+ Log di distribuzione.
+ La funzione di sincronizzazione si attiva in un'app per le funzioni del [Piano A consumo](functions-scale.md).

Per altre informazioni, vedere le [informazioni di riferimento sulla distribuzione push tramite file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Requisiti di distribuzione con file ZIP
Il file ZIP utilizzato per la distribuzione push deve contenere tutti i file di progetto nell'app per le funzioni, incluso il codice funzione. 

>[!IMPORTANT]
> Quando si utilizza la distribuzione push con file ZIP, vengono eliminati dall'app per le funzioni i file della distribuzione esistente che non sono presenti nel file ZIP.  

### <a name="function-app-folder-structure"></a>Struttura di cartelle dell'app per le funzioni

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>Scaricare il progetto dell'app per le funzioni

Quando lo sviluppo avviene in un computer locale, è facile creare un file ZIP della cartella del progetto dell'app per le funzioni nel computer di sviluppo. 

È possibile, tuttavia, che le funzioni siano state create tramite l'editor nel portale di Azure. Per scaricare il progetto dell'app per le funzioni dal portale: 

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'app per le funzioni.

2. Nella scheda **Panoramica** selezionare **Scarica contenuto dell'app**. Selezionare le opzioni di download, quindi **Scarica**.     

    ![Scaricare il progetto dell'app per le funzioni](./media/deployment-zip-push/download-project.png)

Il file ZIP scaricato è nel formato corretto per essere ripubblicato nell'app per le funzioni utilizzando la distribuzione push con file ZIP.

È anche possibile scaricare un file ZIP da un repository GitHub. Tenere presente che quando si scarica un repository GitHub come file ZIP, GitHub aggiunge un livello di cartelle aggiuntivo per il ramo. Questo livello di cartelle aggiuntivo implica che non è possibile distribuire direttamente il file ZIP così come è stato scaricato da GitHub. Se si usa un repository GitHub per mantenere l'app per le funzioni, è necessario utilizzare l'[integrazione continua](functions-continuous-deployment.md) per distribuire l'app.  

## <a name="cli"></a>Eseguire la distribuzione usando l'interfaccia della riga di comando di Azure

Per attivare una distribuzione push, è possibile utilizzare l'interfaccia della riga di comando di Azure. Eseguire la distribuzione push di un file ZIP nell'app per le funzioni usando il comando [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip). Per usare questo comando, è necessario utilizzare l'interfaccia della riga di comando di Azure 2.0.21 o versione successiva. Per visualizzare la versione dell'interfaccia della riga di comando di Azure in uso, utilizzare il comando `az --version`.

Nel comando seguente sostituire il segnaposto `<zip_file_path>` con il percorso del file ZIP. Sostituire inoltre `<app_name>` con il nome univoco dell'app per le funzioni. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Questo comando distribuisce i file di progetto del file ZIP scaricato nell'app per le funzioni in Azure. Riavvia quindi l'app. Per visualizzare l'elenco delle distribuzioni per questa app per le funzioni, è necessario utilizzare le API REST.

Quando si utilizza l'interfaccia della riga di comando di Azure nel computer locale, `<zip_file_path>` è il percorso del file ZIP nel computer in uso. È anche possibile eseguire l'interfaccia della riga di comando di Azure in [Azure Cloud Shell](../cloud-shell/overview.md). Quando si utilizza Cloud Shell, è innanzitutto necessario caricare il file ZIP per la distribuzione nell'account File di Azure associato a Cloud Shell. In tal caso `<zip_file_path>` è il percorso di archiviazione utilizzato dall'account Cloud Shell. Per altre informazioni, vedere [Rendere persistenti i file in Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
