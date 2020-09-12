---
title: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Vedere come iniziare a usare l'interfaccia della riga di comando di Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2c642b2441d1f30c31e707a237732e028f548ac5
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298182"
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

I comandi di Azure Digital Twins fanno parte dell' [estensione Azure Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension)della riga di comando di Azure. È possibile visualizzare la documentazione di riferimento per questi comandi come parte del `az iot` set di comandi: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

È possibile assicurarsi di avere la versione più recente dell'estensione con questi passaggi. È possibile eseguire questi comandi nell' [Azure cloud Shell](../cloud-shell/overview.md) o in un'interfaccia della riga di comando di [Azure locale](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Passaggi successivi

Per un'alternativa ai comandi dell'interfaccia della riga di comando, vedere How to manage an Azure Digital Twins instance using APIs and SDKs:
* [*Procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md)
