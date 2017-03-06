---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Eseguire il mapping di un dominio personalizzato a un&quot;App Web | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Eseguire il mapping di un dominio personalizzato a un&quot;App Web
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 09851d3f2b3cf33015e508aec6cdae613a747038
ms.openlocfilehash: b543cbfff4552efa3bafad04115106e4f6a09949
ms.lasthandoff: 02/27/2017

---

# <a name="map-a-custom-domain-to-a-web-app"></a>Eseguire il mapping di un dominio personalizzato a un'App Web

Questo script di esempio crea un'App Web nel servizio App con le relative risorse correlate e quindi esegue il mapping di `www.<yourdomain>` a essa. 

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il comando `az login` e di avere accesso alla pagina di configurazione DNS del registrar di dominio.

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Eseguire il mapping di un dominio personalizzato a un'App Web")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse, l'app del servizio app e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Consente di creare un piano di servizio app. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crea un'App Web di Azure. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Aggiorna un piano di servizio app per ridimensionare il piano tariffario. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Apre un'App Web di Azure in un browser. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../app-service-cli-samples.md).
