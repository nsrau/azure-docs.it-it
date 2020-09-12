---
title: Dimensioni della cartella TEMP predefinita ridotte per un ruolo | Documentazione Microsoft
description: Un ruolo del servizio cloud dispone di una quantità limitata di spazio per la cartella TEMP. Questo articolo fornisce alcuni suggerimenti su come evitare l'esaurimento dello spazio.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 77c4f627668b91c4bc21ad73b3511c1775943229
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460202"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Dimensioni della cartella TEMP predefinita ridotte per un ruolo di lavoro/Web del servizio cloud
La directory temporanea predefinita di un ruolo Web o di lavoro del servizio cloud ha una dimensione massima di 100 MB, che può esaurirsi. Questo articolo descrive come evitare l'esaurimento dello spazio della directory temporanea.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Perché ho esaurito lo spazio?
Le variabili di ambiente Windows standard, TEMP e TMP, sono disponibili per il codice in esecuzione nell'applicazione. Sia TEMP che TMP puntano a una singola directory con una dimensione massima di 100 MB. Tutti i dati archiviati in questa directory non sono persistenti nel ciclo di vita del servizio cloud. Se le istanze del ruolo in un servizio cloud vengono riciclate, la directory viene pulita.

## <a name="suggestion-to-fix-the-problem"></a>Suggerimento per risolvere il problema
Implementare una delle alternative seguenti:

* Configurare una risorsa di archiviazione locale e accedervi direttamente invece che usando TEMP o TMP. Per accedere a una risorsa di archiviazione locale dal codice in esecuzione nell'applicazione, chiamare il metodo [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .
* Configurare una risorsa di archiviazione locale e definire le directory TEMP e TMP in modo che puntino al percorso della risorsa di archiviazione locale. Questa modifica deve essere eseguita nel metodo [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) .

L'esempio di codice seguente illustra come modificare le directory di destinazione per TEMP e TMP nel metodo OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Vedere il blog [How to increase the size of the Windows Azure Web Role ASP.NET Temporary Folder](https://docs.microsoft.com/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder)(Come aumentare le dimensioni della cartella temporanea ASP.NET del ruolo Web di Azure).

Altri [articoli sulla risoluzione dei problemi](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) per i servizi cloud.

Per informazioni su come risolvere i problemi dei ruoli del servizio cloud usando i dati di diagnostica del computer Azure PaaS, vedere la [serie di blog di Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
