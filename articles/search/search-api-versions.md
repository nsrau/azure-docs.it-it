<properties
   pageTitle="API versions of Azure Search | Microsoft Azure | Search API"
   description="Version policy for Azure Search REST APIs and the client library in the .NET SDK."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="03/08/2016"
   ms.author="brjohnst"/>

# Versioni API in Ricerca di Azure

Ricerca di Azure applica aggiornamenti delle funzionalità a intervalli regolari. A volte, ma non sempre, questi aggiornamenti richiedono la pubblicazione di una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Questa pubblicazione consente di controllare come e quando integrare gli aggiornamenti del servizio di ricerca nel codice.

Di norma, le nuove versioni vengono pubblicate solo se necessario, perché ciò può comportare alcune operazioni di aggiornamento del codice per l'utilizzo di una nuova versione API. Verrà pubblicata una nuova versione solo in caso di modifica di alcuni aspetti dell'API che va a interrompere la compatibilità con le versioni precedenti. Ciò può verificarsi in caso di correzioni alle funzionalità esistenti o di nuove funzionalità che modificano la superficie API esistente.

Si applica la stessa regola per gli aggiornamenti SDK. L'SDK di Ricerca di Azure segue le regole di [versionamento semantico](http://semver.org/), il che significa che la versione ha tre parti: principale, secondaria e numero di build (ad esempio, 1.1.0). Una nuova versione principale del SDK verrà rilasciata solo in caso di modifiche che vanno a interrompere la compatibilità con le versioni precedenti. Per aggiornamenti di funzionalità senza interruzione di compatibilità verrà incrementata la versione secondaria e, in caso di correzione di bug, verrà incrementata solo la versione build.

##Snapshot delle versioni correnti 

Di seguito è illustrato uno snapshot delle versioni correnti di tutte le interfacce di programmazione di Ricerca di Azure. Le guide di orientamento e altri dettagli sono disponibili nelle sezioni successive di questo documento.

Interfacce|Versione principale più recente|Stato
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1\.1|Disponibile a livello generale, rilasciata a febbraio 2016
[API REST del servizio](https://msdn.microsoft.com/library/azure/dn798935.aspx)|28/02/2015|Disponibile a livello generale
[Anteprima API REST del servizio](search-api-2015-02-28-preview.md)|28/02/2015-Anteprima|Preview
[API REST di gestione ](https://msdn.microsoft.com/library/azure/dn832684.aspx)|19/08/2015|Disponibile a livello generale

Per le API REST, è necessario includere la `api-version` ad ogni chiamata. Questo facilita l'identificazione di una versione specifica, ad esempio un'API di anteprima. L'esempio seguente illustra come viene specificato il parametro `api-version`:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Benché ogni richiesta abbia una `api-version`, si consiglia di utilizzare la stessa versione per tutte le richieste API. Ciò vale soprattutto nel caso in cui nuove versioni API introducano attributi oppure operazioni non riconosciuti dalle versioni precedenti. La combinazione di diverse versioni API può avere conseguenze impreviste e deve essere evitata.
> 
L'API REST del servizio e l'API REST di gestione sono sottoposte al controllo delle versioni in modo indipendente tra loro. L'eventuale somiglianza del numero di versione è causale.

Le API disponibili a livello generale (o GA) possono essere utilizzate nell'ambiente di produzione e sono soggette a contratti di servizio di Azure. Le versioni di anteprima offrono funzionalità sperimentali che non sempre vengono migrate a una versione GA. **È consigliabile evitare l'utilizzo delle API di anteprima in applicazioni di produzione.**

##Guida di orientamento alla versione SDK

Ogni versione di .NET SDK è destinata a una particolare versione dell'API REST del servizio. Le funzionalità sono distribuite prima nell'API REST e quindi implementate nel SDK.

.NET SDK è ora disponibile a livello generale e sono già in corso i lavori per la prossima versione. La tabella seguente riporta le versioni future del SDK per dare un'idea delle novità in arrivo.

Versione di .NET SDK|Versione API REST|Funzionalità|ETA
----------------|----------------|--------|---
1\.1|28/02/2015|Sintassi di query Lucene|Febbraio 2016
2\. x-anteprima|28/02/2015-Anteprima|Analizzatori personalizzati, indicizzatore BLOB di Azure, mapping dei campi, ETag|Le funzionalità verranno introdotte nel primo trimestre del 2016
2\.x|Nuova versione API GA|Uguale alla 2.x-anteprima|Subito dopo il completamento di 2.x-anteprima

##Informazioni sull'anteprima e sulle versioni disponibili a livello generale

Ricerca di Azure rilascia sempre in via preliminare funzionalità sperimentali tramite l'API REST, quindi tramite versioni non definitive di .NET SDK. Un elenco di funzionalità di anteprima è incluso in [Novità dell'ultimo aggiornamento di Ricerca di Azure](search-latest-updates.md).

La migrazione alla versione GA delle funzionalità di anteprima non è garantita. Mentre le funzionalità in una versione GA vengono considerate stabili e difficilmente modificabili, ad eccezione di piccoli miglioramenti e correzioni compatibili con le versioni precedenti, le funzionalità di anteprima sono disponibili per il testing e la sperimentazione, allo scopo di raccogliere commenti e suggerimenti sulla relativa progettazione e implementazione.

Tuttavia, poiché le funzionalità di anteprima sono soggette a modifiche, è consigliabile evitare di scrivere codice di produzione dipendente dalle versioni di anteprima. Se si utilizza una versione di anteprima precedente, è consigliabile eseguire la migrazione alla versione disponibile a livello generale (GA).

- Per .NET SDK: le linee guida per la migrazione del codice sono disponibili nell’articolo sull’[aggiornamento di .NET SDK](search-dotnet-sdk-migration.md).
- Per l'API REST: le linee guida per la migrazione del codice sono disponibili nell'articolo sulla [transizione dalla versione di anteprima alla GA](search-transition-from-preview.md).

Con disponibilità a livello generale si intende che Ricerca di Azure è ora soggetta al contratto di servizio (SLA). Il contratto di servizio è reperibile in [Contratto di Servizio per Ricerca](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<!---HONumber=AcomDC_0309_2016-->