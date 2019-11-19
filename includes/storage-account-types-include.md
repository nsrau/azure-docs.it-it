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
ms.openlocfilehash: 0c0f1f4dfd873c8c9a18d300b249ace0295e450e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174031"
---
Archiviazione di Azure offre diversi tipi di account di archiviazione. Ogni tipo supporta caratteristiche diverse e ha uno specifico modello di prezzi. È importante tenere in considerazione tali differenze prima di creare un account di archiviazione per determinare il tipo di account ottimale per le proprie applicazioni. I tipi di account di archiviazione sono i seguenti:

- **Account per utilizzo generico V2**: tipo di account di archiviazione di base per BLOB, file, code e tabelle. Consigliato per la maggior parte degli scenari che usano Archiviazione di Azure.
- **Account per utilizzo generico V1**: tipo di account legacy per BLOB, file, code e tabelle. Laddove è possibile, preferire account per utilizzo generico v2.
- **Account BlockBlobStorage**: account di archiviazione solo BLOB con caratteristiche di prestazioni Premium. Consigliato per scenari con tariffe di transazioni elevate, uso di oggetti più piccoli o che richiedono una latenza di archiviazione costantemente bassa.
- **Account filestorage**: account di archiviazione solo file con caratteristiche di prestazioni Premium. Consigliato per applicazioni di livello aziendale o a prestazioni elevate.
- **Account BlobStorage**: account di archiviazione solo BLOB legacy. Laddove è possibile, preferire account per utilizzo generico v2.

Nella tabella seguente sono descritti i tipi di account di archiviazione e le relative funzionalità:

| Tipo di account di archiviazione | Servizi supportati                       | Livelli di prestazioni supportati      | Livelli di accesso supportati         | Opzioni di replica               | Modello di distribuzione<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Crittografia<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Utilizzo generico v2   | BLOB, file, code, tabelle, dischi e Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Frequente, ad accesso sporadico, archivio<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | CON ridondanza locale, GRS, RA-GRS, ZRS, GZRS (anteprima), RA-GZRS (anteprima)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Gestione risorse             | Crittografato              |
| Utilizzo generico v1   | BLOB, file, coda, tabella e disco       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/D                            | LRS, GRS, RA-GRS                  | Resource Manager, classica    | Crittografato              |
| BlockBlobStorage   | BLOB (solo BLOB in blocchi e BLOB di accodamento) | Premium                       | N/D                            | CON RIDONDANZA LOCALE, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Gestione risorse             | Crittografato              |
| FileStorage   | Solo file | Premium                       | N/D                            | CON RIDONDANZA LOCALE, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Gestione risorse             | Crittografato              |
| BlobStorage         | BLOB (solo BLOB in blocchi e BLOB di accodamento) | Standard                      | Frequente, ad accesso sporadico, archivio<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Gestione risorse             | Crittografato              |

<div id="deployment-model"><sup>1</sup>È consigliabile usare il modello di distribuzione Azure Resource Manager. Gli account di archiviazione che usano il modello di distribuzione classico possono comunque essere creati in alcune posizioni e gli account classici esistenti continuano a essere supportati. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Confronto tra distribuzione di Azure Resource Manager e classica: comprensione dei modelli di implementazione e dello stato delle risorse</a>.</div>

<div id="encryption"><sup>2</sup>Tutti gli account di archiviazione sono crittografati tramite Crittografia del servizio di archiviazione per i dati inattivi. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Crittografia del servizio di archiviazione di Azure per dati inattivi</a>.</div>

<div id="archive"><sup>3</sup>Il livello archivio è disponibile solo a livello di singolo BLOB, non a livello di account di archiviazione. Possono essere archiviati solo i BLOB in blocchi e i BLOB di accodamento. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> Archiviazione con ridondanza della zona (ZRS) e archiviazione con ridondanza della zona geografica (GZRS/RA-GZRS) (anteprima) sono disponibili solo per gli account standard per utilizzo generico V2, BlockBlobStorage e filestorage in determinate aree. Per altre informazioni sull'archiviazione con ridondanza della zona, vedere <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">Archiviazione con ridondanza della zona: applicazioni di Archiviazione Azure a disponibilità elevata</a>. Per altre informazioni su GZRS/RA-GZRS, vedere <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">archiviazione con ridondanza geografica per disponibilità elevata e durabilità massima (anteprima)</a>. Per altre informazioni sulle altre opzioni di replica, vedere <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Replica di Archiviazione di Azure</a>.</div>

<div id="premium-performance"><sup>5</sup> Le prestazioni Premium per gli account per utilizzo generico V2 e per utilizzo generico V1 sono disponibili solo per i BLOB di pagine e dischi. Le prestazioni Premium per i BLOB in blocchi o di Accodamento sono disponibili solo per gli account BlockBlobStorage. Le prestazioni Premium per i file sono disponibili solo negli account di archiviazione filestorage.</div>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 è un set di funzionalità dedicate a Big Data Analytics, basate sull'archiviazione BLOB di Azure. Data Lake Storage Gen2 è supportato solo per gli account di archiviazione per utilizzo generico V2 con lo spazio dei nomi gerarchico abilitato. Per ulteriori informazioni su Data Lake Storage Gen2, vedere <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Introduzione a Azure Data Lake storage Gen2</a>.</div>
