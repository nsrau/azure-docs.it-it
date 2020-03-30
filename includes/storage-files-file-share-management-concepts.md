---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597826"
---
Le condivisioni file di Azure vengono distribuite *in account di archiviazione,* ovvero oggetti di primo livello che rappresentano un pool condiviso di archiviazione. Questo pool di archiviazione può essere usato per distribuire più condivisioni file, nonché altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. Tutte le risorse di archiviazione distribuite in un account di archiviazione condividono i limiti applicabili a tale account di archiviazione. Per visualizzare i limiti correnti di un account di archiviazione, vedere Obiettivi di [scalabilità e prestazioni di File](../articles/storage/files/storage-files-scale-targets.md)di Azure.

Esistono due tipi principali di account di archiviazione che verranno usati per le distribuzioni di File di Azure:There are two main types of storage accounts you will use for Azure Files deployments: 
- Account di **archiviazione generici versione 2 (GPv2):** gli account di archiviazione GPv2 consentono di distribuire condivisioni file di Azure in hardware basato su disco rigido/standard/HDD. Oltre a archiviare le condivisioni file di Azure, gli account di archiviazione GPv2 possono archiviare altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. 
- **Account di archiviazione fileStorage:** gli account di archiviazione FileStorage consentono di distribuire condivisioni file di Azure nell'hardware basato su disco premium/solid-state. Gli account FileStorage possono essere usati solo per archiviare condivisioni file di Azure.FileStorage accounts can only be used to store Azure file shares; non è possibile distribuire altre risorse di archiviazione (contenitori BLOB, code, tabelle e così via) in un account FileStorage.No other storage resources (blob containers, queues, tables, etc.) can be deployed in a FileStorage account.

Esistono diversi altri tipi di account di archiviazione che possono essere presenti nel portale di Azure, in PowerShell o nell'interfaccia della riga di comando. Due tipi di account di archiviazione, gli account di archiviazione BlockBlobStorage e BlobStorage non possono contenere condivisioni file di Azure.Two storage account types, BlockBlobStorage and BlobStorage storage accounts, cannot contain Azure file shares. Gli altri due tipi di account di archiviazione che possono essere visualizzati sono la versione generica 1 (GPv1) e gli account di archiviazione classici, entrambi con condivisioni file di Azure.The other two storage account types you may see are general purpose version 1 (GPv1) and classic storage accounts, both of which can contain Azure file shares. Anche se GPv1 e gli account di archiviazione classici possono contenere condivisioni file di Azure, la maggior parte delle nuove funzionalità di File di Azure sono disponibili solo negli account di archiviazione GPv2 e FileStorage.Although GPv1 and classic storage accounts may contain Azure file shares, most new features of Azure Files are available only in GPv2 and FileStorage storage accounts. È pertanto consigliabile usare solo gli account di archiviazione GPv2 e FileStorage per le nuove distribuzioni e aggiornare GPv1 e gli account di archiviazione classici se già presenti nell'ambiente.  