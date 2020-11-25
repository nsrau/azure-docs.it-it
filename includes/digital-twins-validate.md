---
author: baanders
description: file di inclusione che descrive come convalidare i modelli di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028405"
---
> [!TIP]
> Dopo aver creato un modello, è consigliabile convalidare i modelli offline prima di caricarli nell'istanza di Azure Digital gemelli.

È disponibile un esempio indipendente dal linguaggio per la convalida dei documenti del modello per verificare che DTDL sia corretto prima di caricarlo nell'istanza. Si trova qui: [**DTDL validator Sample**](/samples/azure-samples/dtdl-validator/dtdl-validator).

* L'esempio di validator DTDL si basa su una libreria parser .NET DTDL, disponibile in NuGet come libreria lato client: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). È anche possibile usare direttamente la libreria per progettare una soluzione di convalida personalizzata. Quando si usa la libreria del parser, assicurarsi di usare una versione compatibile con la versione che esegue i dispositivi gemelli digitali di Azure. Attualmente, questa è la versione *3.12.4*.

Per ulteriori informazioni sull'esempio di validator e sulla libreria del parser, inclusi esempi di utilizzo, vedere [*procedura: analizzare e convalidare i modelli*](../articles/digital-twins/how-to-parse-models.md).