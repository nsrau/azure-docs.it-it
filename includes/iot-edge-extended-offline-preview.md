---
title: File di inclusione
description: iot edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264373"
---
## <a name="enabling-extended-offline-operation-preview"></a>Abilitazione delle operazioni offline estese (anteprima)
Con la [versione 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) del runtime di Edge, il dispositivo Edge e i dispositivi downstream che si connettono ad esso possono essere configurati per le operazioni offline estese. 

Con questa funzionalità, i moduli locali o i dispositivi downstream possono eseguire nuovamente l'autenticazione con il dispositivo Edge in base alle esigenze e comunicare tra loro usando messaggi e metodi anche quando sono disconnessi dall'hub IoT. Vedere questo [post di blog](https://aka.ms/iot-edge-offline) e questo [articolo concettuale](../articles/iot-edge/offline-capabilities.md) per maggiori dettagli e per l'ambito di questa funzionalità.

Per l'abilitazione delle operazioni offline estese in uno scenario gateway stabilire una relazione padre-figlio tra il dispositivo Edge e i dispositivi downstream che si connetteranno ad esso.

1. Nel pannello dei dettagli del dispositivo Edge nel portale dell'hub IoT fare clic sul pulsante **Manage Child Devices (preview)** (Gestisci dispositivi figlio (anteprima)) nella barra dei comandi superiore.

1. Fare clic sul pulsante **+ Aggiungi**.

1. Nell'elenco dei dispositivi selezionare i dispositivi figlio e usare la freccia destra per selezionare quelli da aggiungere come elementi figlio.

1. Fare clic su **OK** per confermare.

Il dispositivo Edge e i relativi dispositivi figlio sono ora abilitati per le operazioni offline estese.  