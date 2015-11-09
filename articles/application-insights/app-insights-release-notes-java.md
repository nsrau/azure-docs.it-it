<properties 
	pageTitle="Note sulla versione per Application Insights per Java" 
	description="Gli aggiornamenti più recenti per Java SDK." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="awills"/>
 
# Note sulla versione per Application Insights SDK per Java

[Application Insights SDK per Java](app-insights-java-get-started.md) invia dati di telemetria sull'app attiva ad [Application Insights](http://azure.microsoft.com/services/application-insights/), dove è possibile analizzarne l'utilizzo e le prestazioni.

#### Per installare l'SDK nell'applicazione

Vedere [Introduzione all'SDK per Java](app-insights-java-get-started.md).

#### Per eseguire l'aggiornamento all'SDK più recente 

Dopo l'aggiornamento, è necessario unire nuovamente le personalizzazioni apportate al file ApplicationInsights.xml. Eseguirne una copia per confrontarla con il nuovo file.

*Se si usa Maven o Gradle*

1. Se è stato specificato un determinato numero di versione in pom.xml o build.gradle, è necessario aggiornarlo.
2. Aggiornare le dipendenze del progetto.

*In caso contrario*

* Scaricare la versione più recente delle [librerie di Azure per Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) e sostituire quelle precedenti. 
 
Confrontare il vecchio e il nuovo file ApplicationInsights.xml. Molte delle modifiche visibili sono dovute all'aggiunta e alla rimozione di moduli. Ripristinare eventuali personalizzazioni apportate.

## Versione 1.0.2

- Compatibilità con Docker
- Correzioni di bug - individuare tutte le eccezioni, gestire tutti i codici di stato delle operazioni riuscite

## Versione 1.0.1
- L'[agente Java](app-insights-java-agent.md) raccoglie le informazioni sulle dipendenze per i seguenti elementi:
	- Chiamate HTTP eseguite tramite HttpClient, OkHttp e RestTemplate (Spring).
	- Chiamate a Redis effettuate tramite il client Jedis. Quando viene superata una soglia configurabile, l’SDK recupererà inoltre gli argomenti della chiamata.
	- Chiamate JDBC eseguite con i client Oracle DB e Apache Derby DB.
	- Supporta il tipo di query 'executeBatch' per le istruzioni preparate: l’SDK visualizzerà l'istruzione con il numero di batch.
	- Fornire il piano di query per i client JDBC che dispongono del relativo supporto (MySql, PostgreSql); il piano di query viene recuperato solo quando viene superata una soglia configurabile

## Versione 1.0.0
- Aggiunta di supporto per il plug-in di scrittura di Application Insights per CollectD.
- Aggiunta di supporto per l'agente Java di Application Insights.
- Correzione per un problema di compatibilità relativo al supporto delle versioni HttpClient 4.2 e successive.

## Versione 0.9.6
- Rendere Java SDK compatibile con servlet v2.5 e HttpClient pre-v4.3.
- Aggiunta del supporto per gli intercettori di Java EE.
- Rimozione di dipendenze ridondanti dall'appender Logback.

## Versione 0.9.5  

- Correzione di un problema per cui gli eventi personalizzati non sono correlati con utenti/sessioni a causa di errori di analisi dei cookie.  
- Migliorata la logica per risolvere il percorso del file di configurazione ApplicationInsights.xml.
- I cookie di utente e sessione anonimi non verranno generati sul lato server. Per implementare il rilevamento di utenti e sessioni per le app Web, è ora necessaria la strumentazione con JavaScript SDK. I cookie da JavaScript SDK verranno comunque rispettati. Si noti che questa modifica potrebbe provocare una rideterminazione significativa dei conteggi relativi a utenti e sessioni, poiché vengono ora contate solo le sessioni originate dagli utenti.

## Versione 0.9.4

- Supporto della raccolta dei contatori delle prestazioni da computer Windows a 32 bit.
- Supporto del rilevamento manuale delle dipendenze con una nuova API del metodo ```trackDependency```.
- Possibilità di apporre un tag a un elemento di telemetria come sintetico aggiungendo una proprietà ```SyntheticSource``` all'elemento segnalato.
 

<!---HONumber=Nov15_HO1-->