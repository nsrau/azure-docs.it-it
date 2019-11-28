---
title: File di inclusione
description: File di inclusione
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: mhopkins
ms.custom: include file
ms.openlocfilehash: 7e6bdf8f2cede2d55163932bfe8ab58e9ee96e0f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422025"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiare le credenziali dal portale di Azure

Le richieste effettuate dall'applicazione di esempio ad Archiviazione di Azure devono essere autorizzate. Per autorizzare una richiesta, aggiungere all'applicazione le credenziali dell'account di archiviazione sotto forma di stringa di connessione. Visualizzare le credenziali dell'account di archiviazione seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. Nella sezione **Impostazioni** della panoramica dell'account di archiviazione selezionare **Chiavi di accesso**. A questo punto è possibile visualizzare le chiavi di accesso dell'account e la stringa di connessione completa per ogni chiave.
4. Trovare il valore **Stringa di connessione** in **key1** e selezionare il pulsante **Copia** per copiare la stringa di connessione. Il valore della stringa di connessione verrà aggiunto a una variabile di ambiente nel passaggio successivo.

    ![Screenshot che mostra come copiare una stringa di connessione dal portale di Azure](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Dopo aver copiato la stringa di connessione, scriverla in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra della console e seguire le istruzioni per il sistema operativo specifico. Sostituire `<yourconnectionstring>` con la stringa di connessione effettiva.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Dopo aver aggiunto la variabile di ambiente in Windows, è necessario avviare una nuova istanza della finestra di comando.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Riavviare i programmi

Dopo aver aggiunto la variabile di ambiente, riavviare gli eventuali programmi in esecuzione che dovranno leggerla. Ad esempio, riavviare l'ambiente di sviluppo o l'editor prima di continuare.
