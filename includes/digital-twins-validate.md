---
author: baanders
description: file di inclusione che descrive come convalidare i modelli di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: bccf92c6c912747e28b3075f12ac1558cced30c9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130382"
---
> [!TIP]
> Dopo aver creato un modello, è consigliabile convalidare i modelli offline prima di caricarli nell'istanza di Azure Digital gemelli.

È disponibile un esempio indipendente dal linguaggio per la convalida dei documenti del modello per verificare che DTDL sia corretto prima di caricarlo nell'istanza. Si trova qui: [**DTDL validator Sample**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

* L'esempio di validator DTDL si basa su una libreria parser .NET DTDL, disponibile in NuGet come libreria lato client: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). È anche possibile usare direttamente la libreria per progettare una soluzione di convalida personalizzata. Quando si usa la libreria del parser, assicurarsi di usare una versione compatibile con la versione che esegue i dispositivi gemelli digitali di Azure. Attualmente, questa è la versione *3.12.4* .

Per ulteriori informazioni sull'esempio di validator e sulla libreria del parser, inclusi esempi di utilizzo, vedere [*procedura: analizzare e convalidare i modelli*](../articles/digital-twins/how-to-parse-models.md).