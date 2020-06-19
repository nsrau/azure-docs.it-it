---
title: Rimuovere TLS 1.0 and 1.1 per l'uso con cache di Azure per Redis
description: Di seguito viene descritto come rimuovere TLS 1.0 e 1.1 dall'applicazione per comunicare con la cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: b7b3556896f2d8bb8fea7ffc4543356e248df60d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848822"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Rimuovere TLS 1.0 and 1.1 per l'uso con cache di Azure per Redis

In tutto il settore si assiste a una spinta verso l'uso esclusivo della versione 1.2 o successiva di Transport Layer Security (TLS). Le versioni TLS 1.0 e 1.1 sono notoriamente esposte ad attacchi come BEAST e POODLE e presentano punti deboli relativi a vulnerabilità ed esposizioni comuni (CVE). Inoltre, non supportano i metodi e i pacchetti di crittografia moderni consigliati dagli standard di conformità PCI (Payment Card Industry). Questo [blog sulla sicurezza TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) illustra alcune di queste vulnerabilità in modo più dettagliato.

In tale contesto, verranno apportate le modifiche seguenti alla cache di Azure per Redis:

* **Fase 1:** la versione TLS minima predefinita verrà configurata su 1.2 per le istanze della cache appena create (in precedenza TLS 1.0).  A questo punto le istanze della cache esistenti non verranno aggiornate. Se necessario, sarà possibile [modificare la versione minima di TLS](cache-configure.md#access-ports) in 1.0 o 1.1 per la compatibilità con le versioni precedenti. Questa modifica può essere eseguita tramite il portale di Azure o altre API di gestione.
* **Fase 2:** le versioni TLS 1.0 e 1.1 non saranno più supportate. Dopo questa modifica, l'applicazione dovrà usare TLS 1.2 o una versione successiva per comunicare con la cache.

Inoltre, nell'ambito di questa modifica, verrà rimosso il supporto per i pacchetti di crittografia meno recenti e non sicuri.  Quando la cache è configurata con una versione minima di TLS 1.2, i pacchetti di crittografia supportati saranno limitati ai seguenti.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Questo articolo offre indicazioni generali su come rilevare le dipendenze da queste versioni precedenti di TLS e rimuoverle dall'applicazione.

Le date di entrata in vigore di queste modifiche sono:

| Cloud                | Data di inizio della fase 1 | Data di inizio della fase 2      |
|----------------------|--------------------|-------------------------|
| Azure (globale)       |  13 gennaio 2020  | 11 maggio 2020            |
| Azure Government     |  13 marzo 2020    | 11 maggio 2020            |
| Azure Germania        |  13 marzo 2020    | 11 maggio 2020            |
| 21Vianet per Azure Cina |  13 marzo 2020    | 11 maggio 2020            |

## <a name="check-whether-your-application-is-already-compliant"></a>Verificare se l'applicazione è già conforme

Il modo più semplice per verificare se l'applicazione funzionerà con TLS 1.2 consiste nell'impostare il valore **Versione minima TLS** su TLS 1.2 in una cache di test o di gestione temporanea usata. L'impostazione **Versione minima TLS** si trova nelle [Impostazioni avanzate](cache-configure.md#advanced-settings) dell'istanza della cache nel portale di Azure. Se l'applicazione continua a funzionare come previsto dopo questa modifica, è probabile che sia conforme. Potrebbe essere necessario configurare alcune librerie client Redis usate dall'applicazione in modo specifico per abilitare TLS 1.2, in modo che possano connettersi alla cache di Azure per Redis tramite il protocollo di sicurezza.

## <a name="configure-your-application-to-use-tls-12"></a>Configurare l'applicazione per l'uso di TLS 1.2

La maggior parte delle applicazioni usa le librerie client Redis per gestire la comunicazione con le cache. Di seguito sono riportate le istruzioni per configurare alcune delle librerie client più diffuse, in diversi linguaggi di programmazione e framework, per l'uso di TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Per impostazione predefinita, i client .NET Redis usano la versione più recente di TLS in .NET Framework 4.5.2 o versioni precedenti e la versione più recente di TLS in .NET Framework 4.6 o versioni successive. Se si usa una versione precedente di .NET Framework, è possibile abilitare TLS 1.2 manualmente:

* **StackExchange.Redis:** impostare `ssl=true` e `sslprotocols=tls12` nella stringa di connessione.
* **ServiceStack.Redis:** seguire le istruzioni [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) e richiedere almeno la versione 5.6 di ServiceStack.Redis.

### <a name="net-core"></a>.NET Core

Per impostazione predefinita, i client .NET Core Redis usano la versione di TLS predefinita del sistema operativo che ovviamente dipende da quest'ultimo. 

A seconda della versione del sistema operativo e di altre patch che hanno eventualmente modificato la versione di TLS predefinita, la versione di TLS del sistema operativo può essere molto varia. Anche se non sono disponibili informazioni complete al riguardo, per il sistema operativo Windows in particolare è possibile trovare altre informazioni [qui](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12). 

Tuttavia, se si usa un sistema operativo precedente, o semplicemente per sicurezza, è consigliabile configurare manualmente la versione TLS preferita tramite il client.


### <a name="java"></a>Java

I client Java per Redis usano TLS 1.0 nella versione 6 di Java o precedente. Jedis, Lettuce e Redisson non possono connettersi alla cache di Azure per Redis se TLS 1.0 è disabilitato nella cache. Aggiornare il framework Java per usare le nuove versioni di TLS.

In Java 7, i client Redis non usano TLS 1.2 per impostazione predefinita ma possono essere configurati appositamente. Jedis consente di specificare le impostazioni TLS sottostanti con il frammento di codice seguente:

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

I client Lettuce e Redisson non supportano ancora la specifica della versione di TLS, quindi se la cache accetta solo connessioni TLS 1.2 si interromperanno. Le correzioni per questi client sono in fase di revisione, quindi si consiglia di verificare se esiste una versione aggiornata con questo supporto.

In Java 8, per impostazione predefinita viene usato TLS 1.2 e nella maggior parte dei casi non è necessario aggiornare la configurazione client. Per sicurezza, eseguire il test dell'applicazione.

### <a name="nodejs"></a>Node.js

Node Redis e IORedis usano TLS 1.2 per impostazione predefinita.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versioni precedenti alla PHP 7: Predis supporta solo TLS 1.0. Queste versioni non funzionano con TLS 1.2, quindi è necessario eseguire l'aggiornamento.
 
* Da PHP 7.0 a PHP 7.2.1: Predis usa solo TLS 1.0 o 1.1 per impostazione predefinita. Per usare TLS 1.2 è possibile sfruttare la soluzione alternativa seguente. Specificare TLS 1.2 quando si crea l'istanza del client:

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

* PHP 7.3 e versioni successive: Predis usa la versione più recente di TLS.

#### <a name="phpredis"></a>PhpRedis

PhpRedis non supporta TLS su nessuna versione PHP.

### <a name="python"></a>Python

Redis-py usa TLS 1.2 per impostazione predefinita.

### <a name="go"></a>GO

Redigo usa TLS 1.2 per impostazione predefinita.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Come configurare la cache di Azure per Redis](cache-configure.md)
