<properties
	pageTitle="Risoluzione dei problemi di Diagnostica di Azure"
	description="Risoluzione dei problemi di Diagnostica di Azure nei servizi cloud di Azure, nelle macchine virtuali e"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Risoluzione dei problemi di Diagnostica di Azure
Informazioni sulla risoluzione dei problemi relativi all'uso di Diagnostica di Azure. Per altre informazioni su Diagnostica di Azure, vedere [Panoramica di Diagnostica di Azure](azure-diagnostics.md#cloud-services).

## Mancato avvio di Diagnostica Azure
La diagnostica è costituita da due componenti: un plug-in agente guest e l'agente di monitoraggio.

In un ruolo di servizi cloud, i file di log del plug-in dell'agente guest si trovano nel file seguente:

	*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\CommandExecution.log

In una macchina virtuale di Azure, i file di log del plug-in dell'agente guest si trovano nel file seguente:

		C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\CommandExecution.log

Il plug-in restituisce i seguenti codici di errore:

Codice di uscita|Descrizione
---|---
0|Completamento della procedura.
-1|Errore generico.
-2|Impossibile caricare il file rcf. <p>Questo è un errore interno che si dovrebbe verificare solo se il programma di avvio del plug-in agente guest viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.
-3|Impossibile caricare il file di configurazione di Diagnostica. <p> <p> Soluzione: questo errore è causato da un file di configurazione che non supera la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema.
-4|Un'altra istanza della diagnostica dell'agente di monitoraggio sta già usando la directory delle risorse locali. <p> <p> Soluzione: specificare un valore diverso per **LocalResourceDirectory**.
-6|L’utilità di avvio del plug-in dell’agente guest ha tentato di avviare la Diagnostica con una riga di comando non valida. <p><p>Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.
-10|Il plug-in di Diagnostica ha generato un'eccezione non gestita.
-11|L'agente guest non è stato in grado di creare il processo responsabile dell'avvio e del monitoraggio dell'agente di monitoraggio. <p><p> Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.<p>
-101|Argomenti non validi durante la chiamata del plug-in di Diagnostica. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.
-102|Il processo del plug-in non è in grado di inizializzarsi. <p><p> Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.
-103|Il processo del plug-in non è in grado di inizializzarsi. In particolare, non è in grado di creare l'oggetto logger. <p> <p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.
-104|Impossibile caricare il file rcf fornito dall'agente guest. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.
-105|Il plug-in di Diagnostica non è in grado di aprire il file di configurazione di Diagnostica. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.
-106|Impossibile caricare il file di configurazione di Diagnostica. <p><p> Soluzione: questo errore è causato da un file di configurazione che non supera la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema. Il file XML fornito all'estensione Diagnostica si trova nella cartella *%SystemDrive%\\WindowsAzure\\Config* sulla macchina virtuale. Aprire il file XML appropriato e cercare prima **Microsoft.Azure.Diagnostics**, quindi il campo **xmlCfg**. I dati sono codificati in base64, pertanto sarà necessario [decodificarli](http://www.bing.com/search?q=base64+decoder) per visualizzare il codice XML che è stato caricato da Diagnostica. <p>
-107|La directory delle risorse passata all'agente di monitoraggio non è valida. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se l'agente di monitoraggio viene richiamato manualmente, in modo non corretto, sulla macchina virtuale. </p>
-108 |Impossibile convertire il file di configurazione di Diagnostica nel file di configurazione dell'agente di monitoraggio. <p><p> Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido.
-110|Errore di configurazione generale di Diagnostica. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido.
-111|Impossibile avviare l'agente di monitoraggio. <p> <p> Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.
-112|Errore generale:


## I dati di diagnostica non vengono registrati in Archiviazione di Azure
Diagnostica di Azure archivia tutti i dati in Archiviazione di Azure.

La causa più comune della mancanza di dati degli eventi è la definizione non corretta delle informazioni sull'account di archiviazione.

Soluzione: correggere il file di configurazione della diagnostica e reinstallare la diagnostica. Se il problema persiste dopo la reinstallazione dell'estensione di diagnostica, potrebbe essere necessario eseguire un ulteriore debug esaminando gli eventuali errori dell'agente di monitoraggio. I dati degli eventi, prima di essere caricati nell'account di archiviazione, vengono memorizzati in LocalResourceDirectory.

Per il ruolo dei servizi cloud LocalResourceDirectory è:

	C:\Resources\Directory<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Per le macchine virtuali LocalResourceDirectory è:

	C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Se nella cartella LocalResourceDirectory non sono presenti file, l'agente di monitoraggio non si avvia. Questo problema in genere è causato da un file di configurazione non valido, evento che dovrebbe essere segnalato in CommandExecution.log

Se l'agente di monitoraggio sta raccogliendo correttamente i dati degli eventi, sarà presente un file con estensione tsf per ogni evento definito nel file di configurazione. L'agente di monitoraggio registra gli errori nel file MaEventTable.tsf. Per esaminare il contenuto di questo file è possibile usare l'applicazione tabel2csv per convertire il file con estensione tsf in un file con estensione csv:

In un ruolo dei servizi cloud:

	%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%SystemDrive%* in un ruolo dei servizi cloud è in genere D:

In una macchina virtuale:

	C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

I comandi precedenti generano il file di log *maeventtable.csv* che è possibile aprire per esaminare i messaggi di errore.


## Tabelle dei dati di diagnostica non trovate
Le tabelle nell'archiviazione di Azure contenenti i dati di diagnostica vengono denominate usando il codice seguente:

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Di seguito è fornito un esempio:

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

Verranno generate quattro tabelle:

Evento|Nome tabella
---|---
provider=”prov1” &lt;Event id=”1” /&gt;|WADEvent+MD5(“prov1”)+”1”
provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt;|WADdest1
provider=”prov1” &lt;DefaultEvents /&gt;|WADDefault+MD5(“prov1”)
provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt;|WADdest2

<!---HONumber=AcomDC_0302_2016-->