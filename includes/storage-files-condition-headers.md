---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180215"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Errore ConditionHeadersNotSupported da un'applicazione Web tramite file di Azure dal Browser

Quando l'accesso al contenuto ospitato nei file di Azure tramite un'applicazione che consente di utilizzare delle intestazioni condizionali, ad esempio un web browser, accesso ha esito negativo, con la visualizzazione di un errore ConditionHeadersNotSupported.

![Errore ConditionHeaderNotSupported](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Le intestazioni condizionali non sono ancora supportate. Le applicazioni implementarli necessario richiedere il file completo ogni volta che il file è accessibile.

### <a name="workaround"></a>Soluzione alternativa

Quando un nuovo file viene caricato, la proprietà di controllo della cache per impostazione predefinita è "no-cache". Per forzare l'applicazione per richiedere i file ogni volta, proprietà di cache-control del file deve essere aggiornato da "no-cache" a "no-cache, no-store, must-revalidate". Ciò può essere ottenuto tramite [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Modifica cache di contenuti di storage explorer](media/storage-files-condition-headers/storage-explorer-cache.png)