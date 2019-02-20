---
title: Come usare redis-cli con Cache Redis di Azure | Microsoft Docs
description: Informazioni su come usare redis-cli con Cache Redis di Azure.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: yegu
ms.openlocfilehash: 318d02f5da816ae8fe2fe199b9c87b3748d5d1fc
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237570"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Come usare lo strumento da riga di comando Redis con Cache Redis di Azure

*redis-cli.exe* è uno strumento da riga di comando comune per l'interazione con un'istanza di Cache Redis di Azure come client. Questo strumento è disponibile anche per l'uso con Cache Redis di Azure.

Lo strumento è disponibile per le piattaforme Windows scaricando gli [strumenti da riga di comando Redis per Windows](https://github.com/MSOpenTech/redis/releases/). 

Se si vuole eseguire lo strumento da riga di comando su un'altra piattaforma, scaricare Cache Redis di Azure da [https://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Raccogliere le informazioni di accesso alla cache

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile raccogliere le informazioni necessarie per accedere alla cache nelle tre modalità seguenti:

1. Tramite l'interfaccia della riga di comando di Azure usando [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Tramite Azure PowerShell usando [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Tramite il portale di Azure.

In questa sezione si recupereranno le chiavi dal portale di Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Abilitare l'accesso per redis-cli.exe.

Con Cache Redis di Azure, solo la porta SSL (6380) è abilitata per impostazione predefinita. Lo strumento da riga di comando `redis-cli.exe` non supporta il protocollo SSL. Per usarlo sono disponibili due opzioni di configurazione:

1. [Abilitare la porta non SSL (6379)](cache-configure.md#access-ports) - **Questa configurazione non è consigliabile** poiché le chiavi di accesso vengono trasmesse tramite TCP in testo non crittografato. Questa modifica può compromettere l'accesso alla cache. L'unico scenario in cui è possibile tenere in considerazione questa configurazione è quando si accede a una cache di test.

2. Scaricare e installare [stunnel](https://www.stunnel.org/downloads.html).

    Eseguire **stunnel GUI Start** (Avvio GUI stunnel) per avviare il server.

    Fare clic con il pulsante destro del mouse sull'icona della barra delle applicazioni per il server stunnel e fare clic su **Show Log Window** (Mostra finestra log).

    Nel menu corrispondente fare clic su **Configuration** (Configurazione) > **Edit Configuration** (Modifica configurazione) per aprire il file di configurazione corrente.

    Aggiungere la voce seguente per *redis-cli.exe* nella sezione **Service definitions** (Definizioni di servizio). Inserire il nome effettivo della cache invece di `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Salvare e chiudere il file di configurazione. 
  
    Nel menu della finestra di log di stunnel fare clic su **Configuration** (Configurazione)  > **Reload Configuration** (Ricarica configurazione).


## <a name="connect-using-the-redis-command-line-tool"></a>Connettersi usando lo strumento da riga di comando Redis.

Quando si usa stunnel, eseguire *redis-cli.exe* e passare solo la *porta* e la *chiave di accesso* (primaria o secondaria) per connettersi alla cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel con redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se si usa una cache di test con la porta non SSL **non sicura**, eseguire `redis-cli.exe` e passare il *nome host*, la *porta* e la *chiave di accesso*(primaria o secondaria) per connettersi alla cache di test.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel con redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'uso della [Console Redis](cache-configure.md#redis-console) per inviare i comandi.

