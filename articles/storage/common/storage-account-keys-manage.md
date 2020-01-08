---
title: Gestire le chiavi di accesso dell'account
titleSuffix: Azure Storage
description: Informazioni su come visualizzare, gestire e ruotare le chiavi di accesso dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 68d0ef14a7b5967d6a4535da85d32128c3a12a19
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486729"
---
# <a name="manage-storage-account-access-keys"></a>Gestisci chiavi di accesso all'account di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso dell'account di archiviazione a 512 bit. Queste chiavi possono essere usate per autorizzare l'accesso ai dati nell'account di archiviazione tramite l'autorizzazione della chiave condivisa.

Microsoft consiglia di utilizzare Azure Key Vault per gestire le chiavi di accesso e di ruotare e rigenerare periodicamente le chiavi. L'uso di Azure Key Vault rende più semplice ruotare le chiavi senza interruzioni delle applicazioni. È anche possibile ruotare manualmente le chiavi.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Visualizza chiavi di accesso e stringa di connessione

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Usare Azure Key Vault per gestire le chiavi di accesso

Microsoft consiglia di utilizzare Azure Key Vault per gestire e ruotare le chiavi di accesso. L'applicazione può accedere in modo sicuro alle chiavi in Key Vault, in modo che sia possibile evitare di archiviarle con il codice dell'applicazione. Per ulteriori informazioni sull'utilizzo di Key Vault per la gestione delle chiavi, vedere gli articoli seguenti:

- [Gestire le chiavi dell'account di archiviazione con Azure Key Vault e PowerShell](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Gestire chiavi dell'account di archiviazione con Azure Key Vault e l'interfaccia della riga di comando di Azure](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ruotare manualmente le chiavi di accesso

Microsoft consiglia di ruotare periodicamente le chiavi di accesso per proteggere l'account di archiviazione. Se possibile, usare Azure Key Vault per gestire le chiavi di accesso. Se non si usa Key Vault, sarà necessario ruotare manualmente le chiavi.

Vengono assegnate due chiavi di accesso che è quindi possibile ruotare. La presenza di due chiavi garantisce che l'applicazione mantenga l'accesso ad archiviazione di Azure durante tutto il processo.

> [!WARNING]
> La rigenerazione delle chiavi di accesso può influire sulle applicazioni o sui servizi di Azure che dipendono dalla chiave dell'account di archiviazione. I client che usano la chiave dell'account per accedere all'account di archiviazione devono essere aggiornati per usare la nuova chiave, inclusi servizi multimediali, applicazioni cloud, desktop e per dispositivi mobili e applicazioni di interfaccia utente grafica per Archiviazione di Azure, ad esempio [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

Per ruotare le chiavi dell'account di archiviazione, seguire questa procedura:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per usare la chiave secondaria.
2. Rigenerare la chiave di accesso primaria per l'account di archiviazione. Nel pannello **Chiavi di accesso** del portale di Azure fare clic su **Rigenerare Key1** e quindi su **Sì** per confermare che si vuole generare una nuova chiave.
3. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
4. Rigenerare la chiave di accesso secondaria nello stesso modo.

> [!NOTE]
> Microsoft consiglia di usare solo una delle chiavi in tutte le applicazioni contemporaneamente. Se si usa la Chiave 1 in alcune posizioni e la Chiave 2 in altre, non si potranno ruotare le chiavi senza quale applicazione perda l'accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md)
- [Creare un account di archiviazione](storage-quickstart-create-account.md)
