---
title: includere il file
description: includere file
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74559316"
---
| Nomi di dominio                  | Porte in uscita | Descrizione                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Richiesto dal runtime di integrazione self-hosted per connettersi ai servizi di spostamento dei dati in Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Richiesta dal runtime di integrazione self-hosted per connettersi al servizio Data Factory. |
| `download.microsoft.com`    | 443            | Richiesta dal runtime di integrazione self-hosted per il download degli aggiornamenti. Se è stato disabilitato l'aggiornamento automatico, è possibile ignorare la configurazione di questo dominio. |
| `*.core.windows.net`          | 443            | Usata dal runtime di integrazione self-hosted per connettersi all'account di archiviazione di Azure quando si usa la funzionalità di [copia temporanea](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Obbligatorio solo quando si esegue la copia da o nel database SQL di Azure o Azure SQL Data Warehouse e in caso contrario facoltativo. Usare la funzionalità di copia temporanea per copiare i dati nel database SQL o SQL Data Warehouse senza aprire la porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obbligatorio solo quando si esegue la copia da o a Azure Data Lake Store e facoltativo in caso contrario. |
