---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391686"
---
No: rimuovere un endpoint server non è come riavviare un server! La rimozione e la ricreazione dell'endpoint server non sono quasi mai una soluzione appropriata per la risoluzione dei problemi relativi alla sincronizzazione, al tiering cloud o ad altri aspetti di Sincronizzazione file di Azure.Removing and recreating the server endpoint is almost never an appropriate solution to fixing issues with sync, cloud tiering, or other aspects of Azure File Sync. La rimozione di un endpoint server è un'operazione distruttiva. Può causare la perdita di dati nel caso in cui i file a livelli esistano all'esterno dello spazio dei nomi dell'endpoint server. Per altre informazioni, vedere [Perché esistono file a livelli all'esterno dello spazio dei nomi dell'endpoint server.](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) Oppure potrebbe causare file inaccessibili per i file a livelli presenti nello spazio dei nomi dell'endpoint server. Questi problemi non si risolveranno quando l'endpoint server viene ricreato. I file a livelli potrebbero esistere all'interno dello spazio dei nomi dell'endpoint server anche se la suddivisione a livelli del cloud non è mai stata abilitata. Ecco perché è consigliabile non rimuovere l'endpoint server a meno che non si desideri interrompere l'utilizzo di Sincronizzazione file di Azure con questa particolare cartella o sia stato esplicitamente indicato da un tecnico Microsoft.This's why you don't remove the server endpoint unless you would like to stop using Azure File Sync with this particular folder or have been explicitly isstruct that do so by a Microsoft engineer. Per altre informazioni sulla rimozione degli endpoint server, vedere [Rimuovere un endpoint server](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
