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
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027512"
---
Un'attività in esecuzione in Azure Batch può produrre dati di output quando viene eseguita. I dati di output delle attività spesso devono essere archiviati per consentirne il recupero da parte di altre attività del processo, dell'applicazione client che ha eseguito il processo o di entrambe. Le attività scrivono i dati di output nel file system di un nodo di calcolo di Batch, ma tutti i dati presenti nel nodo vanno persi quando ne viene ricreata l'immagine o quando il nodo lascia il pool. Le attività possono anche avere un periodo di memorizzazione dei file, dopo il quale i file creati dall'attività vengono eliminati. Per questi motivi, è importante salvare in modo permanente l'output delle attività che sarà necessario in seguito in un archivio dati, ad esempio [Archiviazione di Azure](../articles/storage/index.yml).

Per le opzioni dell'account di archiviazione in Batch, vedere [Account Batch e account di Archiviazione di Azure](../articles/batch/accounts.md#azure-storage-accounts).