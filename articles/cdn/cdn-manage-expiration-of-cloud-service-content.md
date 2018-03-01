---
title: Gestire la scadenza del contenuto Web nella rete per la distribuzione di contenuti di Azure | Microsoft Docs
description: Informazioni su come gestire la scadenza del contenuto di app Web o servizi cloud di Azure, ASP.NET o IIS nella rete CDN di Azure.
services: cdn
documentationcenter: .NET
author: dksimpson
manager: akucer
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: mazha
ms.openlocfilehash: db7b5053cb926d2ec86c7feea4ac411acbeb1ae2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Gestire la scadenza del contenuto Web nella rete per la distribuzione di contenuti di Azure
> [!div class="op_single_selector"]
> * [Contenuto Web di Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Archivio BLOB di Azure](cdn-manage-expiration-of-blob-content.md)
> 

I file provenienti da server Web di origine accessibili pubblicamente possono essere memorizzati nella cache della rete per la distribuzione di contenuti (CDN) di Azure fino allo scadere della relativa durata (TTL). La durata (TTL) è determinata dall'intestazione `Cache-Control` nella risposta HTTP del server di origine. Questo articolo descrive come impostare le intestazioni `Cache-Control` per la funzionalità App Web di Servizio app di Microsoft Azure, Servizi cloud di Azure, applicazioni ASP.NET e siti Internet Information Services (IIS), che hanno tutti una configurazione simile. È possibile impostare l'intestazione `Cache-Control` usando i file di configurazione o a livello di codice. 

È inoltre possibile controllare le impostazioni della cache dal portale di Azure mediante l'impostazione delle [regole di memorizzazione nella cache della rete CDN](cdn-caching-rules.md). Se si creano una o più regole di memorizzazione nella cache e si imposta il relativo comportamento su **Ignora** o **Ignora cache**, le impostazioni di memorizzazione nella cache fornite in origine e descritte in questo articolo vengono ignorate. Per informazioni sui concetti generali sulla memorizzazione nella cache, vedere [Come funziona la memorizzazione nella cache](cdn-how-caching-works.md).

> [!TIP]
> È possibile scegliere di non impostare alcuna durata (TTL) per un file. In questo caso, la rete CDN di Azure applica automaticamente una durata (TTL) predefinita di sette giorni, a meno che non siano state configurate le regole di memorizzazione nella cache nel portale di Azure. Questa impostazione predefinita di durata (TTL) si applica solo alle ottimizzazioni di distribuzione web generali. Per le ottimizzazioni di file di grandi dimensioni, il valore TTL predefinito è un giorno e per le ottimizzazioni dei flussi dei file multimediali, il valore TTL predefinito è un anno.
> 
> Per altre informazioni sull'uso della rete CDN di Azure per velocizzare l'accesso a file e altre risorse, vedere [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Impostazione delle intestazioni Cache-Control tramite le regole di memorizzazione nella cache della rete CDN
Il metodo preferito per la configurazione dell'intestazione `Cache-Control` di un server Web consiste nell'usare le regole di memorizzazione nella cache nel portale di Azure. Per altre informazioni sulle regole di memorizzazione nella cache della rete CDN, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole](cdn-caching-rules.md).

> [!NOTE] 
> Le regole di memorizzazione nella cache sono disponibili solo per profili di **rete CDN di Azure di Verizon Standard** e di **rete CDN di Azure di Akamai Standard**. Per i profili di **rete CDN di Azure di Verizon Premium** è possibile usare il [motore delle regole della rete CDN di Azure](cdn-rules-engine.md) nel portale a cui si accedere tramite il pulsante **Gestisci** per funzionalità analoghe.

**Per passare alla pagina delle regole di memorizzazione nella cache della rete CDN**:

1. Nel portale di Azure selezionare un profilo della rete CDN, quindi selezionare l'endpoint per il server Web.

