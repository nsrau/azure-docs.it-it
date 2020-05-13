---
title: Rimuovere TLS 1,0 e 1,1 da usare con cache di Azure per Redis
description: Informazioni su come rimuovere TLS 1,0 e 1,1 dall'applicazione durante la comunicazione con cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: efb9e8b8abdcb442e2c5c4d8bfd1b2e1e60865ce
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197843"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Rimuovere TLS 1,0 e 1,1 da usare con cache di Azure per Redis

È disponibile un push a livello di settore verso l'uso esclusivo di Transport Layer Security (TLS) versione 1,2 o successiva. Le versioni di TLS 1,0 e 1,1 sono note come vulnerabili ad attacchi come BEAST e POODLE e per avere altre vulnerabilità comuni (CVE). Non supportano inoltre i moderni metodi di crittografia e i pacchetti di crittografia consigliati dagli standard di conformità PCI (Payment Card Industry). Questo [Blog sulla sicurezza di TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) illustra alcune di queste vulnerabilità in modo più dettagliato.

Come parte di questo sforzo, verranno apportate le modifiche seguenti alla cache di Azure per redis:

* **Fase 1:** Verrà configurata la versione minima predefinita di TLS come 1,2 per le istanze di cache appena create (in precedenza TLS 1,0).  A questo punto le istanze di cache esistenti non verranno aggiornate. Se necessario, sarà possibile ripristinare [la versione minima di TLS](cache-configure.md#access-ports) a 1,0 o 1,1 per compatibilità con le versioni precedenti. Questa modifica può essere eseguita tramite la portale di Azure o altre API di gestione.
* **Fase 2:** Verrà interrotto il supporto delle versioni di TLS 1,0 e 1,1. Dopo questa modifica, l'applicazione dovrà usare TLS 1,2 o versione successiva per comunicare con la cache.

Inoltre, nell'ambito di questa modifica, verrà rimosso il supporto per i gruppi di crittografia meno recenti e non sicuri.  Quando la cache è configurata con una versione minima di TLS 1,2, i gruppi di crittografia supportati saranno limitati a quanto segue.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Questo articolo fornisce indicazioni generali su come rilevare le dipendenze da queste versioni precedenti di TLS e rimuoverle dall'applicazione.

Le date in cui queste modifiche diventano effettive sono:

| Cloud               | Data di inizio fase 1 | Data di inizio della fase 2      |
|---------------------|--------------------|-------------------------|
| Azure (globale)      |  13 gennaio 2020  | 11 maggio 2020            |
| Azure Government    |  13 marzo, 2020    | 11 maggio 2020            |
| Azure Germania       |  13 marzo, 2020    | 11 maggio 2020            |
| Azure Cina         |  13 marzo, 2020    | 11 maggio 2020            |

## <a name="check-whether-your-application-is-already-compliant"></a>Verificare se l'applicazione è già conforme

Il modo più semplice per verificare se l'applicazione funzionerà con TLS 1,2 consiste nell'impostare il valore della **versione minima di TLS** su TLS 1,2 in una cache di test o di gestione temporanea che usa. L'impostazione **minima della versione di TLS** è nelle [Impostazioni avanzate](cache-configure.md#advanced-settings) dell'istanza della cache nel portale di Azure. Se l'applicazione continua a funzionare come previsto dopo questa modifica, è probabile che sia conforme. Potrebbe essere necessario configurare alcune librerie client Redis usate dall'applicazione in modo specifico per abilitare TLS 1,2, in modo che possano connettersi alla cache di Azure per Redis tramite il protocollo di sicurezza.

## <a name="configure-your-application-to-use-tls-12"></a>Configurare l'applicazione per l'uso di TLS 1,2

La maggior parte delle applicazioni usa le librerie client Redis per gestire la comunicazione con le cache. Di seguito sono riportate le istruzioni per la configurazione di alcune delle librerie client più diffuse, in diversi linguaggi di programmazione e Framework, per l'uso di TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Per impostazione predefinita, i client di Redis .NET usano la versione più recente di TLS in .NET Framework 4.5.2 o versioni precedenti e usano la versione più recente di TLS .NET Framework 4,6 o versioni successive. Se si usa una versione precedente di .NET Framework, è possibile abilitare TLS 1,2 manualmente:

* **Stackexchange. Redis:** Impostare `ssl=true` e `sslprotocols=tls12` nella stringa di connessione.
* **ServiceStack. Redis:** Seguire le istruzioni di [ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) e richiede ServiceStack. Redis v 5.6 come minimo.

### <a name="net-core"></a>.NET Core

Per impostazione predefinita, i client .NET Core di redis usano la versione più recente di TLS.

### <a name="java"></a>Java

I client Java Redis usano TLS 1,0 in Java versione 6 o versioni precedenti. JEDIS, lattuga e Redisson non possono connettersi alla cache di Azure per Redis se TLS 1,0 è disabilitato nella cache. Aggiornare il framework Java per usare le nuove versioni di TLS.

Per Java 7, i client Redis non usano TLS 1,2 per impostazione predefinita, ma possono essere configurati. JEDIS consente di specificare le impostazioni TLS sottostanti con il frammento di codice seguente:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

I client lattuga e Redisson non supportano ancora la specifica della versione di TLS, quindi verranno Interrompi se la cache accetta solo connessioni TLS 1,2. Le correzioni per questi client vengono esaminate, quindi controllare con questi pacchetti una versione aggiornata con questo supporto.

In Java 8, per impostazione predefinita viene usato TLS 1,2 e non è necessario aggiornare la configurazione client nella maggior parte dei casi. Per essere sicuri, testare l'applicazione.

### <a name="nodejs"></a>Node.js

Il nodo Redis e IORedis usano TLS 1,2 per impostazione predefinita.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versioni precedenti a PHP 7: predazione supporta solo TLS 1,0. Queste versioni non funzionano con TLS 1,2; per usare TLS 1,2, è necessario eseguire l'aggiornamento.
 
* Da PHP 7,0 a PHP 7.2.1: predazione usa solo TLS 1,0 o 1,1 per impostazione predefinita. Per usare TLS 1,2, è possibile usare la soluzione alternativa seguente. Specificare TLS 1,2 quando si crea l'istanza del client:

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

* PHP 7,3 e versioni successive: predazione usa la versione più recente di TLS.

#### <a name="phpredis"></a>PhpRedis

PhpRedis non supporta TLS in alcuna versione di PHP.

### <a name="python"></a>Python

Redis-py usa TLS 1,2 per impostazione predefinita.

### <a name="go"></a>GO

Per impostazione predefinita, redigo usa TLS 1,2.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Come configurare Cache Redis di Azure](cache-configure.md)
