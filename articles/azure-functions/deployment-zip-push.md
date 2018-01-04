---
title: ZIP di distribuzione push per le funzioni di Azure | Documenti Microsoft
description: "Utilizzare le funzionalità di distribuzione di file zip del servizio di distribuzione Kudu per pubblicare le funzioni di Azure."
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Distribuzione push ZIP per le funzioni di Azure 
In questo articolo viene descritto come distribuire i file di progetto app di funzione in Azure da un file ZIP (compresso). Informazioni su come eseguire una distribuzione push, mediante Azure CLI e tramite le API REST. 

Funzioni di Azure include l'intera gamma di opzioni di distribuzione e l'integrazione continua forniti dal servizio App di Azure. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md). 

Per l'iterazione più veloci durante lo sviluppo, è spesso più facile da distribuire i file di progetto app di funzione direttamente da un file compresso con estensione zip. Questa distribuzione di file con estensione zip utilizza lo stesso servizio Kudu che rafforza continua basata sull'integrazione con distribuzioni, tra cui:

+ Eliminazione dei file che sono stati lasciati da distribuzioni precedenti.
+ Personalizzazione di distribuzione, incluse l'esecuzione di script di distribuzione.
+ Registri di distribuzione.
+ La sincronizzazione di trigger di funzione in un [piano il consumo](functions-scale.md) funzione app.

Per ulteriori informazioni, vedere il [riferimento per la distribuzione push zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Requisiti di distribuzione del file con estensione zip
Il file ZIP che utilizzati per la distribuzione push deve contenere tutti i file di progetto nell'app di funzione, incluso il codice della funzione. 

>[!IMPORTANT]
> Quando si utilizza distribuzione push con estensione zip, vengono eliminati i file da una distribuzione esistente che non vengono trovati nel file zip dalla tua app di funzione.  

### <a name="function-app-folder-structure"></a>Struttura di cartelle dell'app (funzione)

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>Scaricare il progetto di app (funzione)

Quando si sviluppa in un computer locale, è facile creare un file con estensione zip della cartella di progetto app di funzione nel computer di sviluppo. 

Le funzioni, tuttavia, si potrebbero essere creata tramite l'editor nel portale di Azure. Per scaricare il progetto di app di funzione dal portale: 

1. Accedi al [portale di Azure](https://portal.azure.com)e quindi passare all'app di funzione.

2. Nel **Panoramica** , selezionare **scaricare il contenuto applicazione**. Selezionare le opzioni di download, quindi **scaricare**.     

    ![Scaricare il progetto di app (funzione)](./media/deployment-zip-push/download-project.png)

Il file ZIP scaricato è nel formato corretto per essere ripubblicati all'app di funzione utilizzando la distribuzione push con estensione zip.

È anche possibile scaricare un file ZIP da un repository di GitHub. Tenere presente che quando si scarica un repository GitHub come file con estensione zip, GitHub aggiunge un livello aggiuntivo di cartella per il ramo. Ciò significa livello cartella aggiuntive che non è possibile distribuire il file ZIP direttamente come si è scaricato da GitHub. Se si usa un repository di GitHub per mantenere l'app di funzione, è necessario utilizzare [integrazione continua](functions-continuous-deployment.md) per distribuire l'applicazione.  

## <a name="cli"></a>Distribuire mediante Azure CLI

Per attivare una distribuzione push, è possibile utilizzare CLI di Azure. Push distribuire un file con estensione zip all'app di funzione usando il [az functionapp distribuzione origine configurazione-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) comando. Per usare questo comando, è necessario utilizzare Azure CLI versione 2.0.21 o versione successiva. Per visualizzare la versione di CLI di Azure in uso, utilizzare il `az --version` comando.

Il comando seguente, sostituire il `<zip_file_path>` segnaposto con il percorso del file ZIP. Inoltre, non sostituire `<app_name>` con il nome univoco dell'app in funzione. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Questo comando consente di distribuire il file di progetto da file con estensione ZIP scaricato all'app di funzione in Azure. Quindi riavvia l'app. Per visualizzare l'elenco delle distribuzioni per questa app di funzione, è necessario utilizzare le API REST.

Quando si utilizza l'interfaccia CLI di Azure nel computer locale, `<zip_file_path>` è il percorso del file con estensione zip nel computer in uso. È anche possibile eseguire CLI di Azure in [Azure Cloud Shell](../cloud-shell/overview.md). Quando si utilizza la Shell Cloud, è innanzitutto necessario caricare il file ZIP di distribuzione per l'account di Azure file associata con la Shell di Cloud. In tal caso, `<zip_file_path>` è il percorso di archiviazione che utilizza l'account Cloud Shell. Per ulteriori informazioni, vedere [mantenere i file nella Shell di Cloud di Azure](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
