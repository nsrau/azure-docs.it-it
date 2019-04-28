---
title: File di inclusione
description: File di inclusione
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7cee3f534e4ab4973a87a9c373a5b83aa2ba9ce2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549946"
---
Un'attività in esecuzione in Azure Batch può produrre dati di output quando viene eseguita. I dati di output delle attività spesso devono essere archiviati per consentirne il recupero da parte di altre attività del processo, dell'applicazione client che ha eseguito il processo o di entrambe. Le attività scrivono i dati di output nel file system di un nodo di calcolo di Batch, ma tutti i dati presenti nel nodo vanno persi quando ne viene ricreata l'immagine o quando il nodo lascia il pool. Le attività possono anche avere un periodo di memorizzazione dei file, dopo il quale i file creati dall'attività vengono eliminati. Per questi motivi, è importante salvare in modo permanente l'output delle attività che sarà necessario in seguito in un archivio dati, ad esempio [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/).

Per le opzioni dell'account di archiviazione in Batch, vedere [Panoramica delle funzionalità di Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
