---
title: Gestire la scadenza di contenuti Web nella rete CDN di Azure | Microsoft Docs
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c207d780857a61d4b1fc0f39e6185cae67abc955
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Gestire la scadenza del contenuto di app Web o servizi cloud di Azure, ASP.NET o IIS nella rete CDN di Azure
> [!div class="op_single_selector"]
> * [App Web/Servizi cloud di Azure, ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Servizio BLOB del servizio di archiviazione di Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

I file provenienti da un server Web di origine accessibile pubblicamente possono essere memorizzati nella cache della rete CDN di Azure fino allo scadere della relativa durata (TTL).  La durata (TTL) è determinata dall'[intestazione *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) nella risposta HTTP del server di origine.  Questo articolo descrive come impostare le intestazioni `Cache-Control` per App Web di Azure, Servizi cloud di Azure, applicazioni ASP.NET e siti Internet Information Services, che hanno tutti una configurazione simile.

> [!TIP]
> È possibile scegliere di non impostare alcuna durata (TTL) per un file.  In tal caso, la rete CDN di Azure applica automaticamente una durata (TTL) predefinita di sette giorni.
> 
> Per altre informazioni sull'uso della rete CDN di Azure per velocizzare l'accesso a file e altre risorse, vedere la [panoramica della rete CDN di Azure](cdn-overview.md).
> 
> 

## <a name="setting-cache-control-headers-in-configuration"></a>Impostazione delle intestazioni Cache-Control nella configurazione
Per il contenuto statico, come immagini e fogli di stile, è possibile controllare la frequenza di aggiornamento modificando i file **applicationHost.config** o **web.config** per l'applicazione Web.  L'elemento **system.webServer\staticContent\clientCache** nel file di configurazione imposterà l'intestazione `Cache-Control` per il contenuto. Per **web.config**, le impostazioni di configurazione influiranno su qualsiasi elemento presente nella cartella e in tutte le sottocartelle, a meno di non eseguirne l'override a livello di sottocartella.  Ad esempio, è possibile impostare una durata (TTL) predefinita nella radice per memorizzare nella cache tutto il contenuto statico per tre giorni, ma impostare un valore di sei ore per una sottocartella che include contenuto più variabile.  Per **applicationHost.config**, tutte le applicazioni nel sito saranno interessate, ma è possibile eseguirne l'override nei file **web.config** nelle applicazioni.

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

Specificando **UseMaxAge**, si aggiunge un'intestazione `Cache-Control: max-age=<nnn>` alla risposta in base al valore specificato nell'attributo **CacheControlMaxAge**. Il formato dell'intervallo di tempo per l'attributo **cacheControlMaxAge** è <days>.<hours>:<min>:<sec>. Per altre informazioni sul nodo **clientCache**, vedere l'argomento relativo alla [cache client <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Impostazione delle intestazioni Cache-Control nel codice
Per le applicazioni ASP.NET, è possibile impostare a livello di codice il comportamento di memorizzazione nella cache della rete CDN impostando la proprietà **HttpResponse.Cache** . Per altre informazioni sulla proprietà **HttpResponse.Cache**, vedere [Proprietà HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Se si vuole memorizzare nella cache il contenuto dell'applicazione a livello di codice in ASP.NET, assicurarsi che tale contenuto sia contrassegnato come inseribile nella cache impostando HttpCacheability su *Public*. Assicurarsi anche che sia impostato un validator della cache. Il validator della cache può essere un timestamp dell'ultima modifica impostato chiamando SetLastModified oppure un valore etag impostato chiamando SetETag. Facoltativamente, è anche possibile specificare una scadenza della cache chiamando SetExpires oppure affidarsi all'euristica predefinita della cache descritta prima in questo documento.  

Ad esempio, per memorizzare nella cache il contenuto per un'ora, aggiungere il codice seguente:  

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