2. In Impostazioni nel riquadro sinistro selezionare **Regole di memorizzazione nella cache**.

   ![Pulsante Regole di memorizzazione nella cache della rete CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Viene visualizzata la pagina **Regole di memorizzazione nella cache**.

   ![Pagina Regole di memorizzazione nella cache della rete CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Per configurare le intestazioni Cache-Control di un server Web tramite regole di memorizzazione nella cache globali:**

1. In **Regole di memorizzazione nella cache globali**, impostare **Comportamento di memorizzazione nella cache della stringa di query** su **Ignora stringhe di query** e impostare **Comportamento di memorizzazione nella cache** su **Ignora**.
      
2. Per **Durata scadenza cache** immettere 3600 nella casella **Secondi** o 1 nella casella **Ore**. 

   ![Esempio di regole di memorizzazione nella cache globali della rete CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Questa regola di memorizzazione nella cache globale imposta una durata di un'ora per la cache e influisce su tutte le richieste per l'endpoint. Esegue l'override di qualsiasi intestazione HTTP `Cache-Control` o `Expires` inviata dal server di origine specificato dall'endpoint.   

3. Selezionare **Salva**.

**Per configurare le intestazioni Cache-Control di un server Web tramite regole di memorizzazione nella cache personalizzate:**

1. In **Regole di memorizzazione nella cache personalizzate** creare due condizioni di corrispondenza:

     a. Per la prima condizione di corrispondenza impostare **Condizione di corrispondenza** su **Percorso** e immettere `/webfolder1/*` per **Valori di corrispondenza**. Impostare **Comportamento di memorizzazione nella cache** su **Ignora** e immettere 4 nella casella **Ore**.

     b. Per la seconda condizione di corrispondenza impostare **Condizione di corrispondenza** su **Percorso** e immettere `/webfolder1/file1.txt` per **Valori di corrispondenza**. Impostare **Comportamento di memorizzazione nella cache** su **Ignora** e immettere 2 nella casella **Ore**.

    ![Esempio di regole di memorizzazione nella cache personalizzate della rete CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    La prima regola di memorizzazione nella cache personalizzata imposta una durata della cache di quattro ore per qualsiasi file nella cartella `/webfolder1` nel server di origine specificato dall'endpoint. La seconda regola esegue l'override della prima regola solo per il file `file1.txt` e imposta una durata della cache di due ore per tale file.

2. Selezionare **Salva**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Impostazione delle intestazioni Cache-Control tramite dei file di configurazione
Per il contenuto statico, come immagini e fogli di stile, è possibile controllare la frequenza di aggiornamento modificando i file di configurazione **applicationHost.config** o **Web.config** per l'applicazione Web. Per impostare l'intestazione `Cache-Control` per il contenuto, usare l'elemento `<system.webServer>/<staticContent>/<clientCache>` in qualsiasi file.

### <a name="using-applicationhostconfig-files"></a>Uso dei file Applicationhost.config
Il file **ApplicationHost.config** è il file radice del sistema di configurazione IIS. Le impostazioni di configurazione nel file **ApplicationHost.config** interessano tutte le applicazioni nel sito, ma se ne esegue l'override usando le impostazioni di qualsiasi altro file **Web.config** esistente per un'applicazione web.

### <a name="using-webconfig-files"></a>Uso dei file Web.config
Con un file **Web.config**, è possibile personalizzare il comportamento dell'applicazione web intera o di una directory specifica sull'applicazione web. In genere, si ha almeno un file **Web.config** nella cartella radice dell'applicazione web. Per ciascun file **Web.config** in una cartella specifica, le impostazioni di configurazione influiscono su qualsiasi elemento presente nella cartella e in tutte le sottocartelle, a meno di non eseguirne l'override a livello di sottocartella da un altro file **Web.config**. 

Ad esempio, è possibile impostare un elemento `<clientCache>` in un file **Web.config** nella cartella radice dell'applicazione web per memorizzare nella cache qualsiasi contenuto statico sull'applicazione web per tre giorni. È anche possibile aggiungere un file **Web.config** in una sottocartella con contenuto più variabile (ad esempio, `\frequent`) e impostare il relativo elemento `<clientCache>` per memorizzare nella cache il contenuto della sottocartella per sei ore. Il risultato è che il contenuto nell'intero sito Web è memorizzato nella cache per tre giorni, ad eccezione di qualsiasi contenuto nella directory `\frequent`, che verrà memorizzato nella cache solo per sei ore.  

L'esempio di file di configurazione XML seguente mostra come impostare l'elemento `<clientCache>` per specificare una durata massima di tre giorni:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Per usare l'attributo **cacheControlMaxAge**, è necessario impostare il valore dell'attributo **cacheControlMode** su `UseMaxAge`. Questa impostazione ha fatto sì che l'intestazione HTTP e la direttiva, `Cache-Control: max-age=<nnn>`, fossero aggiunte alla risposta. Il formato del valore dell'intervallo di tempo per l'attributo **cacheControlMaxAge** è `<days>.<hours>:<min>:<sec>`. Il valore viene convertito in secondi e viene usato come il valore della direttiva `Cache-Control` `max-age`. Per altre informazioni sull'elemento `<clientCache>`, vedere [Cache client<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Impostazione delle intestazioni Cache-Control a livello di programmazione
Per le applicazioni ASP.NET, gestire a livello di codice il comportamento di memorizzazione nella cache della rete CDN impostando la proprietà **HttpResponse.Cache** dell'API.NET. Per informazioni sulla proprietà **HttpResponse.Cache**, vedere [Proprietà HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Per memorizzare nella cache il contenuto dell'applicazione in ASP.NET a livello di codice, eseguire la procedura seguente:
   1. Verificare che il contenuto sia contrassegnato come memorizzabile nella cache impostando `HttpCacheability` su `Public`. 
   2. Impostare un validator della cache chiamando uno dei metodi `HttpCachePolicy` seguenti:
      - Chiamare `SetLastModified` per impostare un valore di timestamp per l'intestazione`Last-Modified`.
      - Chiamare `SetETag` per impostare un valore per l'intestazione`ETag`.
   3. Facoltativamente, specificare un'ora di scadenza della cache chiamando `SetExpires` per impostare un valore dell'intestazione `Expires`. In caso contrario, si applica l'euristica predefinita della cache descritta in precedenza in questo documento.

Ad esempio, per memorizzare nella cache il contenuto per un'ora, aggiungere il codice C# seguente:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Test dell'intestazione Cache-Control
È possibile verificare facilmente le impostazioni di durata (TTL) del contenuto web. Con gli [strumenti di sviluppo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del browser verificare che il contenuto web includa l'intestazione della risposta `Cache-Control`. È anche possibile usare uno strumento come **wget**, [Postman](https://www.getpostman.com/) o [Fiddler](http://www.telerik.com/fiddler) per esaminare le intestazioni della risposta.

## <a name="next-steps"></a>Passaggi successivi
* [Vedere informazioni dettagliate sull'elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Vedere la documentazione sulla proprietà **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Vedere la documentazione sulla classe **HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Informazioni sui concetti di memorizzazione nella cache](cdn-how-caching-works.md)
