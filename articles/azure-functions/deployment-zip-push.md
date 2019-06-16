---
title: Distribuzione push con file ZIP per Funzioni di Azure | Documentazione Microsoft
description: Per pubblicare Funzioni di Azure, usare le funzionalità di distribuzione con file ZIP del servizio di distribuzione Kudu.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: glenga
ms.openlocfilehash: 2762e5c4f2b67415a0e42e80a34ae5b34c57adc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62111199"
---
# <a name="zip-deployment-for-azure-functions"></a>Distribuzione con file ZIP per Funzioni di Azure

Questo articolo descrive come distribuire i file di progetto dell'app per le funzioni in Azure da un file ZIP (compresso). Vengono fornite informazioni su come eseguire una distribuzione push sia utilizzando l'interfaccia della riga di comando di Azure sia le API REST. [Strumenti di base di Funzioni di Azure](functions-run-local.md) usa anche le API di distribuzione quando pubblica un progetto locale in Azure.

Funzioni di Azure include l'intera gamma di opzioni di integrazione e distribuzione continua offerte dal Servizio app di Azure. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md).

Per velocizzare lo sviluppo, può risultare più semplice distribuire i file di progetto dell'app per le funzioni direttamente da un file con estensione zip. L'API della distribuzione con estensione zip prende il contenuto di un file con estensione zip e lo estrae nella cartella `wwwroot` dell'app per le funzioni. La distribuzione tramite file ZIP utilizza lo stesso servizio Kudu usato per le distribuzioni basate su integrazione continua, che include:

+ Eliminazione di file rimasti da distribuzioni precedenti.
+ Personalizzazione della distribuzione, inclusa l'esecuzione di script di distribuzione.
+ Log di distribuzione.
+ La funzione di sincronizzazione si attiva in un'app per le funzioni del [Piano A consumo](functions-scale.md).

Per altre informazioni, vedere le [informazioni di riferimento sulla distribuzione tramite file ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Requisiti di distribuzione con file ZIP

Il file ZIP usato per la distribuzione push deve contenere tutti i file necessari per eseguire la funzione.

>[!IMPORTANT]
> Quando si usa la distribuzione con file ZIP, vengono eliminati dall'app per le funzioni i file della distribuzione esistente che non sono presenti nel file ZIP.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Un'app per le funzioni include tutti i file e le cartelle nella directory `wwwroot`. Una distribuzione di file ZIP include il contenuto della directory `wwwroot`, ma non la directory stessa. Quando si distribuisce un progetto libreria di classi C# è necessario includere i file di libreria compilati e le dipendenze in una sottocartella `bin` nel pacchetto con estensione zip.

## <a name="download-your-function-app-files"></a>Scaricare i file dell'app per le funzioni

Quando lo sviluppo avviene in un computer locale, è facile creare un file ZIP della cartella del progetto dell'app per le funzioni nel computer di sviluppo.

È possibile, tuttavia, che le funzioni siano state create tramite l'editor nel portale di Azure. È possibile scaricare un progetto di app per le funzioni esistente in uno dei modi seguenti:

+ **Dal portale di Azure:**

  1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'app per le funzioni.

  2. Nella scheda **Panoramica** selezionare **Scarica contenuto dell'app**. Selezionare le opzioni di download, quindi **Scarica**.

      ![Scaricare il progetto dell'app per le funzioni](./media/deployment-zip-push/download-project.png)

     Il file ZIP scaricato è nel formato corretto per essere ripubblicato nell'app per le funzioni utilizzando la distribuzione push con file ZIP. Il download dal portale consente anche di aggiungere i file necessari per aprire l'app per le funzioni direttamente in Visual Studio.

+ **Tramite API REST:**

    Usare l'API GET di distribuzione seguente per scaricare i file dal progetto `<function_app>`: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Includendo `/site/wwwroot/` ci si assicura che il file ZIP contenga solo i file di progetto dell'app per le funzioni e non l'intero sito. Se l'accesso ad Azure non è ancora stato eseguito, verrà richiesto di farlo.  

È anche possibile scaricare un file ZIP da un repository GitHub. Quando si scarica un repository GitHub come file ZIP, GitHub aggiunge un livello di cartelle aggiuntivo per il ramo. Questo livello di cartelle aggiuntivo implica che non è possibile distribuire direttamente il file ZIP così come è stato scaricato da GitHub. Se si usa un repository GitHub per mantenere l'app per le funzioni, è necessario utilizzare l'[integrazione continua](functions-continuous-deployment.md) per distribuire l'app.  

## <a name="cli"></a>Eseguire la distribuzione usando l'interfaccia della riga di comando di Azure

Per attivare una distribuzione push, è possibile utilizzare l'interfaccia della riga di comando di Azure. Eseguire la distribuzione push di un file ZIP nell'app per le funzioni usando il comando [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip). Per usare questo comando, è necessario utilizzare l'interfaccia della riga di comando di Azure 2.0.21 o versione successiva. Per visualizzare la versione dell'interfaccia della riga di comando di Azure in uso, utilizzare il comando `az --version`.

Nel comando seguente sostituire il segnaposto `<zip_file_path>` con il percorso del file ZIP. Sostituire inoltre `<app_name>` con il nome univoco dell'app per le funzioni. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```

Questo comando distribuisce i file di progetto del file ZIP scaricato nell'app per le funzioni in Azure. Riavvia quindi l'app. Per visualizzare l'elenco delle distribuzioni per questa app per le funzioni, è necessario utilizzare le API REST.

Quando si utilizza l'interfaccia della riga di comando di Azure nel computer locale, `<zip_file_path>` è il percorso del file ZIP nel computer in uso. È anche possibile eseguire l'interfaccia della riga di comando di Azure in [Azure Cloud Shell](../cloud-shell/overview.md). Quando si utilizza Cloud Shell, è innanzitutto necessario caricare il file ZIP per la distribuzione nell'account File di Azure associato a Cloud Shell. In tal caso `<zip_file_path>` è il percorso di archiviazione utilizzato dall'account Cloud Shell. Per altre informazioni, vedere [Rendere persistenti i file in Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Eseguire le funzioni dal pacchetto di distribuzione

È anche possibile scegliere di eseguire le funzioni direttamente dal file di pacchetto di distribuzione. Questo metodo ignora il passaggio di distribuzione relativo alla copia dei file dal pacchetto alla directory `wwwroot` dell'app per le funzioni. Il file del pacchetto è invece montato dal runtime di funzioni e il contenuto della directory `wwwroot` diventa di sola lettura.  

La distribuzione ZIP si integra con questa funzionalità che può essere attivata impostando l'impostazione dell'app per le funzioni `WEBSITE_RUN_FROM_PACKAGE` su un valore di `1`. Per altre informazioni, vedere [Run your Functions from a deployment package file](run-functions-from-deployment-package.md) (Esecuzione di funzioni da un file di pacchetto di distribuzione).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
