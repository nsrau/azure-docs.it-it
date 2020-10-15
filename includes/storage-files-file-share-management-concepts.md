---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8d26b6c92462abc13c42257d2c6e571156eacc0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563447"
---
Le condivisioni file di Azure vengono distribuite in *account di archiviazione*, ovvero oggetti di primo livello che rappresentano un pool di archiviazione condiviso. Questo pool di archiviazione condiviso può essere usato per distribuire più condivisioni file, nonché altre risorse di archiviazione, come contenitori BLOB, code o tabelle. Tutte le risorse di archiviazione distribuite in un account di archiviazione condividono i limiti applicabili a tale account. Per visualizzare i limiti correnti per un account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per File di Azure](../articles/storage/files/storage-files-scale-targets.md).

Sono due i tipi principali di account di archiviazione che verranno usati per le distribuzioni di File di Azure: 
- **Account di archiviazione per utilizzo generico v2**: gli account di archiviazione per utilizzo generico v2 consentono di distribuire condivisioni file di Azure in hardware standard o basato su disco rigido (HDD). Oltre a archiviare le condivisioni file di Azure, gli account di archiviazione per utilizzo generico v2 possono archiviare altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. 
- **Account di archiviazione FileStorage**: gli account di archiviazione FileStorage consentono di distribuire condivisioni file di Azure in hardware Premium o basato su unità SSD. Gli account FileStorage possono essere usati solo per archiviare le condivisioni file di Azure. Non è infatti possibile distribuire altre risorse di archiviazione (contenitori BLOB, code, tabelle e così via) in un account FileStorage. Solo gli account FileStorage possono distribuire sia condivisioni file SMB che NFS.

Esistono diversi altri tipi di account di archiviazione che è possibile trovare nel portale di Azure, in PowerShell o nell'interfaccia della riga di comando. Due tipi di account di archiviazione, ovvero gli account di archiviazione BlockBlobStorage e BlobStorage, non possono contenere condivisioni file di Azure. Gli altri due tipi di account di archiviazione disponibili sono gli account di archiviazione per utilizzo generico v1 e quelli classici, che possono contenere entrambi condivisioni file di Azure. Anche se gli account di archiviazione classici e per utilizzo generico v1 possono contenere condivisioni file di Azure, la maggior parte delle nuove funzionalità di File di Azure è disponibile solo negli account di archiviazione per utilizzo generico v2 e FileStorage. È quindi consigliabile usare solo account di archiviazione per utilizzo generico v2 e FileStorage per le nuove distribuzioni e per aggiornare account di archiviazione per utilizzo generico v1 e classici, se esistono già nell'ambiente corrente.  