---
title: Rimuovere TLS 1,0 e 1,1 da usare con cache di Azure per Redis
description: Informazioni su come rimuovere TLS 1,0 e 1,1 dall'applicazione durante la comunicazione con cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 585828855cdbd3a585ced1aa6803482c3f26f0f4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121585"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Rimuovere TLS 1,0 e 1,1 da usare con cache di Azure per Redis

È disponibile un push a livello di settore verso l'uso esclusivo di Transport Layer Security (TLS) versione 1,2 o successiva. Le versioni di TLS 1,0 e 1,1 sono note come vulnerabili ad attacchi come BEAST e POODLE e per avere altre vulnerabilità comuni (CVE). Non supportano inoltre i moderni metodi di crittografia e i pacchetti di crittografia consigliati dagli standard di conformità PCI (Payment Card Industry). Questo [Blog sulla sicurezza di TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) illustra alcune di queste vulnerabilità in modo più dettagliato.

Anche se nessuna di queste considerazioni costituisce un problema immediato, è consigliabile interrompere l'uso di TLS 1,0 e 1,1 a breve. Cache di Azure per Redis smetterà di supportare queste versioni di TLS il 31 marzo 2020. Dopo tale data, l'applicazione dovrà usare TLS 1,2 o versione successiva per comunicare con la cache.

Questo articolo fornisce indicazioni generali su come rilevare le dipendenze da queste versioni precedenti di TLS e rimuoverle dall'applicazione.

## <a name="check-whether-your-application-is-already-compliant"></a>Verificare se l'applicazione è già conforme

Il modo più semplice per verificare se l'applicazione funzionerà con TLS 1,2 consiste nell'impostare il valore della **versione minima di TLS** su TLS 1,2 in una cache di test o di gestione temporanea che usa. L'impostazione **minima della versione di TLS** è nelle [Impostazioni avanzate](cache-configure.md#advanced-settings) dell'istanza della cache nel portale di Azure. Se l'applicazione continua a funzionare come previsto dopo questa modifica, è probabile che sia conforme. Potrebbe essere necessario configurare alcune librerie client Redis usate dall'applicazione in modo specifico per abilitare TLS 1,2, in modo che possano connettersi alla cache di Azure per Redis tramite il protocollo di sicurezza.

## <a name="configure-your-application-to-use-tls-12"></a>Configurare l'applicazione per l'uso di TLS 1,2

La maggior parte delle applicazioni usa le librerie client Redis per gestire la comunicazione con le cache. Di seguito sono riportate le istruzioni per la configurazione di alcune delle librerie client più diffuse, in diversi linguaggi di programmazione e Framework, per l'uso di TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Per impostazione predefinita, i client di Redis .NET usano la versione più recente di TLS in .NET Framework 4.5.2 o versioni precedenti e usano la versione più recente di TLS .NET Framework 4,6 o versioni successive. Se si usa una versione precedente di .NET Framework, è possibile abilitare TLS 1,2 manualmente:

* **Stackexchange. Redis:** Impostare `ssl=true` e `sslprotocls=tls12` nella stringa di connessione.
* **ServiceStack. Redis:** Seguire le [istruzioni di ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Per impostazione predefinita, i client .NET Core di redis usano la versione più recente di TLS.

### <a name="java"></a>Java

I client Java Redis usano TLS 1,0 in Java versione 6 o versioni precedenti. JEDIS, lattuga e Radisson non possono connettersi alla cache di Azure per Redis se TLS 1,0 è disabilitato nella cache. Attualmente non esiste alcuna soluzione nota.

In Java 7 o versioni successive, i client Redis non usano TLS 1,2 per impostazione predefinita, ma possono essere configurati. Lattuga e Radisson non supportano questa configurazione in questo momento. Si interrompono se la cache accetta solo connessioni TLS 1,2. JEDIS consente di specificare le impostazioni TLS sottostanti con il frammento di codice seguente:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Il nodo Redis e IORedis usano TLS 1,2 per impostazione predefinita.

### <a name="php"></a>PHP

Il predazione di PHP 7 non funziona perché PHP 7 supporta solo TLS 1,0. In PHP 7.2.1 o versioni precedenti, predazione usa TLS 1,0 o 1,1 per impostazione predefinita. È possibile specificare TLS 1,2 quando si crea l'istanza del client:

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

In PHP 7,3 o versioni successive, predazione usa la versione più recente di TLS.

PhpRedis non supporta TLS in alcuna versione di PHP.

### <a name="python"></a>Python

Redis-py usa TLS 1,2 per impostazione predefinita.

### <a name="go"></a>GO

Per impostazione predefinita, redigo usa TLS 1,2.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Come configurare cache di Azure per Redis](cache-configure.md)
