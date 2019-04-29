---
title: Azure Application Insights OpenCensus distributed traccia utilità di inoltro locali (anteprima) | Documentazione Microsoft
description: Inoltrare intervalli e tracce distribuite OpenCensus da linguaggi come Python e Go in Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: a7efe663a75fa29a31e7157c5eab24c2973a3758
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699321"
---
# <a name="local-forwarder-preview"></a>Utilità di inoltro locali (anteprima)

Il servizio d'inoltro locale è un agente che raccoglie dati di telemetria di Application Insights o [OpenCensus](https://opencensus.io/) da un'ampia gamma di SDK e li instrada ad Application Insights. Può essere eseguito sia in Windows sia in Linux. Può essere eseguito anche in macOS, ma al momento non è ufficialmente supportato.

## <a name="running-local-forwarder"></a>Esecuzione del servizio d'inoltro locale

Il servizio d'inoltro locale è un [progetto open source su GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Esistono diversi modi per eseguire il servizio d'inoltro locale su più piattaforme.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Servizio Windows

Il modo più semplice per eseguire un servizio d'inoltro locale in Windows è quello di installarlo come servizio Windows. Questa versione include un file eseguibile del servizio Windows (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) che può essere facilmente registrato con il sistema operativo.

> [!NOTE]
> Il servizio di inoltro locale richiede almeno .NET Framework 4.7. In caso contrario, il servizio verrà installato ma non verrà avviato. Per accedere alla versione più recente di .NET Framework, **[visitare la pagina di download di .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)**.

