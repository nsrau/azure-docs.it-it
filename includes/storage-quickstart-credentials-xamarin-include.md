---
title: File di inclusione
description: File di inclusione
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "83006377"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiare le credenziali dal portale di Azure

Le richieste effettuate dall'applicazione di esempio ad Archiviazione di Azure devono essere autorizzate. Per autorizzare una richiesta, aggiungere all'applicazione le credenziali dell'account di archiviazione sotto forma di stringa di connessione. Visualizzare le credenziali dell'account di archiviazione seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. Nella sezione **Impostazioni** della panoramica dell'account di archiviazione selezionare **Chiavi di accesso**. A questo punto è possibile visualizzare le chiavi di accesso dell'account e la stringa di connessione completa per ogni chiave.
4. Trovare il valore **Stringa di connessione** in **key1** e selezionare il pulsante **Copia** per copiare la stringa di connessione. Il valore della stringa di connessione verrà aggiunto a una variabile di ambiente nel passaggio successivo.

    ![Screenshot che mostra come copiare una stringa di connessione dal portale di Azure](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Dopo aver copiato la stringa di connessione, impostarla su una variabile a livello di classe nel file *MainPage.xaml.cs*. Aprire *MainPaage.xaml.cs* e trovare la variabile `storageConnectionString`. Sostituire `<yourconnectionstring>` con la stringa di connessione effettiva.

Ecco il codice:

```csharp
string storageConnectionString = "<yourconnectionstring>";
```