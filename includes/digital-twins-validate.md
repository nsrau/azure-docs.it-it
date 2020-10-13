---
author: baanders
description: file di inclusione che descrive come convalidare i modelli di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: fc7c31d1ab82e2e7edc74fb57233e8a0891fd113
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91974966"
---
> [!TIP]
> Dopo aver creato un modello, è consigliabile convalidare i modelli offline prima di caricarli nell'istanza di Azure Digital gemelli.

È disponibile un esempio indipendente dal linguaggio per la convalida dei documenti del modello per verificare che DTDL sia corretto. Si trova qui: [**DTDL validator Sample**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

L'esempio di validator DTDL si basa su una libreria parser .NET DTDL, disponibile in NuGet come libreria lato client: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). È anche possibile usare direttamente la libreria per progettare una soluzione di convalida personalizzata. Quando si usa la libreria del parser, assicurarsi di usare una versione compatibile con la versione che esegue i dispositivi gemelli digitali di Azure. Durante l'anteprima, si tratta della versione *3.12.4*.

Per ulteriori informazioni sull'esempio di validator e sulla libreria del parser, inclusi esempi di utilizzo, vedere [*procedura: analizzare e convalidare i modelli*](../articles/digital-twins/how-to-parse-models.md).