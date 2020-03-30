---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9333bb36971fc28a23a443e50d191abeef05b758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208177"
---
Per visualizzare e copiare le chiavi di accesso o la stringa di connessione dell'account di archiviazione dal portale di Azure:To view and copy your storage account access keys or connection string from the Azure portal:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. In **Impostazioni**selezionare **Chiavi di accesso**. Verranno visualizzate le chiavi di accesso dell'account, con la stringa di connessione completa per ogni chiave.
4. Trovare il valore **Chiave** in **key1** e fare clic sul pulsante **Copia** per copiare la chiave dell'account.
5. In alternativa, è possibile copiare l'intera stringa di connessione. Trovare il valore **Stringa di connessione** in **key1** e fare clic sul pulsante **Copia** per copiare la stringa di connessione.

    ![Screenshot che mostra come visualizzare le chiavi di accesso nel portale di Azure](media/storage-view-keys-include/portal-connection-string.png)

È possibile usare una delle due chiavi per accedere ad Archiviazione di Azure, ma in generale è consigliabile usare la prima chiave e riservare l'uso della seconda chiave per la rotazione delle chiavi.
