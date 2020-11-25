---
title: includere file
description: includere file
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f7b11f813232f593be3598a87b6f1ad7a57054b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023356"
---
| Nomi di dominio                  | Porte in uscita | Descrizione                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Richiesta dal runtime di integrazione self-hosted per la creazione interattiva. |
| `{datafactory}.{region}.datafactory.azure.net`<br> o `*.frontend.clouddatahub.net` | 443            | Richiesta dal runtime di integrazione self-hosted per connettersi al servizio Data Factory. <br>Per i nuovi data factory creati, trovare il nome di dominio completo nella chiave del runtime di integrazione self-hosted, nel formato {datafactory}.{region}.datafactory.azure.net. Per i data factory precedenti, se il nome di dominio completo non è visibile nella chiave di integrazione self-hosted, usare invece *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Richiesta dal runtime di integrazione self-hosted per il download degli aggiornamenti. Se l'aggiornamento automatico è stato disabilitato, è possibile evitare di configurare questo dominio. |
| `*.core.windows.net`          | 443            | Usata dal runtime di integrazione self-hosted per connettersi all'account di archiviazione di Azure quando si usa la funzionalità di [copia temporanea](../articles/data-factory/copy-activity-performance.md#staged-copy). |
| `*.database.windows.net`      | 1433           | Obbligatori solo quando si esegue la copia da o nel database SQL di Azure o in Azure Synapse Analytics, facoltativi in caso contrario. Usare la funzionalità di copia temporanea per copiare i dati nel database SQL di Azure o in Synapse Analytics senza aprire la porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obbligatori solo quando si esegue la copia da o in Azure Data Lake Store, facoltativi in caso contrario. |