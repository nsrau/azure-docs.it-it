---
title: Gestire la scadenza del contenuto Web nella rete per la distribuzione di contenuti di Azure | Microsoft Docs
description: Informazioni su come gestire la scadenza del contenuto di app Web o servizi cloud di Azure, ASP.NET o IIS nella rete CDN di Azure.
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Gestire la scadenza del contenuto Web nella rete per la distribuzione di contenuti di Azure
 Rete CDN di Azure
> [!div class="op_single_selector"]
> * [App Web/Servizi cloud di Azure, ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Archivio BLOB di Azure](cdn-manage-expiration-of-blob-content.md)
> 

I file provenienti da un server Web di origine accessibile pubblicamente possono essere memorizzati nella cache della rete per la distribuzione di contenuti (CDN) di Azure fino allo scadere della relativa durata (TTL). La durata (TTL) è determinata dall'intestazione [`Cache-Control` ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) nella risposta HTTP del server di origine. Questo articolo descrive come impostare le intestazioni `Cache-Control` per la funzionalità App Web di Servizio app di Microsoft Azure, Servizi cloud di Azure, applicazioni ASP.NET e siti Internet Information Services, che hanno tutti una configurazione simile.

> [!TIP]
> È possibile scegliere di non impostare alcuna durata (TTL) per un file. In tal caso, la rete CDN di Azure applica automaticamente una durata (TTL) predefinita di sette giorni.
> 
> Per altre informazioni sull'uso della rete CDN di Azure per velocizzare l'accesso a file e altre risorse, vedere [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>Impostazione delle intestazioni Cache-Control nei file di configurazione
Per il contenuto statico, come immagini e fogli di stile, è possibile controllare la frequenza di aggiornamento modificando i file **applicationHost.config** o **web.config** per l'applicazione Web. L'elemento **system.webServer\staticContent\clientCache** nel file di configurazione imposta l'intestazione `Cache-Control` per il contenuto. Per i file **web.config**, le impostazioni di configurazione influiscono su qualsiasi elemento presente nella cartella e in tutte le sottocartelle, a meno di non eseguirne l'override a livello di sottocartella. Ad esempio, è possibile configurare un'impostazione di durata (TTL) predefinita nella cartella radice per memorizzare nella cache tutto il contenuto statico per tre giorni e quindi impostare una sottocartella con contenuto più variabile per memorizzare nella cache il relativo contenuto solo per sei ore. Sebbene le impostazioni del file **applicationHost.config** interessino tutte le applicazioni nel sito, è possibile eseguirne l'override usando le impostazioni di qualsiasi altro file **web.config** esistente nelle applicazioni.

L'esempio di codice XML seguente mostra l'impostazione di **clientCache** in modo da specificare una durata massima di tre giorni:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Specificando **UseMaxAge**, si aggiunge un'intestazione `Cache-Control: max-age=<nnn>` alla risposta in base al valore specificato nell'attributo **CacheControlMaxAge**. Il formato dell'intervallo di tempo per l'attributo **cacheControlMaxAge** è `<days>.<hours>:<min>:<sec>`. Per altre informazioni sul nodo **clientCache**, vedere [Cache client<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Impostazione delle intestazioni Cache-Control nel codice
Per le applicazioni ASP.NET, è possibile gestire a livello di codice il comportamento di memorizzazione nella cache della rete CDN impostando la proprietà **HttpResponse.Cache**. Per altre informazioni sulla proprietà **HttpResponse.Cache**, vedere [Proprietà HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Per memorizzare nella cache il contenuto dell'applicazione in ASP.NET a livello di codice, eseguire la procedura seguente:
   1. Verificare che il contenuto sia contrassegnato come memorizzabile nella cache impostando `HttpCacheability` su *Public*. 
   2. Impostare un validator della cache chiamando uno dei metodi seguenti:
      - Chiamare `SetLastModified` per impostare un timestamp LastModified.
      - Chiamare `SetETag` per impostare un valore `ETag`.
   3. Facoltativamente, specificare un'ora di scadenza della cache chiamando `SetExpires`. In caso contrario, si applica l'euristica predefinita della cache descritta in precedenza in questo documento.

Ad esempio, per memorizzare nella cache il contenuto per un'ora, aggiungere il codice C# seguente:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Passaggi successivi
* [Vedere informazioni dettagliate sull'elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Vedere la documentazione sulla proprietà **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Vedere la documentazione sulla classe **HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  

