---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bef314e60b962e39b3a55d0fb7acb40d4d1b4c32
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38732879"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Copiare le credenziali dal portale di Azure

L'applicazione di esempio deve autenticare l'accesso all'account di archiviazione. Per eseguire l'autenticazione, si passano all'applicazione le credenziali dell'account di archiviazione sotto forma di stringa di connessione. Per visualizzare le credenziali dell'account di archiviazione:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. Nella sezione **Impostazioni** della panoramica dell'account di archiviazione selezionare **Chiavi di accesso**. Verranno visualizzate le chiavi di accesso dell'account, con la stringa di connessione completa per ogni chiave.   
4. Trovare il valore **Stringa di connessione** in **key1** e fare clic sul pulsante **Copia** per copiare la stringa di connessione. Il valore della stringa di connessione verrà aggiunto a una variabile di ambiente nel passaggio successivo.

    ![Screenshot che mostra come copiare una stringa di connessione dal portale di Azure](media/storage-copy-connection-string-portal/portal-connection-string.png)
