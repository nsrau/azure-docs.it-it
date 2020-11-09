---
title: includere file
description: includere file
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 79a8bc73f416c8d10d83e7ad94a727094f072b00
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331708"
---
| Nomi di dominio                  | Porte in uscita | Descrizione                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Richiesta dal runtime di integrazione self-hosted per la creazione interattiva. |
| `{datafactory}.{region}.datafactory.azure.net`<br> o `*.frontend.clouddatahub.net` | 443            | Richiesta dal runtime di integrazione self-hosted per connettersi al servizio Data Factory. <br>Per i nuovi data factory creati, trovare il nome di dominio completo nella chiave del runtime di integrazione self-hosted, nel formato {datafactory}.{region}.datafactory.azure.net. Per i data factory precedenti, se il nome di dominio completo non è visibile nella chiave di integrazione self-hosted, usare invece *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Richiesta dal runtime di integrazione self-hosted per il download degli aggiornamenti. Se l'aggiornamento automatico è stato disabilitato, è possibile evitare di configurare questo dominio. |
| `*.core.windows.net`          | 443            | Usata dal runtime di integrazione self-hosted per connettersi all'account di archiviazione di Azure quando si usa la funzionalità di [copia temporanea](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Obbligatori solo quando si esegue la copia da o nel database SQL di Azure o in Azure Synapse Analytics, facoltativi in caso contrario. Usare la funzionalità di copia temporanea per copiare i dati nel database SQL di Azure o in Synapse Analytics senza aprire la porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obbligatori solo quando si esegue la copia da o in Azure Data Lake Store, facoltativi in caso contrario. |
