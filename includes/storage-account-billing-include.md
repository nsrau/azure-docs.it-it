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
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74851630"
---
Viene fatturata l'archiviazione di Azure in base all'utilizzo dell'account di archiviazione. Tutti gli oggetti in un account di archiviazione vengono fatturati insieme come gruppo. 

I costi di stoccaggio sono calcolati in base ai seguenti fattori: 

* L'**area** si riferisce all'area geografica in cui si trova l'account.
* **Per tipo di account** si intende il tipo di account di archiviazione in uso. 
* **Il livello di accesso** si riferisce al modello di utilizzo dei dati specificato per l'account di archiviazione BLOB o v2 generico.
* **Capacità** di archiviazione si riferisce alla quantità di account di archiviazione in uso per archiviare i dati.
* La **replica** determina il numero di copie dei dati conservate contemporaneamente e le posizioni di archiviazione.
* **Le transazioni** fanno riferimento a tutte le operazioni di lettura e scrittura in Archiviazione di Azure.Transactions refer to all read and write operations to Azure Storage.
* I **dati in uscita** si riferiscono ai dati trasferiti all'esterno di un'area di Azure. Quando l'accesso ai dati nell'account di archiviazione viene eseguito da un'applicazione che non è in esecuzione nella stessa area, viene addebitato l'uscita dei dati. Per informazioni sull'uso dei gruppi di risorse per raggruppare i dati e i servizi nella stessa area per ridurre gli addebiti per il traffico in uscita, vedere [Informazioni sul gruppo di risorse di Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

La pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) fornisce informazioni dettagliate sui prezzi in base a tipo di account, capacità di archiviazione, replica e transazioni. Nella pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita. È possibile usare la pagina per il [calcolo dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) per stimare i costi.