1. Scaricare il file LF.WindowsServiceHost.zip dalla [pagina della versione del servizio d'inoltro locale](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) in GitHub.

    ![Screenshot della pagina di download della versione del server d'inoltro locale](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. Per una maggiore semplicità di dimostrazione, in questo esempio il file ZIP verrà estratto nel percorso `C:\LF-WindowsServiceHost`.

    Per registrare il servizio e configurarlo in modo che venga avviato contestualmente all'avvio del sistema, eseguire il comando seguente dalla riga di comando come amministratore:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Si riceverà la risposta seguente:
    
    `[SC] CreateService SUCCESS`
    
    Per esaminare il nuovo servizio tramite il tipo di interfaccia utente grafica dei servizi ``services.msc``
        
     ![Screenshot del servizio di inoltro locale](./media/opencensus-local-forwarder/002-services.png)

3. **Fare clic con il pulsante destro del mouse** sul nuovo servizio d'inoltro locale e selezionare **Avvia**. Il servizio entrerà in stato di esecuzione.

4. Per impostazione predefinita, il servizio viene creato senza azioni di recupero. Per configurare risposte automatiche a un errore del servizio, **fare clic con il pulsante destro del mouse** e selezionare **Proprietà** > **Recupero**.

    Se invece si preferisce impostare opzioni di recupero automatico a livello di codice in caso di errori, è possibile usare:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. Nello stesso percorso del file ``Microsoft.LocalForwarder.WindowsServiceHost.exe``, che in questo esempio è ``C:\LF-WindowsServiceHost``, è presente un file denominato ``LocalForwarder.config``. Si tratta di un file basato su XML che consente di modificare la configurazione del servizio d'inoltro locale e di specificare la chiave di strumentazione della risorsa di Application Insights a cui si vuole distribuire i dati delle tracce distribuite. 

    Dopo aver modificato il file ``LocalForwarder.config`` per aggiungere la chiave di strumentazione, assicurarsi di riavviare il **servizio d'inoltro locale** per rendere effettive le modifiche.
    
6. Per verificare che siano state applicate le impostazioni desiderate e che il servizio d'inoltro locale sia in ascolto dei dati di traccia come previsto, consultare il file ``LocalForwarder.log``. Nella parte inferiore del file verranno visualizzati risultati simili all'immagine seguente:

    ![Schermata del file LocalForwarder.log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Applicazione console

In determinati casi d'uso può essere utile eseguire un server d'inoltro locale come applicazione console. Questa versione include le seguenti versioni eseguibili dell'host della console:
* un file binario di .NET Core dipendente dal framework */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Per eseguire questo file binario è necessario che sia installato un runtime di .NET Core; per informazioni dettagliate, fare riferimento a questa [pagina](https://www.microsoft.com/net/download/dotnet-core/2.1) di download.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* un set completo di file binari di .NET Core per piattaforme x86 e x64. Per questi file non è necessario che venga eseguito un runtime di .NET Core. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Come per Windows, questa versione include le seguenti versioni eseguibili dell'host della console:
* un file binario di .NET Core dipendente dal framework */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Per eseguire questo file binario è necessario che sia installato un runtime di .NET Core; per informazioni dettagliate, fare riferimento a questa [pagina](https://www.microsoft.com/net/download/dotnet-core/2.1) di download.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* un set completo di file binari di .NET Core per Linux a 64 bit. Per questi file non è necessario che venga eseguito un runtime di .NET Core. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Molti utenti Linux sceglieranno di eseguire il servizio d'inoltro locale come daemon. I sistemi Linux includono un'ampia gamma di soluzioni per la gestione dei servizi, tra cui Upstart, sysv e systemd. Indipendentemente dalla versione installata, è possibile usarla per eseguire il servizio d'inoltro locale nel modo più appropriato al proprio scenario.

In questo esempio si creerà un servizio daemon usando systemd. Si userà inoltre la versione dipendente dal framework, ma sarebbe possibile usare anche la versione completa.

* creare il file di servizio seguente denominato *localforwarder.service* e salvarlo in */lib/systemd/system*.
Questo esempio presuppone che il nome utente sia SAMPLE_USER e che i file binari dipendenti da framework del servizio d'inoltro locale siano stati copiati (da */ConsoleHost/publish*) in */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Eseguire il comando seguente per indicare a systemd di avviare il servizio d'inoltro locale ad ogni avvio di sistema

```
systemctl enable localforwarder
```

* Eseguire il comando seguente per indicare a systemd di avviare il servizio d'inoltro locale immediatamente

```
systemctl start localforwarder
```

* Monitorare il servizio controllando i file **.log* nella directory /home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Il servizio d'inoltro locale può essere usato anche in macOS, ma al momento non è ufficialmente supportato.

### <a name="self-hosting"></a>Self-hosting
Il servizio d'inoltro locale può essere distribuito anche come pacchetto NuGet standard di .NET, in modo da poter essere ospitato all'interno di un'applicazione .NET.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Configurazione del servizio d'inoltro locale

* Quando si esegue uno degli host del servizio d'inoltro locale (host della console o host del servizio Windows), si noterà che accanto al file binario è presente il file **LocalForwarder.config**.
* Quando si esegue il self-hosting del pacchetto NuGet del servizio d'inoltro locale, nel codice è necessario specificare la configurazione nello stesso formato (vedere la sezione sul self-hosting). Per informazioni sulla sintassi di configurazione, consultare il file [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) nel repository GitHub. 

> [!NOTE]
> È importare prestare attenzione alla versione in uso, poiché è possibile che la configurazione cambi in base alla versione.

## <a name="monitoring-local-forwarder"></a>Monitoraggio del servizio d'inoltro locale

Le tracce vengono scritte nel file system accanto al file eseguibile che installa il servizio d'inoltro locale (eseguire la ricerca di file **log*). Per sostituire le configurazione predefinita con una personalizzata, è possibile posizionare un file denominato *NLog.config* accanto al file eseguibile. Per una descrizione del formato, vedere la [documentazione](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format).

Se non viene fornito alcun file di configurazione (impostazione predefinita), il servizio d'inoltro locale userà la configurazione predefinita, disponibile [qui](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Passaggi successivi

* [OpenCensus](https://opencensus.io/)
