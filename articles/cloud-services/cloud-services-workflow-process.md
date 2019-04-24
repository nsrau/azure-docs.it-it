---
title: Flusso di lavoro di architettura di Azure della macchina virtuale Windows | Microsoft Docs
description: Questo articolo fornisce una panoramica dei processi del flusso di lavoro quando si distribuisce un servizio.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480765"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Flusso di lavoro di architettura di macchina virtuale classica di Azure 
Questo articolo offre una panoramica dei processi del flusso di lavoro che si verificano quando si distribuisce o si aggiorna una risorsa di Azure, ad esempio una macchina virtuale. 

> [!NOTE]
>Azure offre due modelli di distribuzione diversi per creare e usare le risorse: Resource Manager e classica. Questo articolo illustra l'uso del modello di distribuzione classica.

Il diagramma seguente illustra l'architettura delle risorse di Azure.

![Flusso di lavoro di Azure](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Nozioni di base del flusso di lavoro
   
**R**. RDFE / FFE è il percorso di comunicazione da parte dell'utente all'infrastruttura. RDFE (RedDog Front End) è l'API esposta pubblicamente che è il front-end nel portale di gestione e l'API di gestione dei servizi, ad esempio Visual Studio, MMC di Azure e così via.  Tutte le richieste da parte dell'utente passano attraverso RDFE. FFE (Fabric Front-End) è il livello che converte le richieste provenienti da RDFE nei comandi di fabric. Tutte le richieste da RDFE passano attraverso il FFE per raggiungere i controller di infrastruttura.

**B**. Il controller di infrastruttura è responsabile della gestione e monitoraggio di tutte le risorse nel data center. Comunica con gli agenti host di infrastruttura nell'infrastruttura l'invio di informazioni del sistema operativo, ad esempio la versione del sistema operativo Guest, pacchetto del servizio, configurazione del servizio e lo stato del servizio.

**C**. L'agente Host si trova in OSsystem l'Host ed è responsabile della configurazione del sistema operativo Guest e comunicazione con l'agente Guest (WindowsAzureGuestAgent) per aggiornare il ruolo verso un stato dell'obiettivo desiderato ed eseguire l'heartbeat verifica con l'agente Guest. Se l'agente Host non riceve risposta di heartbeat per 10 minuti, dell'agente Host viene riavviato il sistema operativo Guest.

**C2**. È responsabile dell'installazione, configurazione e l'aggiornamento WindowsAzureGuestAgent.exe WaAppAgent.

**D**.  WindowsAzureGuestAgent è responsabile per le operazioni seguenti:

1. Configurazione del sistema operativo Guest, tra cui firewall, gli ACL, LocalStorage risorse, pacchetto del servizio e configurazione e certificati. Configurando il SID per l'account utente che verrà eseguito il ruolo.
2. La comunicazione sullo stato del ruolo all'infrastruttura.
3. Avvio WaHostBootstrapper e monitoraggio per verificare che il ruolo è nello stato dell'obiettivo.

**E**. WaHostBootstrapper è responsabile per:

1. La lettura della configurazione di ruoli e avvio di tutte le attività appropriate e processi per configurare ed eseguire il ruolo.
2. Monitoraggio di tutti i relativi processi figlio.
3. Genera l'evento StatusCheck nel processo host del ruolo.

**F**. IISConfigurator viene eseguito se il ruolo è configurato come ruolo web IIS completo (non verrà eseguito per i ruoli HWC 1.2 SDK). È responsabile per:

1. Avvio dei servizi IIS standard
2. Configurazione del modulo di riscrittura nella configurazione web
3. Configurare il pool di applicazioni per il ruolo configurato nel modello del servizio
4. Impostare la registrazione di IIS in modo che punti alla cartella DiagnosticStore LocalStorage
5. Configurazione delle autorizzazioni e ACL
6. Il sito Web si trova in % roleroot%:\sitesroot\0 e i punti di pool di applicazioni in questo percorso per l'esecuzione di IIS. 

**G**. Le attività di avvio sono definite dal modello di ruolo e avviate da WaHostBootstrapper. Le attività di avvio possono essere configurate per l'esecuzione in background in modo asincrono e il programma di avvio automatico host verrà avviare l'attività di avvio e quindi continuare con altre attività di avvio. Le attività di avvio possono essere configurate anche per l'esecuzione in modalità semplice (impostazione predefinita) in cui il programma di avvio automatico host attenderà per l'attività di avvio completare l'esecuzione e restituire un codice di uscita riuscita (0) prima di procedere all'attività di avvio successivo.

**H**. Queste attività fanno parte del SDK e sono definite come plug-nella definizione del ruolo del servizio (csdef). Se viene espansa in attività di avvio, il **DiagnosticsAgent** e **RemoteAccessAgent** siano univoci perché ogni definiscono due attività di avvio, una normale e quella che ha un **/blockStartup** parametro. L'attività di avvio normale è definito come un'attività di avvio in Background in modo che possa essere eseguita in background mentre è in esecuzione il ruolo stesso. Il **/blockStartup** attività di avvio è definito come un'attività di avvio semplice, in modo che WaHostBootstrapper attende che verrà chiusa prima di continuare. Il **/blockStartup** attività attende il completamento l'inizializzazione dell'attività normali e quindi viene chiusa e consentire il programma di avvio automatico host continuare. Questa operazione viene eseguita in modo che la diagnostica e l'accesso RDP possono essere configurate prima dell'inizio dei processi di ruolo (questa operazione viene eseguita tramite l'attività /blockStartup). Ciò consente anche di diagnostica e l'accesso RDP per continuare l'esecuzione dopo che il programma di avvio automatico host ha completato le attività di avvio (questa operazione viene eseguita tramite l'attività normale).

**I**. WaWorkerHost è il processo host standard per i ruoli di lavoro normali. Questo processo host ospita DLL e codice di punto di ingresso, ad esempio OnStart e l'esecuzione del ruolo.

**J**. WaWebHost è il processo host standard per i ruoli web se sono configurati per usare il SDK 1.2 compatibile Hostable Webcore (HWC). I ruoli è possono abilitare la modalità di HWC rimuovendo l'elemento dalla definizione del servizio (csdef). In questa modalità, codice del servizio e le DLL eseguiti dal processo WaWebHost. Non viene usato IIS (w3wp) e non esistono Nessun pool di applicazioni configurate in Gestione IIS perché IIS è ospitato in WaWebHost.exe.

**K**. WaIISHost è il processo host per il codice di punto di ingresso ruolo per i ruoli web che usano IIS completo. Questo processo carica la DLL prima che si trova che usa il **RoleEntryPoint** classe ed esegue il codice da questa classe (OnStart, Run, OnStop). Eventuali **RoleEnvironment** (ad esempio StatusCheck e Changed) che vengono creati nella classe RoleEntryPoint vengono generati in questo processo.

**L**. W3wp è il processo di lavoro IIS standard che viene usato se il ruolo è configurato per usare IIS completo. Esegue il pool di applicazioni configurata in modo da IISConfigurator. Generazione degli eventi RoleEnvironment (ad esempio StatusCheck e Changed) che vengono creati di seguito in questo processo. Si noti che se si effettua la sottoscrizione agli eventi in entrambi i processi, eventi RoleEnvironment verranno generato in entrambe le posizioni (WaIISHost e w3wp.exe).

## <a name="workflow-processes"></a>Processi del flusso di lavoro

1. Un utente effettua una richiesta, ad esempio il caricamento di file con estensione cspkg e cscfg, indicare una risorsa per arrestare o apportare una modifica della configurazione, e così via. Questa operazione può essere eseguita tramite il portale di Azure o uno strumento che usi l'API gestione dei servizi, ad esempio la funzionalità di pubblicazione di Visual Studio. Questa richiesta viene inviata a RDFE per eseguire tutti i relativi alla sottoscrizione di lavoro e quindi comunicare la richiesta di FFE. Il resto dei passaggi del flusso di lavoro sono per distribuire un nuovo pacchetto e avviarlo.
2. FFE rileva che il pool di computer corretti (basato su input del cliente questo tipo, come gruppo di affinità o posizione geografica più input dall'infrastruttura, ad esempio disponibilità del computer) e comunica con il controller di infrastruttura master in tale pool di computer.
3. Il controller di infrastruttura consente di trovare un host che disponga di core CPU (o rotazioni fino a un nuovo host). Il pacchetto del servizio e la configurazione viene copiato nell'host e il controller di infrastruttura comunica con l'agente host nell'host del sistema operativo per distribuire il pacchetto (configurare DIP, porte, sistema operativo guest e così via).
4. L'agente host viene avviato il sistema operativo Guest e comunica con l'agente guest (WindowsAzureGuestAgent). L'host invia gli heartbeat nella macchina guest per assicurarsi che il ruolo sta lavorando verso lo stato obiettivo.
5. WindowsAzureGuestAgent configura il guest OS (firewall, gli ACL, LocalStorage e così via), copia un nuovo file di configurazione XML c:\Config. e quindi avvia il processo WaHostBootstrapper.
6. Per i ruoli web IIS completo, WaHostBootstrapper avvia IISConfigurator e indica di eliminare qualsiasi pool di applicazioni esistenti per il ruolo web da IIS.
7. WaHostBootstrapper legge il **avvio** attività da E:\RoleModel.xml e inizia l'esecuzione di attività di avvio. WaHostBootstrapper attende fino a quando tutte le attività di avvio semplici hanno terminato e ha restituito un messaggio "operazione riuscita".
8. Per i ruoli web IIS completo, WaHostBootstrapper indica IISConfigurator per configurare il pool di applicazioni IIS e fa riferimento il sito `E:\Sitesroot\<index>`, dove `<index>` è un indice in base 0 nel numero di <Sites> elementi definiti per il servizio.
9. WaHostBootstrapper avvierà il processo host a seconda del tipo di ruolo:
    1. **Ruolo di lavoro**: WaWorkerHost.exe è stato avviato. WaHostBootstrapper esegue il metodo OnStart (). Dopo aver restituito, WaHostBootstrapper inizia a eseguire il metodo Run () e quindi contemporaneamente contrassegna il ruolo come pronto e inserito nella rotazione del bilanciamento del carico (se sono definiti InputEndpoints). WaHostBootsrapper quindi entra in un ciclo di verifica dello stato del ruolo.
    1. **Ruolo Web SDK 1.2 HWC**: WaWebHost è stato avviato. WaHostBootstrapper esegue il metodo OnStart (). Dopo aver restituito, WaHostBootstrapper inizia a eseguire il metodo Run () e contemporaneamente contrassegna quindi il ruolo come pronto e inserito nella rotazione del bilanciamento del carico. WaWebHost emette una richiesta di riscaldamento (/do.rd_runtime_init GET). Tutte le richieste web vengono inviate a WaWebHost.exe. WaHostBootsrapper quindi entra in un ciclo di verifica dello stato del ruolo.
    1. **Ruolo Web IIS completo**: aIISHost viene avviato. WaHostBootstrapper esegue il metodo OnStart (). Dopo aver restituito, viene avviato eseguire il metodo Run () e quindi contrassegna contemporaneamente il ruolo come pronto e inserito nella rotazione del bilanciamento del carico. WaHostBootsrapper quindi entra in un ciclo di verifica dello stato del ruolo.
10. Le richieste web in ingresso a un'edizione completa di IIS web trigger ruolo IIS per avviare il processo W3WP e rispondere alla richiesta, lo stesso come verrebbe mostrata in un ambiente di IIS in locale.

## <a name="log-file-locations"></a>Percorsi dei File di log

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Questo log contiene modifiche al servizio tra cui viene avviato, viene arrestata e le nuove configurazioni. Se il servizio non viene modificato, sarà possibile vedere spazi vuoti di tempo in questo file di log.
- C:\Logs\WaAppAgent.Log.  
Questo log contiene gli aggiornamenti di stato e le notifiche di heartbeat e viene aggiornato ogni 2 e 3 secondi.  Questo log contiene una visualizzazione cronologica dello stato dell'istanza e indicherà quando l'istanza non è nello stato pronto.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**Log IIS**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Log eventi di Windows**

D:\Windows\System32\Winevt\Logs
 



