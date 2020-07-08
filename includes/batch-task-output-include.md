---
title: includere file
description: includere file
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797167"
---
Un'attività in esecuzione in Azure Batch può produrre dati di output quando viene eseguita. I dati di output delle attività spesso devono essere archiviati per consentirne il recupero da parte di altre attività del processo, dell'applicazione client che ha eseguito il processo o di entrambe. Le attività scrivono i dati di output nel file system di un nodo di calcolo di Batch, ma tutti i dati presenti nel nodo vanno persi quando ne viene ricreata l'immagine o quando il nodo lascia il pool. Le attività possono anche avere un periodo di memorizzazione dei file, dopo il quale i file creati dall'attività vengono eliminati. Per questi motivi, è importante salvare in modo permanente l'output delle attività che sarà necessario in seguito in un archivio dati, ad esempio [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/).

Per le opzioni dell'account di archiviazione in Batch, vedere [Account Batch e account di Archiviazione di Azure](../articles/batch/accounts.md#azure-storage-accounts).
