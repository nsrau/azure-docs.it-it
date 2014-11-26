<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

## Installazione di Azure SDK 2.4 per Visual Studio "14" CTP

Per installare Azure SDK 2.4 per .NET con Visual Studio "14" CTP, seguire questi passaggi. Con questa procedura vengono installati gli SDK, gli strumenti di base e quelli avanzati per lo sviluppo in Azure con Visual Studio "14" CTP. La procedura non deve essere eseguita con nessuna delle altre versioni di Visual Studio.

**Nota**: Azure SDK 2.4 non è compatibile con Visual Studio "14" CTP1.

Per installare Azure SDK 2.4 per .NET, seguire questi passaggi:

1.  Installare la versione più recente di [Visual Studio "14" CTP][Visual Studio "14" CTP].

2.  Installare tutti i componenti di Azure SDK tramite i collegamenti nell'elenco seguente, nell'ordine indicato. Scegliere la versione x86 o x64 di ciascuno dei seguenti componenti.

    -   Strumenti di creazione di Azure: [WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] o [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi].
    -   Emulatore di calcolo di Azure: [WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] o [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe].
    -   Librerie client di Azure: [WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] o [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi].
    -   Emulatore di archiviazione: [WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]. Se si riceve un avviso relativo ai database SQL locali, installare SQL Server LocalDB 11.0 da [qui][qui] per x86 o da [qui][1] per x64.
    -   Strumenti di Azure per Visual Studio: [WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe].

    </p>

## Problemi noti

1.  Se si installa Visual Studio "14" CTP2 su un computer in cui è installato Visual Studio 2013, non è possibile avviare i servizi mobili in Visual Studio "14" CTP2. Per risolvere il problema, aggiungere un riferimento ai seguenti assembly nel progetto di servizio mobile:

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  Il debug remoto per i siti Web e Servizi mobili di Azure non funziona in Visual Studio "14" CTP2.

## Note sulla versione

Leggere le [note sulla versione][note sulla versione] per Azure SDK 2.4.

  [Visual Studio "14" CTP]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [qui]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [note sulla versione]: http://go.microsoft.com/fwlink/?LinkId=507517
