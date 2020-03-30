---
title: Rimuovere TLS 1.0 e 1.1 dall'uso con la cache di Azure per RedisRemove TLS 1.0 and 1.1 from use with Azure Cache for Redis
description: Informazioni su come rimuovere TLS 1.0 e 1.1 dall'applicazione quando si comunica con la cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 0d28469820f63f63089d9b91d57ccd7fe75c8b95
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348646"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Rimuovere TLS 1.0 e 1.1 dall'uso con la cache di Azure per RedisRemove TLS 1.0 and 1.1 from use with Azure Cache for Redis

C'è un push a livello di settore verso l'uso esclusivo di Transport Layer Security (TLS) versione 1.2 o successiva. TLS versioni 1.0 e 1.1 sono noti per essere suscettibili ad attacchi come BEAST e POODLE, e per avere altri punti deboli comuni vulnerabilità ed esposizione (CVE). Inoltre, non supportano i moderni metodi di crittografia e suite di crittografia consigliati dagli standard di conformità PCI (Payment Card Industry). Questo blog sulla [sicurezza TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) spiega alcune di queste vulnerabilità in modo più dettagliato.

Come parte di questo sforzo, apporteremo le modifiche seguenti alla cache di Azure per Redis:As a part of this effort, we'll be making the following changes to Azure Cache for Redis:

* **Fase 1:** La versione TLS minima predefinita verrà configurata su 1.2 per le istanze della cache appena create. (Questo utilizzato per essere TLS 1.0.) Le istanze della cache esistenti non verranno aggiornate a questo punto. Potrai modificare la [versione TLS minima](cache-configure.md#access-ports) a 1.0 o 1.1 per garantire la compatibilità con le versioni precedenti, se necessario. Questa modifica può essere eseguita tramite il portale di Azure o altre API di gestione.
* **Fase 2:** Smetteremo di supportare LE versioni TLS 1.0 e 1.1. Dopo questa modifica, all'applicazione verrà richiesto di utilizzare TLS 1.2 o versione successiva per comunicare con la cache.

Inoltre, come parte di questa modifica, rimuoveremo il supporto per le suite di cifre meno recenti e non sicure.  Le nostre suite di crittografia supportate saranno limitate a quanto segue quando la cache è configurata con una versione TLS minima di 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

In questo articolo vengono fornite indicazioni generali su come rilevare le dipendenze da queste versioni TLS precedenti e rimuoverle dall'applicazione.

Le date in cui queste modifiche diventano effettive sono:

| Cloud               | Fase 1 Data di inizio | Fase 2 Data di inizio      |
|---------------------|--------------------|-------------------------|
| Azure (globale)Azure (global)      |  13 gennaio 2020  | 11 maggio 2020 (esteso) |
| Azure Government    |  del 13 marzo 2020    | l'11 maggio 2020            |
| Azure Germania       |  del 13 marzo 2020    | l'11 maggio 2020            |
| Azure Cina         |  del 13 marzo 2020    | l'11 maggio 2020            |

## <a name="check-whether-your-application-is-already-compliant"></a>Verificare se l'applicazione è già conforme

Il modo più semplice per scoprire se l'applicazione funzionerà con TLS 1.2 consiste nell'impostare il valore **di versione TLS minima** su TLS 1.2 in una cache di test o di gestione temporanea che utilizza. L'impostazione Versione minima TLS è riportata nelle impostazioni avanzate dell'istanza della cache nel portale di Azure.The **Minimum TLS version** setting is in the Advanced [settings](cache-configure.md#advanced-settings) of your cache instance in the Azure portal. Se l'applicazione continua a funzionare come previsto dopo questa modifica, è probabilmente conforme. Potrebbe essere necessario configurare alcune librerie client Redis usate dall'applicazione in modo specifico per abilitare TLS 1.2, in modo che possano connettersi alla cache di Azure per Redis tramite tale protocollo di sicurezza.

## <a name="configure-your-application-to-use-tls-12"></a>Configurare l'applicazione per l'utilizzo di TLS 1.2

La maggior parte delle applicazioni utilizza librerie client Redis per gestire la comunicazione con le cache. Ecco le istruzioni per configurare alcune delle librerie client più diffuse, in vari linguaggi di programmazione e framework, per utilizzare TLS 1.2.

### <a name="net-framework"></a>.NET Framework

I client Redis .NET utilizzano la versione TLS meno recente per impostazione predefinita in .NET Framework 4.5.2 o versioni precedenti e utilizzano la versione TLS più recente in .NET Framework 4.6 o versioni successive. Se si utilizza una versione precedente di .NET Framework, è possibile abilitare manualmente TLS 1.2:

* **StackExchange.Redis:** Set `ssl=true` `sslprotocols=tls12` e nella stringa di connessione.
* **ServiceStack.Redis:** Seguire le [istruzioni di ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

I client Redis .NET Core utilizzano la versione TLS più recente per impostazione predefinita.

### <a name="java"></a>Java

I client Java Redis utilizzano TLS 1.0 in Java versione 6 o precedente. Jedis, Latuce e Redisson non possono connettersi alla cache di Azure per Redis se TLS 1.0 è disabilitato nella cache. Aggiornare il framework Java per utilizzare le nuove versioni TLS.

Per Java 7, i client Redis non utilizzano TLS 1.2 per impostazione predefinita, ma possono essere configurati per esso. Jedis consente di specificare le impostazioni TLS sottostanti con il frammento di codice seguente:Jedis allows you to specify the underlying TLS settings with the following code snippet:

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

I client Lettuce e Redisson non supportano ancora la specifica della versione TLS, quindi si interromperanno se la cache accetta solo connessioni TLS 1.2. Correzioni per questi client sono in fase di revisione, in modo da verificare con tali pacchetti per una versione aggiornata con questo supporto.

In Java 8, TLS 1.2 viene utilizzato per impostazione predefinita e nella maggior parte dei casi non è necessario richiedere aggiornamenti alla configurazione client. Per sicurezza, testare l'applicazione.

### <a name="nodejs"></a>Node.js

Node Redis e IORedis utilizzano TLS 1.2 per impostazione predefinita.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versioni precedenti a PHP 7: Predis supporta solo TLS 1.0. Queste versioni non funzionano con TLS 1.2; è necessario eseguire l'aggiornamento per utilizzare TLS 1.2.
 
* Da PHP 7.0 a PHP 7.2.1: Predis utilizza solo TLS 1.0 o 1.1 per impostazione predefinita. È possibile utilizzare la soluzione seguente per utilizzare TLS 1.2. Specificare TLS 1.2 quando si crea l'istanza del client:

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

* PHP 7.3 e versioni successive: Predis utilizza la versione TLS più recente.

#### <a name="phpredis"></a>PhpRedis

PhpRedis non supporta TLS su nessuna versione PHP.

### <a name="python"></a>Python

Redis-py utilizza TLS 1.2 per impostazione predefinita.

### <a name="go"></a>GO

Redigo utilizza TLS 1.2 per impostazione predefinita.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Come configurare Cache Redis di Azure](cache-configure.md)
