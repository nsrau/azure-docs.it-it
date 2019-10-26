---
title: Rimuovere l'uso di TLS 1,0 e 1,1 con cache di Azure per Redis | Microsoft Docs
description: Informazioni su come rimuovere TLS 1,0 e 1,1 dall'applicazione durante la comunicazione con cache di Azure per Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901898"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>Rimuovere l'uso di TLS 1,0 e 1,1 con cache di Azure per Redis

È disponibile un push a livello di settore per l'uso esclusivo di TLS 1,2 o versione successiva. Le versioni di TLS 1,0 e 1,1 sono note come vulnerabili ad attacchi come BEAST e POODLE e presentano debolezze di vulnerabilità ed esposizioni (CVE) comuni. Inoltre, non supportano i moderni metodi di crittografia e i pacchetti di crittografia consigliati dagli standard di conformità PCI. Questo [Blog sulla sicurezza di TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) illustra alcune di queste vulnerabilità in altri dettagli.

Anche se nessuno di questi pone problemi immediati, è consigliabile prendere in considerazione la possibilità di allontanarsi dall'uso di TLS 1,0 e 1,1 il prima possibile. Cache di Azure per Redis smetterà di supportare queste versioni di TLS a partire dal 31 marzo 2020. L'applicazione dovrà usare almeno TLS 1,2 per comunicare con la cache dopo questa data.

Questo articolo fornisce indicazioni generali su come rilevare e rimuovere queste dipendenze dall'applicazione.

## <a name="check-if-your-application-is-already-compliant"></a>Verificare se l'applicazione è già conforme

Il modo più semplice per determinare se l'applicazione funzionerà con TLS 1,2 consiste nell'impostare la versione minima di TLS in una cache di test o di gestione temporanea che usa per TLS 1,2. È possibile trovare l'impostazione minima della versione di TLS nelle [Impostazioni avanzate](cache-configure.md#advanced-settings) dell'istanza della cache nel portale di Azure. Se l'applicazione continua a funzionare come previsto dopo questa modifica, è molto probabile che sia conforme. È possibile che alcune librerie client Redis usate dall'applicazione debbano essere configurate in modo specifico per abilitare TLS 1,2 per connettersi alla cache di Azure per Redis tramite il protocollo di sicurezza.

## <a name="configure-your-application-to-use-tls-12"></a>Configurare l'applicazione per l'uso di TLS 1,2

La maggior parte delle applicazioni usa librerie client Redis per gestire la comunicazione con le cache. Di seguito sono riportate le istruzioni per configurare alcune delle librerie client più diffuse in diversi linguaggi di programmazione e Framework per l'uso di TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Per impostazione predefinita, i client di Redis .NET usano la versione TLS più bassa in .NET Framework 4.5.2 o sotto e la versione TLS più elevata 4,6 o successiva. Se si usa una versione precedente di .NET Framework, è possibile abilitare TLS 1,2 manualmente:

* StackExchange. Redis: impostare `ssl=true` e `sslprotocls=tls12` nella stringa di connessione.
* ServiceStack. Redis: seguire [queste istruzioni](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Per impostazione predefinita, i client .NET Core di redis usano la versione TLS più elevata.

### <a name="java"></a>Java

I client Java Redis usano TLS 1,0 in Java versione 6 o successiva. JEDIS, lattuga e Radisson non saranno in grado di connettersi alla cache di Azure per Redis se TLS 1,0 è disabilitato nella cache. Attualmente non esiste alcuna soluzione nota.

In Java 7 o versione successiva, i client Redis non usano TLS 1,2 per impostazione predefinita, ma possono essere configurati. Lattuga e Radisson non supportano questo momento. Si interrompono se la cache accetta solo connessioni TLS 1,2. JEDIS consente di specificare le impostazioni TLS sottostanti con il frammento di codice seguente:

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

Il predazione di PHP 7 non funzionerà poiché quest'ultimo supporta solo TLS 1,0. Per impostazione predefinita, in PHP 7.2.1 o versioni precedenti, Predator usa TLS 1,0 o 1,1. È possibile specificare TLS 1,2 quando si crea un'istanza del client:

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
