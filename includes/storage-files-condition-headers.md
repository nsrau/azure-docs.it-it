---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737492"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Condizione di errore ConditionHeadersNotSupported da un'applicazione Web che usa file di Azure dal browserError ConditionHeadersNotSupported from a Web Application using Azure Files from Browser

L'errore ConditionHeadersNotSupported si verifica quando si accede al contenuto ospitato in File di Azure tramite un'applicazione che usa intestazioni condizionali, ad esempio un Web browser, l'accesso non riesce. L'errore indica che le intestazioni di condizione non sono supportate.

![Errore relativo alle intestazioni condizionali dei file di AzureAzure Files conditional headers error](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Le intestazioni condizionali non sono ancora supportate. Le applicazioni che li implementano dovranno richiedere il file completo ogni volta che si accede al file.

### <a name="workaround"></a>Soluzione alternativa

Quando viene caricato un nuovo file, la proprietà cache-control per impostazione predefinita è "no-cache". Per forzare l'applicazione a richiedere il file ogni volta, la proprietà del controllo della cache del file deve essere aggiornata da "no-cache" a "no-cache, no-store, must-revalidate". A tale scopo, è possibile usare [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

![Modifica della cache del contenuto di Esplora archiviazione per le intestazioni condizionali di File di AzureStorage explorer content cache modification for Azure Files conditional headers](media/storage-files-condition-headers/storage-explorer-cache.png)