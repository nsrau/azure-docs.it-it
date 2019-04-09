---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d4f57eca89cbb68d61546c6d5ce5bcd04f9256e7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59286372"
---
Archiviazione di Azure offre diversi tipi di account di archiviazione. Ogni tipo supporta caratteristiche diverse e ha uno specifico modello di prezzi. È importante tenere in considerazione tali differenze prima di creare un account di archiviazione per determinare il tipo di account ottimale per le proprie applicazioni. I tipi di account di archiviazione sono i seguenti:

- **Account per utilizzo generico v2**: tipo di account di archiviazione Basic per BLOB, file, code e tabelle. Consigliato per la maggior parte degli scenari che usano Archiviazione di Azure.
- **Account per utilizzo generico v1**: tipo di account legacy per BLOB, file, code e tabelle. Laddove è possibile, preferire account per utilizzo generico v2.
- **Bloccare l'account di archiviazione blob**: Account di archiviazione solo BLOB con caratteristiche di prestazioni premium. Consigliato per gli scenari con frequenze di transazioni elevata, con oggetti più piccoli o la richiesta di latenza di archiviazione ridotti in modo coerente.
- **Gli account di archiviazione (anteprima) FileStorage**: Account di archiviazione "solo file" con caratteristiche di prestazioni premium. Consigliato per applicazioni a prestazioni elevate di scalabilità o enterprise.
- **Account di archiviazione BLOB**: Account di archiviazione solo BLOB. Laddove è possibile, preferire account per utilizzo generico v2.

Nella tabella seguente sono descritti i tipi di account di archiviazione e le relative funzionalità:

| Tipo di account di archiviazione | Servizi supportati                       | Livelli di prestazioni supportati      | Livelli di accesso supportati         | Opzioni di replica               | Modello di distribuzione<sup>1</sup> | Crittografia<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Utilizzo generico v2   | BLOB, file, coda, tabella e disco       | Standard, Premium<sup>5</sup> | Accesso frequente, accesso sporadico, archivio<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Gestione risorse             | Crittografato              |
| Utilizzo generico v1   | BLOB, file, coda, tabella e disco       | Standard, Premium<sup>5</sup> | N/D                            | LRS, GRS, RA-GRS                  | Resource Manager, classica    | Crittografato              |
| Archiviazione blob in blocchi   | BLOB (solo BLOB in blocchi e BLOB di accodamento) | Premium                       | N/D                            | Archiviazione con ridondanza locale                               | Gestione risorse             | Crittografato              |
| FileStorage (anteprima)   | Solo i file | Premium                       | N/D                            | Archiviazione con ridondanza locale                               | Gestione risorse             | Crittografato              |
| Archiviazione BLOB         | BLOB (solo BLOB in blocchi e BLOB di accodamento) | Standard                      | Accesso frequente, accesso sporadico, archivio<sup>3</sup> | LRS, GRS, RA-GRS                  | Gestione risorse             | Crittografato              |

<sup>1</sup>È consigliabile usare il modello di distribuzione Azure Resource Manager. Gli account di archiviazione che usano il modello di distribuzione classico possono comunque essere creati in alcune posizioni e gli account classici esistenti continuano a essere supportati. Per altre informazioni, vedere [Distribuzione Azure Resource Manager o classica: Comprendere i modelli di distribuzione e lo stato delle risorse](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>Tutti gli account di archiviazione sono crittografati tramite Crittografia del servizio di archiviazione per i dati inattivi. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>Il livello archivio è disponibile solo a livello di singolo BLOB, non a livello di account di archiviazione. Possono essere archiviati solo i BLOB in blocchi e i BLOB di accodamento. Per altre informazioni, vedere [Archiviazione BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>L'archiviazione con ridondanza della zona (ZRS) è disponibile solo per gli account di archiviazione standard per utilizzo generico v2. Per altre informazioni su ZRS, vedere [Archiviazione con ridondanza della zona: Applicazioni di Archiviazione di Azure a disponibilità elevata](../articles/storage/common/storage-redundancy-zrs.md). Per altre informazioni sulle altre opzioni di replica, vedere [Replica di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md).

<sup>5</sup> prestazioni premium per gli account per utilizzo generico v1 e per utilizzo generico v2 sono disponibile per il blob del disco e di pagina.