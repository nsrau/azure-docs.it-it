---
title: includere il file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597826"
---
Le condivisioni file di Azure vengono distribuite in *account di archiviazione*, ovvero oggetti di livello superiore che rappresentano un pool condiviso di archiviazione. Questo pool di archiviazione può essere usato per distribuire più condivisioni file, oltre ad altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. Tutte le risorse di archiviazione distribuite in un account di archiviazione condividono i limiti che si applicano a tale account di archiviazione. Per visualizzare i limiti correnti per un account di archiviazione, vedere [obiettivi di scalabilità e prestazioni file di Azure](../articles/storage/files/storage-files-scale-targets.md).

Esistono due tipi principali di account di archiviazione che verranno usati per le distribuzioni di File di Azure: 
- **Account di archiviazione per utilizzo generico versione 2 (GPv2)**: gli account di archiviazione GPv2 consentono di distribuire condivisioni file di Azure in hardware standard o basato su disco rigido (HDD). Oltre a archiviare le condivisioni file di Azure, gli account di archiviazione GPv2 possono archiviare altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. 
- **Account di archiviazione filestorage**: gli account di archiviazione filestorage consentono di distribuire condivisioni file di Azure in hardware Premium/con stato solido basato su disco (basato su SSD). Gli account filestorage possono essere usati solo per archiviare le condivisioni file di Azure. non è possibile distribuire altre risorse di archiviazione (contenitori BLOB, code, tabelle e così via) in un account filestorage.

Esistono diversi altri tipi di account di archiviazione che possono essere inclusi nel portale di Azure, in PowerShell o nell'interfaccia della riga di comando. Due tipi di account di archiviazione, ovvero gli account di archiviazione BlockBlobStorage e BlobStorage, non possono contenere condivisioni file di Azure. Gli altri due tipi di account di archiviazione che possono essere visualizzati sono gli account di archiviazione per utilizzo generico versione 1 (utilizzo generico V1) e quelli classici, che possono contenere condivisioni file di Azure. Sebbene gli account di archiviazione utilizzo generico V1 e classici possano contenere condivisioni file di Azure, la maggior parte delle nuove funzionalità di File di Azure sono disponibili solo negli account di archiviazione GPv2 e filestorage. È quindi consigliabile usare gli account di archiviazione GPv2 e filestorage solo per le nuove distribuzioni e per aggiornare gli account di archiviazione utilizzo generico V1 e classici, se esistono già nell'ambiente in uso.  