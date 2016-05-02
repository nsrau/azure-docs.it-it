<properties
   pageTitle="Compilazione da riga di comando per Azure | Microsoft Azure"
   description="Compilazione da riga di comando per Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="tarcher" />

# Compilazione da riga di comando per Azure

## Panoramica

È possibile creare un pacchetto per la distribuzione a Azure tramite l'esecuzione di MSBuild al prompt dei comandi. È possibile configurare e definire compilazioni per il debug, la gestione temporanea e la produzione, oltre ad automatizzare parte del processo di compilazione.


## Microsoft Build Engine (MSBuild)

Tramite Microsoft Build Engine (MSBuild) è possibile compilare prodotti in ambienti lab di compilazione in cui Visual Studio non è installato. MSBuild usa per i file di progetto il formato XML, che è estendibile e completamente supportato da Microsoft. In questo formato file è possibile indicare quali elementi devono essere compilati per una o più piattaforme o configurazioni.

È anche possibile eseguire MSBuild al prompt dei comandi, come illustrato in questo argomento. Impostando le proprietà nel prompt dei comandi, è possibile compilare configurazioni specifiche di un progetto. Analogamente, è possibile definire le destinazioni che il comando di MSBuild compilerà. Per altre informazioni sui parametri della riga di comando e su MSBuild, vedere [Riferimenti alla riga di comando di MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## Installare

Come indicato nella procedura seguente, è necessario installare il software e gli strumenti nel server di compilazione prima di poter creare un pacchetto Azure usando MSBuild:

1. Installare .NET Framework 4 o versione successiva, che include MSBuild.

1. Installare gli [strumenti di creazione di Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (cercare il file MicrosoftAzureAuthoringTools-x64.msi o MicrosoftAzureAuthoringTools-x86.msi).

1. Installare le [librerie di Azure per .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (cercare il file MicrosoftAzureLibsForNet-x64.msi o MicrosoftAzureLibs-x86.msi).

1. Copiare il file Microsoft.WebApplication.targets dall'installazione di Visual Studio in un altro computer.

    Il file si trova nella directory C:\\Programmi (x86)\\MSBuild\\Microsoft\\Visual Studio\\v12.0\\WebApplications (v11.0 per Visual Studio 2012) e deve essere copiato nella stessa directory nel server di compilazione.

1. Installare gli [strumenti di Azure per Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Cercare WindowsAzureTools.vs120.exe per la compilazione di progetti Visual Studio 2013.

## Parametri MSBuild

Il modo più semplice per creare un pacchetto consiste nell'eseguire MSBuild con l'opzione `/t:Publish`. Per impostazione predefinita, questo comando crea una directory in relazione alla cartella radice del progetto, ad esempio ProjectDir\\bin\\Configuration\\app.publish. Quando si compila un progetto Azure, vengono generati due file, il file del pacchetto e il file di configurazione di accompagnamento:

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

Per impostazione predefinita, ogni progetto Azure include un file di configurazione del servizio per le compilazioni locali (debug) e un altro per le compilazioni cloud (gestione temporanea o produzione), ma è possibile aggiungere o rimuovere i file di configurazione del servizio in base alle proprie esigenze. Quando si compila un pacchetto all'interno di Visual Studio, viene richiesto di specificare quale file di configurazione del servizio includere nel pacchetto. Quando si compila un pacchetto usando MSBuild, il file di configurazione del servizio locale viene incluso per impostazione predefinita. Per includere un file di configurazione del servizio diverso, impostare la proprietà `TargetProfile` del comando MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Se si vuole usare una directory alternativa per i file di configurazione e del pacchetto archiviati, impostare il percorso usando l'opzione `/p:PublishDir=Directory`, inclusa la barra rovesciata finale.

## Distribuzione

Dopo la compilazione, sarà possibile distribuire il pacchetto in Azure. Per una dimostrazione di questo processo, vedere l'esercitazione nel sito Web di Azure. Per informazioni su come automatizzare questo processo, vedere [Recapito continuo per Servizi cloud in Azure](../cloud-services/cloud-services-dotnet-continuous-delivery).

<!---HONumber=AcomDC_0420_2016-->