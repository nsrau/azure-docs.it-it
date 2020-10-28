---
title: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Vedere come iniziare a usare l'interfaccia della riga di comando di Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5e4c49e7aea05b6f430860eb6975713f59ad8080
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635984"
---
# <a name="use-the-azure-digital-twins-cli"></a>Usare l'interfaccia della riga di comando di Gemelli digitali di Azure

Oltre a gestire l'istanza di Azure Digital Twins nell'portale di Azure, i dispositivi gemelli di Azure hanno un **set di comandi per l'interfaccia della riga di comando di [Azure](/cli/azure/what-is-azure-cli)** che è possibile usare per eseguire le operazioni più importanti con il servizio, tra cui:
* Gestione di un'istanza di dispositivi gemelli digitali di Azure
* Gestione dei modelli
* Gestione dei dispositivi gemelli digitali
* Gestione delle relazioni gemelle
* Configurazione degli endpoint
* Gestione delle [Route](concepts-route-events.md)
* Configurazione della [sicurezza](concepts-security.md) tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)

Il set di comandi è denominato **AZ DT** ed è parte dell' [estensione Azure Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension)della riga di comando di Azure. È possibile visualizzare l'elenco completo dei comandi e il relativo utilizzo come parte della documentazione di riferimento per il set di comandi `az iot` : [ *AZ DT* Command Reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest).

## <a name="uses-deploy-and-validate"></a>Usi (Deploy e Validate)

Oltre alla gestione generale dell'istanza, l'interfaccia della riga di comando è anche uno strumento utile per la distribuzione e la convalida.
* È possibile utilizzare i comandi del piano di controllo per rendere la distribuzione di una nuova istanza ripetibile o automatizzata.
* I comandi del piano dati possono essere usati per controllare rapidamente i valori nell'istanza e verificare che le operazioni siano state completate come previsto.

## <a name="get-the-command-set"></a>Ottenere il set di comandi

I comandi di Azure Digital Twins fanno parte dell' [estensione Azure Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension)della riga di comando di Azure (Azure-Internet), quindi attenersi alla procedura seguente per assicurarsi di avere l'estensione più recente `azure-iot` con i comandi **AZ DT** .

### <a name="cli-version-requirements"></a>Requisiti della versione CLI

Se si usa l'interfaccia della riga di comando di Azure con PowerShell, il pacchetto di estensione richiede che la versione dell'interfaccia della riga di comando di Azure sia **2.3.1** o successiva.

È possibile controllare la versione dell'interfaccia della riga di comando di Azure con questo comando CLI:
```azurecli
az --version
```

Per istruzioni su come installare o aggiornare l'interfaccia della riga di comando di Azure a una versione più recente, vedere [*installare l'interfaccia della*](/cli/azure/install-azure-cli)riga di comando di Azure.

### <a name="get-the-extension"></a>Ottenere l'estensione

È possibile assicurarsi di avere la versione più recente dell' `azure-iot` estensione con questi passaggi. È possibile eseguire questi comandi nell' [Azure cloud Shell](../cloud-shell/overview.md) o in un'interfaccia della riga di comando di [Azure locale](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare l'interfaccia della riga di comando e il set completo di comandi tramite la documentazione di riferimento:
* [Guida di riferimento al comando *AZ DT*](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)