---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6ad5bafe66e6e39503d3987aa6ddd9159c532fa0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237374"
---
L'importo fatturato per l'uso di Archiviazione di Azure dipende dall'uso dell'account di archiviazione. Tutti gli oggetti in un account di archiviazione vengono fatturati insieme come gruppo. 

I costi di archiviazione sono calcolati in base ai fattori seguenti: area/posizione, tipo di account, livello di accesso, capacità di archiviazione, schema di replica, transazioni di archiviazione e dati in uscita.

* L'**area** si riferisce all'area geografica in cui si trova l'account.
* Il **tipo di account** si riferisce al tipo di account di archiviazione in uso. 
* Il **livello di accesso** si riferisce al criterio di utilizzo dei dati specificato per l'account di archiviazione per utilizzo generico v2 o BLOB.
* La **capacità** di archiviazione si riferisce alla porzione dell'allocazione dell'account di archiviazione usata per l'archiviazione dei dati.
* La **replica** determina il numero di copie dei dati conservate contemporaneamente e le posizioni di archiviazione.
* Le **transazioni** si riferiscono a tutte le operazioni di lettura e scrittura in Archiviazione di Azure.
* I **dati in uscita** si riferiscono ai dati trasferiti all'esterno di un'area di Azure. Quando un'applicazione non in esecuzione nella stessa area accede ai dati nell'account di archiviazione, viene addebitata una quota per l'uscita dei dati. Per informazioni sull'uso dei gruppi di risorse per raggruppare i dati e i servizi nella stessa area per ridurre gli addebiti per il traffico in uscita, vedere [Informazioni sul gruppo di risorse di Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

La pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) fornisce informazioni dettagliate sui prezzi in base a tipo di account, capacità di archiviazione, replica e transazioni. Nella pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita. È possibile usare la pagina per il [calcolo dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) per stimare i costi.

