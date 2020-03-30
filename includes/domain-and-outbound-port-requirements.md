---
title: File di inclusione
description: File di inclusione
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559316"
---
| Nomi di dominio                  | Porte in uscita | Descrizione                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Richiesto dal runtime di integrazione self-hosted per connettersi ai servizi di spostamento dati in Azure Data Factory.Required by the self-hosted integration runtime to connect to data movement services in Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Richiesta dal runtime di integrazione self-hosted per connettersi al servizio Data Factory. |
| `download.microsoft.com`    | 443            | Richiesta dal runtime di integrazione self-hosted per il download degli aggiornamenti. Se è stato disabilitato l'aggiornamento automatico, è possibile ignorare la configurazione di questo dominio. |
| `*.core.windows.net`          | 443            | Usata dal runtime di integrazione self-hosted per connettersi all'account di archiviazione di Azure quando si usa la funzionalità di [copia temporanea](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Obbligatorio solo quando si copia da o nel database SQL di Azure o nel database SQL di Azure o in Azure SQL Data Warehouse e facoltativa in caso contrario. Utilizzare la funzionalità di copia in fasi per copiare i dati nel database SQL o in SQL Data Warehouse senza aprire la porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obbligatorio solo quando si copia da o in Azure Data Lake Store e facoltativo in caso contrario. |
