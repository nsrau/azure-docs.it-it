<properties linkid="mobile-services-dotnet-backend-how-to-use" urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Use the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg" />
# Usare il back-end .NET di Servizi mobili

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo articolo vengono fornite informazioni dettagliate ed esempi sull'uso del back-end .NET in Servizi mobili di Azure. Questo argomento è suddiviso nelle sezioni seguenti:

+ [Introduzione][Introduzione]
+ [Operazioni su tabella][Operazioni su tabella]

## <a name="intro"></a>Introduzione

Il back-end .NET di Servizi mobili consente di scrivere logica di business back-end avanzata usando l'[API Web di ASP.NET][API Web di ASP.NET] insieme al linguaggio .NET di propria scelta. Servizi mobili espone una piccola superficie del modello di programmazione, come i [pacchetti NuGet][pacchetti NuGet], che agevolano l'accesso trasparente al proprio servizio dagli SDK multipiattaforma del client di Servizi mobili per Windows, Android, iOS e così via. Queste API garantiscono inoltre che l'aggiunta di supporto per l'autenticazione e per le notifiche push risulti il più possibile semplificato. Il modello di programmazione, tuttavia, si basa per la maggior parte su API Web, pertanto gli sviluppatori che ne hanno familiarità si troveranno decisamente a proprio agio.

## <a name="table-scripts"></a>Operazioni su tabella

Il backend .NET di Servizi mobili fornisce un'astrazione del tipo a "tabella" universale, che rappresenta un'API HTTP basata su CRUD per l'archiviazione del database. Tale astrazione elimina le problematiche legate all'archiviazione dalla logica di business e consente al servizio mobile di esporre archivi dati diversi in un formato di trasmissione coerente e basato su JSON, di semplice comprensione da parte degli SDK multipiattaforma del client di Servizi mobili.

Alla base di questo modello di programmazione vi è la classe [**TableController<t>**][**TableController<t>**], che è una normale API Web [**ApiController**][**ApiController**], personalizzata per un modello di accesso ai dati di tipo CRUD. Grazie a [Entity Framework][Entity Framework], **TableController** può usare una varietà di archivi dati, compresi SQL, [Archiviazione tabelle di Azure][Archiviazione tabelle di Azure], [MongoDB][MongoDB] o un archivio dati di propria scelta.

  [Introduzione]: #intro
  [Operazioni su tabella]: #table-scripts
  [API Web di ASP.NET]: http://www.asp.net/web-api
  [pacchetti NuGet]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [**TableController<t>**]: http://msdn.microsoft.com/library/dn643359.aspx
  [**ApiController**]: http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx
  [Entity Framework]: http://msdn.microsoft.com/data/ef.aspx
  [Archiviazione tabelle di Azure]: http://azure.microsoft.com/documentation/services/storage/
  [MongoDB]: http://www.mongodb.org
