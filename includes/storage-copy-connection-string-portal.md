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
ms.openlocfilehash: 8f511eafda70fca997a08b5907e606e572b8e219
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128933"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Copiare le credenziali dal portale di Azure

L'applicazione di esempio deve autenticare l'accesso all'account di archiviazione. Per eseguire l'autenticazione, aggiungere all'applicazione le credenziali dell'account di archiviazione sotto forma di stringa di connessione. Visualizzare le credenziali dell'account di archiviazione seguendo questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. Nella sezione **Impostazioni** della panoramica dell'account di archiviazione selezionare **Chiavi di accesso**. A questo punto è possibile visualizzare le chiavi di accesso dell'account e la stringa di connessione completa per ogni chiave.   
4. Trovare il valore **Stringa di connessione** in **key1** e selezionare il pulsante **Copia** per copiare la stringa di connessione. Il valore della stringa di connessione verrà aggiunto a una variabile di ambiente nel passaggio successivo.

    ![Screenshot che mostra come copiare una stringa di connessione dal portale di Azure](media/storage-copy-connection-string-portal/portal-connection-string.png)
