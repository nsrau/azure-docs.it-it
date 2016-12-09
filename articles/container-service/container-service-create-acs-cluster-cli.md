---
title: Distribuire un cluster del servizio contenitore di Azure con l&quot;interfaccia della riga di comando | Documentazione Microsoft
description: Distribuire un cluster del servizio contenitore di Azure usando la versione 2.0 (anteprima) dell&quot;interfaccia della riga di comando di Azure
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>Uso della versione 2.0 (anteprima) dell'interfaccia della riga di comando di Azure per creare un cluster del servizio contenitore di Azure

Per creare un cluster del servizio contenitore di Azure sono necessari questi elementi:
* Un account Azure (è possibile [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/))
* Aver installato la [versione 2.0 dell'interfaccia della riga di comando di Azure (anteprima)](https://github.com/Azure/azure-cli#installation)
* Connessione all'account Azure (vedere più avanti)

## <a name="log-in-to-your-account"></a>Accedere all'account
```azurecli
az login 
```
È necessario selezionare questo [collegamento](https://login.microsoftonline.com/common/oauth2/deviceauth) per eseguire l'autenticazione con il codice di dispositivo fornito nell'interfaccia della riga di comando.

![Comando type](media/container-service-create-acs-cluster-cli/login.png)

![browser](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
```azurecli
az resource group create -n acsrg1 -l "westus"
```

![Immagine della creazione del gruppo di risorse](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>Elenco di comandi disponibili dell'interfaccia della riga di comando del servizio contenitore di Azure

```azurecli
az acs -h
```

![Utilizzo del comando del servizio contenitore di Azure](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>Creare un cluster del servizio contenitore di Azure

*Utilizzo del comando create del servizio contenitore di Azure nell'interfaccia della riga di comando*

```azurecli
az acs create -h
```
Il nome del servizio contenitore, il gruppo di risorse create nel passaggio precedente e un nome DNS univoco sono obbligatori. Altri input vengono impostati sui valori predefiniti (vedere la schermata seguente con lo snapshot della Guida), a meno che non vengano sovrascritti usando le rispettive opzioni.
![Immagine della Guida per il comando create del servizio contenitore di Azure](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

*Creazione rapida del servizio contenitore di Azure con impostazioni predefinite. Se non è disponibile una chiave SSH, usare il secondo comando. Il secondo comando create con l'opzione --generate-ssh-keys creerà automaticamente un cluster*

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

*Assicurarsi che dns-prefix (opzione -d) sia univoco. Se viene visualizzato un errore, riprovare con una stringa univoca.*

Dopo avere digitato il comando precedente, attendere circa 10 minuti per il completamento della creazione del cluster.

![Immagine del comando create del servizio contenitore di Azure](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>Elencare i cluster del servizio contenitore di Azure 

### <a name="under-a-subscription"></a>In una sottoscrizione

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>In un gruppo di risorse specifico

```azurecli
az acs list -g acsrg1 --output table
```

![Immagine dell'elenco del servizio contenitore di Azure](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>Visualizzare i dettagli di un cluster del servizio contenitore

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![Immagine dell'elenco del servizio contenitore di Azure](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>Ridimensionare il cluster del servizio contenitore di Azure
*È consentito sia ridurre che aumentare il numero di istanze. Il parametro new-agent-count è il nuovo numero di agenti nel cluster del servizio contenitore di Azure.*

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![Immagine del ridimensionamento del servizio contenitore di Azure](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Eliminare un cluster del servizio contenitore
```
az acs delete -g acsrg1 -n acs-cluster 
```
*Si noti che il comando di eliminazione non elimina tutte le risorse (di rete e di archiviazione) create durante la creazione del servizio contenitore. Per eliminare tutte le risorse, è consigliabile creare un singolo cluster del servizio contenitore di Azure per ogni gruppo di risorse e quindi eliminare il gruppo di risorse stesso quando il cluster del servizio contenitore di Azure non è più necessario, in modo da assicurare che tutte le risorse correlate vengano eliminate e non venga applicato alcun addebito.*



<!--HONumber=Nov16_HO3-->


