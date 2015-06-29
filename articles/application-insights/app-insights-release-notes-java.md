<properties 
	pageTitle="Note sulla versione per Application Insights" 
	description="Gli aggiornamenti più recenti." 
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
	ms.date="06/18/2015" 
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

## Versione 0.9.6
- Rendere l'SDK Java compatibile con servlet v2.5 e HttpClient pre-v4.3
- Aggiunta del supporto per gli intercettori di Java EE
- Rimozione di dipendenze ridondanti dall'appender Logback

## Versione 0.9.5  

- Correzione di un problema per cui gli eventi personalizzati non sono correlati con utenti/sessioni a causa di errori di analisi dei cookie.  
- Migliorata la logica per risolvere il percorso del file di configurazione ApplicationInsights.xml.
- Rimosso il rilevamento di sessioni e utenti \(che verrà eseguito soltanto dagli SDK lato client\).

## Versione 0.9.4

- Supporto della raccolta dei contatori delle prestazioni da computer Windows a 32 bit.
- Supporto del rilevamento manuale delle dipendenze con una nuova API del metodo ```trackDependency```.
- Possibilità di apporre un tag a un elemento di telemetria come sintetico aggiungendo una proprietà ```SyntheticSource``` all'elemento segnalato.
 

<!---HONumber=58_postMigration-->