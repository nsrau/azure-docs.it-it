---
title: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Vedere come iniziare a usare l'interfaccia della riga di comando di Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 65462937db4711066bdb8b31cc22de508321d701
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950908"
---
# <a name="use-the-azure-digital-twins-cli"></a>Usare l'interfaccia della riga di comando di Gemelli digitali di Azure

Oltre a gestire l'istanza di Azure Digital Twins nell'portale di Azure, i dispositivi gemelli digitali di Azure hanno un' **interfaccia della riga di comando** che è possibile usare per eseguire le operazioni più importanti con il servizio, tra cui:
* Gestione di un'istanza di dispositivi gemelli digitali di Azure
* Gestione dei modelli
* Gestione dei dispositivi gemelli digitali
* Gestione delle relazioni gemelle
* Configurazione degli endpoint
* Gestione delle [Route](concepts-route-events.md)
* Configurazione della [sicurezza](concepts-security.md) tramite il controllo degli accessi in base al ruolo (RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Usi (Deploy e Validate)

Oltre alla gestione generale dell'istanza, l'interfaccia della riga di comando è anche uno strumento utile per la distribuzione e la convalida.
* È possibile utilizzare i comandi del piano di controllo per rendere la distribuzione di una nuova istanza ripetibile o automatizzata.
* I comandi del piano dati possono essere usati per controllare rapidamente i valori nell'istanza e verificare che le operazioni siano state completate come previsto.

## <a name="get-the-extension"></a>Ottenere l'estensione

I comandi di Azure Digital Twins fanno parte dell' [estensione Azure Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension)della riga di comando di Azure. È possibile visualizzare l'elenco completo dei comandi e il relativo utilizzo come parte della documentazione di riferimento per il set di comandi `az iot` : [ *AZ DT* Command Reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true).

È possibile assicurarsi di avere la versione più recente dell'estensione con questi passaggi. È possibile eseguire questi comandi nell' [Azure cloud Shell](../cloud-shell/overview.md) o in un'interfaccia della riga di comando di [Azure locale](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare l'interfaccia della riga di comando e il set completo di comandi tramite la documentazione di riferimento:
* [Guida di riferimento al comando *AZ DT*](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
