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
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70737492"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Errore ConditionHeadersNotSupported da un'applicazione Web utilizzando File di Azure dal browser

L'errore ConditionHeadersNotSupported si verifica quando si accede al contenuto ospitato in File di Azure tramite un'applicazione che utilizza le intestazioni condizionali, ad esempio un Web browser, l'accesso ha esito negativo. L'errore indica che le intestazioni di condizione non sono supportate.

![Errore File di Azure intestazioni condizionali](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Le intestazioni condizionali non sono ancora supportate. Le applicazioni che li implementano dovranno richiedere il file completo ogni volta che viene eseguito l'accesso al file.

### <a name="workaround"></a>Soluzione alternativa

Quando viene caricato un nuovo file, per impostazione predefinita la proprietà Cache-Control è "no-cache". Per forzare l'applicazione a richiedere il file ogni volta, è necessario aggiornare la proprietà Cache-Control del file da "no-cache" a "no-cache, No-Store, must-revalidate". Questa operazione può essere eseguita usando [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Modifica della cache del contenuto di Storage Explorer per File di Azure intestazioni condizionali](media/storage-files-condition-headers/storage-explorer-cache.png)