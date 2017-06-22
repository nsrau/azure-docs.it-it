---
title: Risoluzione dei problemi relativi a Diagnostica di Azure | Documentazione Microsoft
description: Risoluzione dei problemi per l&quot;uso di Diagnostica di Azure nelle macchine virtuali di Azure, in Service Fabric o in Servizi cloud.
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 81f9a5a8e5ce8389c12a40eb02dd554fc140e009
ms.contentlocale: it-it
ms.lasthandoff: 03/22/2017


---
# <a name="azure-diagnostics-troubleshooting"></a>Risoluzione dei problemi di Diagnostica di Azure
Informazioni sulla risoluzione dei problemi relativi all'uso di Diagnostica di Azure. Per altre informazioni su Diagnostica di Azure, vedere [Panoramica di Diagnostica di Azure](azure-diagnostics.md).

## <a name="azure-diagnostics-is-not-starting"></a>Mancato avvio di Diagnostica Azure
La diagnostica è costituita da due componenti: un plug-in agente guest e l'agente di monitoraggio. Per informazioni sui motivi del mancato avvio della diagnostica, è possibile controllare i file di log **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log**.  

In un ruolo di servizi cloud, i file di log del plug-in dell'agente guest si trovano in:

```
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\
```

In una macchina virtuale di Azure, i file di log del plug-in dell'agente guest si trovano in:

```
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.7.4.0\Logs\
```

L'ultima riga del file di log contiene il codice di uscita.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```

Il plug-in restituisce i seguenti codici di uscita:

| Codice di uscita | Descrizione |
| --- | --- |
| 0 |Completamento della procedura. |
| -1 |Errore generico. |
| -2 |Impossibile caricare il file rcf.<p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale. |
| -3 |Impossibile caricare il file di configurazione di Diagnostica.<p><p>Soluzione: questo errore si verifica quando un file di configurazione non ha superato la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema. |
| -4 |La directory delle risorse locali è già usata da un'altra istanza dell'agente di monitoraggio di Diagnostica.<p><p>Soluzione: specificare un valore diverso per **LocalResourceDirectory**. |
| -6 |L'utilità di avvio del plug-in dell'agente guest ha tentato di avviare Diagnostica con una riga di comando non valida.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale. |
| -10 |Il plug-in di Diagnostica ha generato un'eccezione non gestita. |
| -11 |L'agente guest non è stato in grado di creare il processo responsabile dell'avvio e del monitoraggio dell'agente di monitoraggio.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.<p> |
| -101 |Argomenti non validi durante la chiamata del plug-in di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale. |
| -102 |Il processo del plug-in non è in grado di inizializzarsi.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi. |
| -103 |Il processo del plug-in non è in grado di inizializzarsi. In particolare, non è in grado di creare l'oggetto logger.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi. |
| -104 |Impossibile caricare il file rcf fornito dall'agente guest.<p><p>Questo errore interno dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale. |
| -105 |Il plug-in di Diagnostica non è in grado di aprire il file di configurazione di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente, in modo non corretto, sulla macchina virtuale. |
| -106 |Impossibile leggere il file di configurazione di Diagnostica.<p><p>Soluzione: questo errore si verifica quando un file di configurazione non ha superato la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema. Per la macchina virtuale Azure, la configurazione fornita all'estensione Diagnostica si trova nella cartella *%SystemDrive%\WindowsAzure\Config* sulla VM. Aprire il file XML appropriato e cercare prima **Microsoft.Azure.Diagnostics**, quindi il campo **xmlCfg** o **WadCfg**. Se il campo WadCfg è presente, significa che la configurazione è in JSON. Se il campo xmlCfg è presente, significa che la configurazione è in formato XML e contiene la codifica Base64. Per visualizzare il codice XML caricato da Diagnostica è necessario decodificare il file. Per il ruolo Servizi Cloud, è possibile trovare la configurazione fornita all'estensione di Diagnostica in C:\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.7.4.0\config.txt sulla macchina virtuale. I dati sono codificati in base&64;, pertanto sarà necessario [decodificarli](http://www.bing.com/search?q=base64+decoder) per visualizzare il codice XML che è stato caricato da Diagnostica.<p> |
| -107 |La directory delle risorse passata all'agente di monitoraggio non è valida.<p><p>Questo errore interno dovrebbe verificarsi solo se l'agente di monitoraggio viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p> |
| -108 |Impossibile convertire il file di configurazione di Diagnostica nel file di configurazione dell'agente di monitoraggio.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido. |
| -110 |Errore di configurazione generale di Diagnostica.<p><p>Questo errore interno dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido. |
| -111 |Impossibile avviare l'agente di monitoraggio.<p><p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti. |
| -112 |Errore generale: |

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>I dati di diagnostica non vengono registrati in Archiviazione di Azure
Diagnostica di Azure archivia tutti i dati in Archiviazione di Azure.

La causa più comune della mancanza di dati degli eventi è la definizione non corretta delle informazioni sull'account di archiviazione.

Soluzione: correggere il file di configurazione della diagnostica e reinstallare Diagnostica.
Se il problema persiste dopo la reinstallazione dell'estensione Diagnostica, potrebbe essere necessario eseguire un nuovo debug esaminando gli eventuali errori dell'agente di monitoraggio. I dati degli eventi, prima di essere caricati nell'account di archiviazione, vengono memorizzati in LocalResourceDirectory.
Questo percorso di directory varia.

Per i ruoli del servizio cloud:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Per le macchine virtuali:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Se nella cartella LocalResourceDirectory non sono presenti file, l'agente di monitoraggio non si avvia. Questo problema in genere è causato da un file di configurazione non valido, evento che dovrebbe essere segnalato in CommandExecution.log

Se l'agente di monitoraggio sta raccogliendo correttamente i dati degli eventi, sarà presente un file con estensione tsf per ogni evento definito nel file di configurazione. L'agente di monitoraggio registra gli errori nel file MaEventTable.tsf. Per esaminare il contenuto di questo file è possibile usare l'applicazione tabel2csv per convertire il file con estensione tsf in un file di valori con estensione csv:

In un ruolo dei servizi cloud:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%SystemDrive%* in un ruolo dei servizi cloud è in genere D:

In una macchina virtuale:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

I comandi precedenti generano il file di log *maeventtable.csv* che è possibile aprire per esaminare i messaggi di errore.    

## <a name="diagnostics-data-tables-not-found"></a>Tabelle dei dati di diagnostica non trovate
Le tabelle nell'archiviazione di Azure contenenti i dati di diagnostica vengono denominate usando il codice seguente:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Di seguito è fornito un esempio:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```

Vengono generate 4 tabelle:

| Evento | Nome tabella |
| --- | --- |
| provider="prov1" &lt;Event id=v1" /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

