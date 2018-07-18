---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/03/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6572adb0d8d629910492603a17988b89acce2f17
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852059"
---
| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per area per sottoscrizione | 200<sup>1</sup> |
| Capacità massima dell'account di archiviazione | 500 TiB<sup>2</sup> |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima di richieste per account di archiviazione | 20.000 richieste al secondo<sup>2</sup> |
| Traffico in ingresso massimo<sup>3</sup> per account di archiviazione (aree degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS abilitata, 20 Gbps per LRS/ZRS<sup>4</sup> |
| Traffico in uscita massimo<sup>3</sup> per account di archiviazione (aree degli Stati Uniti) | 20 Gbps con archiviazione RA-GRS/GRS abilitata, 30 Gbps per LRS/ZRS<sup>4</sup> |
| Traffico in ingresso massimo<sup>3</sup> per account di archiviazione (aree non degli Stati Uniti) | 5 Gbps con archiviazione RA-GRS/GRS abilitata, 10 Gbps per LRS/ZRS<sup>4</sup> |
| Traffico in uscita massimo<sup>3</sup> per account di archiviazione (aree non degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS abilitata, 15 Gbps per LRS/ZRS<sup>4</sup> |

<sup>1</sup>Sono inclusi gli account di archiviazione Standard e Premium. Se sono necessari più di 200 account di archiviazione in una determinata area, inviare una richiesta tramite il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà il caso aziendale e potrà approvare fino a un massimo di 250 account di archiviazione per una determinata area. 

<sup>2</sup> Se è necessario espandere i limiti dell'account di archiviazione, contattare il [supporto di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà la richiesta e potrà approvare limiti più elevati caso per caso. Gli account di archiviazione per utilizzo generico e BLOB supportano entrambi capacità incrementate, dati in ingresso/uscita e velocità della richiesta in base alla richiesta. Per i nuovi valori massimi per gli account di archiviazione BLOB, vedere [Announcing larger, higher scale storage accounts](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) (Annuncio di account di archiviazione di dimensioni maggiori a scalabilità più elevata).

<sup>3</sup> Limitato solo dai valori massimi per il traffico in ingresso/uscita dell'account. Il *traffico in ingresso* indica tutti i dati (richieste) inviati a un account di archiviazione. *uscita* tutti i dati (risposte) ricevuti da un account di archiviazione.  

<sup>4</sup>Le opzioni di ridondanza di Archiviazione di Azure includono:
* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: archiviazione con ridondanza geografica. 
* **ZRS**: archiviazione con ridondanza della zona.
* **LRS**: archiviazione con ridondanza locale. 
