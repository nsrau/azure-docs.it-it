---
title: includere file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fcfe05db6a9be1049ca5da06985f31135ac79f3b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563432"
---
L'importo fatturato per l'uso di Archiviazione di Azure dipende dall'utilizzo dell'account di archiviazione. Tutti gli oggetti in un account di archiviazione vengono fatturati insieme come gruppo. 

I costi di archiviazione vengono calcolati in base ai fattori seguenti: 

* L'**area** si riferisce all'area geografica in cui si trova l'account.
* Il **tipo di account** si riferisce al tipo di account di archiviazione in uso. 
* Il **livello di accesso** si riferisce al criterio di utilizzo dei dati specificato per l'account di archiviazione BLOB o v2 per utilizzo generico.
* La **capacità** di archiviazione si riferisce alla porzione di allocazione dell'account di archiviazione usata per l'archiviazione dei dati.
* La **replica** determina il numero di copie dei dati conservate contemporaneamente e le posizioni di archiviazione.
* Le **transazioni** si riferiscono a tutte le operazioni di lettura e scrittura in Archiviazione di Azure.
* I **dati in uscita** si riferiscono ai dati trasferiti all'esterno di un'area di Azure. Quando un'applicazione che non è in esecuzione nella stessa area geografica accede ai dati dell'account di archiviazione, viene addebitata un importo per i dati in uscita. Per informazioni sull'uso dei gruppi di risorse per raggruppare i dati e i servizi nella stessa area per ridurre gli addebiti per il traffico in uscita, vedere [Informazioni sul gruppo di risorse di Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

La pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) fornisce informazioni dettagliate sui prezzi in base a tipo di account, capacità di archiviazione, replica e transazioni. Nella pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita. È possibile usare la pagina per il [calcolo dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) per stimare i costi.