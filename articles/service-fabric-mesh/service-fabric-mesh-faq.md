---
title: Domande frequenti su Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni sulle domande frequenti e sulle risposte relative a Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d0ae7fbb22f6d98662f83968158182d447a75394
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501968"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Domande frequenti su Service Fabric Mesh
Azure Service Fabric Mesh è un servizio completamente gestito che consente agli sviluppatori di distribuire applicazioni di microservizi senza dover gestire macchine virtuali, archiviazione o connettività di rete. Questo articolo include le risposte alle domande frequenti relative a questo servizio.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Come segnalare un problema o porre una domanda?

Per inviare domande, ottenere risposte dagli esperti Microsoft e segnalare problemi, usare il [repository GitHub service-fabric-mesh-preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Quote e costi

**Quanto costa partecipare all'anteprima?**

Non sono previsti addebiti per la distribuzione di applicazioni o contenitori nella versione di anteprima di Mesh. Gli utenti sono tuttavia invitati a eliminare le risorse distribuite e a non lasciarle in esecuzione, a meno che non ne eseguano attivamente il test.

**È previsto un limite di quota per il numero di core e la RAM?**

Sì, per ogni sottoscrizione sono previste le quote seguenti:

- Numero di applicazioni: 5 
- Numero di core per applicazione: 12 
- Quantità totale di RAM per applicazione: 48 GB 
- Numero di endpoint di ingresso e rete: 5  
- Numero di volumi di Azure che è possibile collegare: 10 
- Numero di repliche dei servizi: 3 
- Il contenitore più grande che è possibile distribuire è limitato a 4 core e 16 GB di RAM.
- È possibile allocare core parziali ai contenitori in base a incrementi di 0,5 core fino a un massimo di 6 core.

**È possibile lasciare un'applicazione in esecuzione durante le ore notturne?**

Sì, è possibile. Gli utenti sono tuttavia invitati a eliminare le risorse distribuite e a non lasciarle in esecuzione, a meno che non ne eseguano attivamente il test. Questi criteri possono cambiare in futuro ed è possibile che le eventuali risorse usate in modo improprio vengano eliminate.

## <a name="supported-container-os-images"></a>Immagini di sistema operativo contenitore supportate
Durante la distribuzione dei servizi possono essere usate le immagini di sistema operativo contenitore seguenti.

- Windows: windowsservercore e nanoserver
    - Windows Server 2016
    - Windows Server versione 1709
- Linux
    - Nessuna limitazione nota

## <a name="features-gaps-and-known-issues"></a>Lacune di funzionalità e problemi noti

**Dopo la distribuzione dell'applicazione, la risorsa di rete associata non sembra avere un indirizzo IP**

Esiste un problema noto per cui attualmente l'indirizzo IP viene visualizzato con un ritardo. Controllare lo stato della risorsa di rete dopo qualche minuto per visualizzare l'indirizzo IP associato.

**L'applicazione non riesce ad accedere alla risorsa di rete/volume corretta**

Nel modello applicativo è necessario usare l'ID di risorsa completo per le reti e i volumi per essere in grado di accedere alla risorsa associata. Ecco come appare nell'esempio della guida introduttiva:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**Nel modello applicativo corrente non risulta incluso il supporto per la crittografia dei segreti**

Sì, la crittografia dei segreti non è supportata nell'anteprima privata corrente. 

**Il DNS non funziona allo stesso modo nel cluster di sviluppo di Service Fabric e in Mesh**

Esiste un problema noto per cui può essere necessario fare riferimento ai servizi in modo diverso nel cluster di sviluppo locale e in Azure Mesh. Nel cluster di sviluppo locale usare {serviceName}.{applicationName}. In Azure Service Fabric Mesh usare {servicename}. Azure Mesh attualmente non supporta la risoluzione DNS tra le applicazioni.

Per altri problemi noti relativi al DNS durante l'esecuzione di un cluster di sviluppo di Service Fabric in Windows 10, vedere [Eseguire il debug dei contenitori Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers).

**Quando si usa il modulo dell'interfaccia della riga di comando viene visualizzato un errore per segnalare che non è possibile importare il nome 'sdk_no_wait'**

Se si usa una versione dell'interfaccia della riga di comando precedente la 2.0.30, è possibile che venga visualizzato questo errore:

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**Quando si installa il pacchetto dell'estensione dell'interfaccia della riga di comando viene visualizzato un errore di mancata corrispondenza del nome della distribuzione**

Questo non significa che l'estensione non è stata installata. Dovrebbe comunque essere possibile usare i comandi dell'interfaccia della riga di comando senza problemi.

**Quando si aumenta il numero di istanze, tutti i contenitori risultano interessati, inclusi quelli in esecuzione**

Questo è un bug che dovrebbe essere risolto nel successivo aggiornamento del runtime.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Service Fabric Mesh, vedere la [panoramica](service-fabric-mesh-overview.md).
