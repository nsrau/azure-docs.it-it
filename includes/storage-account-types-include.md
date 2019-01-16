---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/02/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b278cce8f885f31f9d59fd389261812e04e58405
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54066991"
---
Archiviazione di Azure offre tre tipi di account di archiviazione. Ogni tipo supporta caratteristiche diverse e ha uno specifico modello di prezzi. È importante tenere in considerazione tali differenze prima di creare un account di archiviazione per determinare il tipo di account ottimale per le proprie applicazioni. Di seguito sono elencati i tre tipi di account di archiviazione:

* **Account per utilizzo generico v2**: tipo di account di archiviazione Basic per BLOB, file, code e tabelle. Consigliato per la maggior parte degli scenari che usano Archiviazione di Azure.
* **Account per utilizzo generico v1**: tipo di account legacy per BLOB, file, code e tabelle. Laddove è possibile, preferire account per utilizzo generico v2.
* **Account di archiviazione BLOB**: Account di archiviazione solo BLOB. Laddove è possibile, preferire account per utilizzo generico v2. 

Nella tabella seguente sono descritti i tipi di account di archiviazione e le relative funzionalità:

| Tipo di account di archiviazione | Servizi supportati                       | Livelli di prestazioni supportati | Livelli di accesso supportati               | Opzioni di replica                                                | Modello di distribuzione<sup>1</sup>  | Crittografia<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| Utilizzo generico v2   | BLOB, file, coda, tabella e disco       | Standard, Premium           | Accesso frequente, accesso sporadico, archivio<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Gestione risorse | Crittografato  |
| Utilizzo generico v1   | BLOB, file, coda, tabella e disco       | Standard, Premium           | N/D                                  | LRS, GRS, RA-GRS                                                   | Resource Manager, classica  | Crittografato  |
| Archiviazione BLOB         | BLOB (solo BLOB in blocchi e BLOB di accodamento) | Standard                    | Accesso frequente, accesso sporadico, archivio<sup>3</sup>                            | LRS, GRS, RA-GRS                                                   | Gestione risorse  | Crittografato  |

<sup>1</sup>È consigliabile usare il modello di distribuzione Azure Resource Manager. Gli account di archiviazione che usano il modello di distribuzione classico possono comunque essere creati in alcune posizioni e gli account classici esistenti continuano a essere supportati. Per altre informazioni, vedere [Distribuzione Azure Resource Manager o classica: Comprendere i modelli di distribuzione e lo stato delle risorse](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>Tutti gli account di archiviazione sono crittografati tramite Crittografia del servizio di archiviazione per i dati inattivi. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>Il livello archivio è disponibile solo a livello di singolo BLOB, non a livello di account di archiviazione. Possono essere archiviati solo i BLOB in blocchi e i BLOB di accodamento. Per altre informazioni, vedere [Archiviazione BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>L'archiviazione con ridondanza della zona (ZRS) è disponibile solo per gli account di archiviazione standard per utilizzo generico v2. Per altre informazioni su ZRS, vedere [Archiviazione con ridondanza della zona: Applicazioni di Archiviazione di Azure a disponibilità elevata](../articles/storage/common/storage-redundancy-zrs.md). Per altre informazioni sulle altre opzioni di replica, vedere [Replica di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md).
