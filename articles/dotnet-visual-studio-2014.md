<properties 
	pageTitle="Installazione di Azure SDK 2.4 per Visual Studio 14 CTP2" 
	description="Installazione di Azure SDK 2.4 e Visual Studio 14 CTP2" 
	services="visual-studio-online" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="visual-studio-online" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Installazione di Azure SDK 2.4 per Visual Studio "14" CTP

Per installare Azure SDK 2.4 per .NET con Visual Studio "14" CTP, seguire questi passaggi. Con questa procedura vengono installati gli SDK, gli strumenti di base e quelli avanzati per lo sviluppo in Azure con Visual Studio "14" CTP. La procedura non deve essere eseguita con nessuna delle altre versioni di Visual Studio.

**Nota**: Azure SDK 2.4 non è compatibile con Visual Studio "14" CTP1.

Per installare Azure SDK 2.4 per .NET, seguire questi passaggi:

1. Installare la versione più recente di [Visual Studio "14" CTP](http://go.microsoft.com/fwlink/p/?LinkId=400776).

2. Installare tutti i componenti di Azure SDK tramite i collegamenti nell'elenco seguente, nell'ordine indicato. Scegliere la versione x86 o x64 di ciascuno dei seguenti componenti.

       <ul>
        <li>Strumenti di creazione di Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> o <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a>.</li>
       <li>Emulatore di calcolo di Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> o <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a>.</li>
       <li>Librerie client di Azure: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> o <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a>.</li>
       <li>Emulatore di archiviazione: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>.                            Se si riceve un avviso relativo ai database SQL locali, installare SQL Server LocalDB 11.0 da <a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">qui</a> per x86 o da <a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">qui</a> per x64.</li><li> Strumenti di Azure per Visual Studio: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>.</li></ul>

## Problemi noti

1. Se si installa Visual Studio "14" CTP2 in un computer in cui è installato Visual Studio 2013, non è possibile avviare i servizi mobili in Visual Studio "14" CTP2. Per risolvere il problema, aggiungere un   riferimento ai seguenti assembly nel progetto di servizio mobile:

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. Il debug remoto per i siti Web e Servizi mobili di Azure non funziona in Visual Studio "14" CTP2.

## Note sulla versione

Leggere le [note sulla versione](http://go.microsoft.com/fwlink/?LinkId=507517) per Azure SDK 2.4.

<!--HONumber=46--> 
