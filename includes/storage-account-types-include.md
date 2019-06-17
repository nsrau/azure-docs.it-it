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
ms.openlocfilehash: d96e69fb526cff633c78e9ac8a1679762014cd4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133700"
---
Archiviazione di Azure offre diversi tipi di account di archiviazione. Ogni tipo supporta caratteristiche diverse e ha uno specifico modello di prezzi. È importante tenere in considerazione tali differenze prima di creare un account di archiviazione per determinare il tipo di account ottimale per le proprie applicazioni. I tipi di account di archiviazione sono i seguenti:

- **Account per utilizzo generico v2**: tipo di account di archiviazione Basic per BLOB, file, code e tabelle. Consigliato per la maggior parte degli scenari che usano Archiviazione di Azure.
- **Account per utilizzo generico v1**: tipo di account legacy per BLOB, file, code e tabelle. Laddove è possibile, preferire account per utilizzo generico v2.
- **Bloccare l'account di archiviazione blob**: Account di archiviazione solo BLOB con caratteristiche di prestazioni premium. Consigliato per gli scenari con frequenze di transazioni elevata, con oggetti più piccoli o la richiesta di latenza di archiviazione ridotti in modo coerente.
- **Gli account di archiviazione (anteprima) FileStorage**: Account di archiviazione "solo file" con caratteristiche di prestazioni premium. Consigliato per applicazioni a prestazioni elevate di scalabilità o enterprise.
- **Account di archiviazione BLOB**: Account di archiviazione solo BLOB. Laddove è possibile, preferire account per utilizzo generico v2.

Nella tabella seguente sono descritti i tipi di account di archiviazione e le relative funzionalità:

| Tipo di account di archiviazione | Servizi supportati                       | Livelli di prestazioni supportati      | Livelli di accesso supportati         | Opzioni di replica               | Modello di distribuzione<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Crittografia<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Utilizzo generico v2   | BLOB, file, coda, tabella e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Accesso frequente, ad accesso sporadico, archivio<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Gestione risorse             | Crittografato              |
| Utilizzo generico v1   | BLOB, file, coda, tabella e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Resource Manager, classica    | Crittografato              |
| Archiviazione blob in blocchi   | BLOB (solo BLOB in blocchi e BLOB di accodamento) | Premium                       | N/D                            | Archiviazione con ridondanza locale                               | Gestione risorse             | Crittografato              |
| FileStorage (anteprima)   | Solo i file | Premium                       | N/D                            | Archiviazione con ridondanza locale                               | Gestione risorse             | Crittografato              |
| Archiviazione BLOB         | BLOB (solo BLOB in blocchi e BLOB di accodamento) | Standard                      | Accesso frequente, ad accesso sporadico, archivio<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Gestione risorse             | Crittografato              |

<div id="deployment-model"><sup>1</sup>È consigliabile usare il modello di distribuzione Azure Resource Manager. Gli account di archiviazione che usano il modello di distribuzione classico possono comunque essere creati in alcune posizioni e gli account classici esistenti continuano a essere supportati. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Distribuzione Azure Resource Manager o classica: Comprendere i modelli di distribuzione e lo stato delle risorse</a>.</div>

<div id="encryption"><sup>2</sup>Tutti gli account di archiviazione sono crittografati tramite Crittografia del servizio di archiviazione per i dati inattivi. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Crittografia del servizio di archiviazione di Azure per dati inattivi</a>.</div>

<div id="archive"><sup>3</sup>Il livello archivio è disponibile solo a livello di singolo BLOB, non a livello di account di archiviazione. Possono essere archiviati solo i BLOB in blocchi e i BLOB di accodamento. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Archiviazione BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup>L'archiviazione con ridondanza della zona (ZRS) è disponibile solo per gli account di archiviazione standard per utilizzo generico v2. Per altre informazioni su ZRS, vedere <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">Archiviazione con ridondanza della zona: Applicazioni di Archiviazione di Azure a disponibilità elevata</a>. Per altre informazioni sulle altre opzioni di replica, vedere <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Replica di Archiviazione di Azure</a>.</div>

<div id="premium-performance"><sup>5</sup>prestazioni premium per gli account per utilizzo generico v1 e per utilizzo generico v2 sono disponibile per il blob del disco e di pagina.</div>
