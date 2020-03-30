---
title: Flusso di lavoro dell'architettura delle macchine virtuali di Windows Azure - Documenti Microsoft
description: In questo articolo viene fornita una panoramica dei processi del flusso di lavoro quando si distribuisce un servizio.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162156"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Flusso di lavoro dell'architettura classica delle macchine virtuali di Windows Azure 
Questo articolo offre una panoramica dei processi del flusso di lavoro che si verificano quando si distribuisce o si aggiorna una risorsa di Azure, ad esempio una macchina virtuale. 

> [!NOTE]
>Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Resource Manager e distribuzione classica. Questo articolo illustra l'uso del modello di distribuzione classica.

Il diagramma seguente presenta l'architettura delle risorse di Azure.The following diagram presents the architecture of Azure resources.

![Flusso di lavoro di AzureAzure workflow](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Nozioni di base sul flusso di lavoro
   
**A**. RDFE / FFE è il percorso di comunicazione dall'utente all'infrastruttura. RDFE (RedDog Front End) è l'API esposta pubblicamente che funge da front-end del portale di gestione e dell'API di gestione dei servizi, ad esempio Visual Studio, MMC di Azure e così via.  Tutte le richieste dell'utente passano attraverso RDFE. FFE (Fabric Front End) è il livello che traduce le richieste da RDFE in comandi di infrastruttura. Tutte le richieste da RDFE passano attraverso il FFE per raggiungere i controller di tessuto.

**B**. Il controller dell'infrastruttura è responsabile della manutenzione e del monitoraggio di tutte le risorse del data center. Comunica con gli agenti host dell'infrastruttura nel sistema operativo dell'infrastruttura che inviano informazioni quali la versione del sistema operativo guest, il pacchetto del servizio, la configurazione del servizio e lo stato del servizio.

**C**. L'agente host si trova nel sistema operativo host ed è responsabile della configurazione del sistema operativo guest e della comunicazione con l'agente guest (WindowsAzureGuestAgent) per aggiornare il ruolo verso uno stato obiettivo previsto ed eseguire controlli heartbeat con l'agente guest. Se l'agente host non riceve risposta heartbeat per 10 minuti, l'agente host riavvia il sistema operativo guest.

**C2**. WaAppAgent è responsabile dell'installazione, della configurazione e dell'aggiornamento di WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent è responsabile di quanto segue:

1. Configurazione del sistema operativo guest, inclusi firewall, ACL, risorse LocalStorage, pacchetto e configurazione del servizio e certificati.
2. Impostazione del SID per l'account utente con cui verrà eseguito il ruolo.
3. Comunicazione dello stato del ruolo nell'infrastruttura.
4. Avvio WaHostBootstrapper e il monitoraggio per assicurarsi che il ruolo è in stato di obiettivo.

**E**. . WaHostBootstrapper è responsabile di:

1. Lettura della configurazione del ruolo e avvio di tutte le attività e i processi appropriati per configurare ed eseguire il ruolo.
2. Monitoraggio di tutti i processi figlio.
3. Generazione dell'evento StatusCheck nel processo host del ruolo.

**F**. IISConfigurator viene eseguito se il ruolo è configurato come ruolo Web IIS completo (non verrà eseguito per i ruoli HWC SDK 1.2). È responsabile di:

1. Avvio dei servizi IIS standard
2. Configurazione del modulo di riscrittura nella configurazione Web
3. Impostazione dell'AppPool per il ruolo configurato nel modello di servizio
4. Impostazione della registrazione IIS in modo che punti alla cartella DiagnosticStore LocalStorage
5. Configurazione di autorizzazioni e ACL
6. Il sito Web si trova in %roleroot%: , ssitesroot , 0 e AppPool punta a questo percorso per eseguire IIS. 

**G**. Le attività di avvio sono definite dal modello di ruolo e avviate da WaHostBootstrapper.Startup tasks are defined by the role model and started by WaHostBootstrapper. Le attività di avvio possono essere configurate per l'esecuzione in background in modo asincrono e il programma di avvio automatico host avvierà l'attività di avvio e continuerà ad eseguire altre attività di avvio. Le attività di avvio possono anche essere configurate per l'esecuzione in modalità semplice (predefinita) in cui il programma di avvio automatico host attenderà il completamento dell'esecuzione dell'attività di avvio e restituisce un codice di uscita positivo (0) prima di passare all'attività di avvio successiva.

**H**. Queste attività fanno parte dell'SDK e sono definite come plug-in nella definizione del servizio del ruolo (con estensione csdef). Quando vengono espanse in attività di avvio, **DiagnosticsAgent** e **RemoteAccessAgent** sono uniche in quanto definiscono ognuna due attività di avvio, una normale e una con un parametro **/blockStartup.** L'attività di avvio normale è definita come un'attività di avvio in background in modo che possa essere eseguita in background mentre il ruolo stesso è in esecuzione. L'attività di avvio **/blockStartup** è definita come un'attività di avvio semplice in modo che WaHostBootstrapper attenda che venga chiusa prima di continuare. L'attività **/blockStartup** attende il completamento dell'inizializzazione dell'attività regolare, quindi viene chiusa e consente al programma di avvio automatico dell'host di continuare. Questa operazione viene eseguita in modo che la diagnostica e l'accesso RDP possono essere configurati prima dell'avvio dei processi del ruolo (questa operazione viene eseguita tramite l'attività /blockStartup). Ciò consente inoltre alla diagnostica e all'accesso RDP di continuare l'esecuzione dopo che il programma di avvio automatico dell'host ha completato le attività di avvio (questa operazione viene eseguita tramite l'attività Normal).

**I**. WaWorkerHost è il processo host standard per i ruoli di lavoro normali. Questo processo host ospita tutte le DLL del ruolo e il codice del punto di ingresso, ad esempio OnStart ed Esegui.This host process hosts all the role's DLs and entry point code, such as OnStart and Run.

**J**. WaWebHost è il processo host standard per i ruoli Web se sono configurati per utilizzare l'HWC (Hostable Web Core) compatibile con SDK 1.2. I ruoli possono abilitare la modalità HWC rimuovendo l'elemento dalla definizione del servizio (con estensione csdef). In questa modalità, tutto il codice del servizio e le DLL vengono eseguiti dal processo WaWebHost. IIS (w3wp) non viene utilizzato e non sono presenti AppPool configurati in Gestione IIS perché IIS è ospitato all'interno di WaWebHost.exe.

**K**. WaIISHost è il processo host per il codice del punto di ingresso del ruolo per i ruoli Web che utilizzano IIS completo. Questo processo carica la prima DLL trovata che utilizza la classe **RoleEntryPoint** ed esegue il codice da questa classe (OnStart, Run, OnStop). Tutti gli eventi **RoleEnvironment** (ad esempio StatusCheck e Changed) creati nella classe RoleEntryPoint vengono generati in questo processo.

**L**. W3WP è il processo di lavoro IIS standard utilizzato se il ruolo è configurato per utilizzare IIS completo. Verrà eseguito l'AppPool configurato da IISConfigurator. Tutti gli eventi RoleEnvironment (ad esempio StatusCheck e Changed) creati qui vengono generati in questo processo. Si noti che gli eventi RoleEnvironment verranno attivati in entrambe le posizioni (WaIISHost e w3wp.exe) se si sottoscrivono eventi in entrambi i processi.

## <a name="workflow-processes"></a>Processi del flusso di lavoro

1. Un utente effettua una richiesta, ad esempio il caricamento di file ".cspkg" e ".cscfg", indicando a una risorsa di interrompere o apportare una modifica alla configurazione e così via. Questa operazione può essere eseguita tramite il portale di Azure o uno strumento che usa l'API di gestione dei servizi, ad esempio la funzionalità di pubblicazione di Visual Studio.This can be done through the Azure portal or a tool that uses the Service Management API, such as the Visual Studio Publish feature. Questa richiesta passa a RDFE per eseguire tutte le operazioni correlate alla sottoscrizione e quindi comunicare la richiesta a FFE. Il resto di questi passaggi del flusso di lavoro consiste nel distribuire un nuovo pacchetto e avviarlo.
2. FFE trova il pool di macchine corretto (in base all'input del cliente, ad esempio il gruppo di affinità o la posizione geografica più l'input dall'infrastruttura, ad esempio la disponibilità della macchina) e comunica con il controller dell'infrastruttura principale in tale pool di macchine.
3. Il controller di infrastruttura trova un host con core CPU disponibili (o crea un nuovo host). Il pacchetto del servizio e la configurazione vengono copiati nell'host e il controller di infrastruttura comunica con l'agente host nel sistema operativo host per distribuire il pacchetto (configurare DIP, porte, sistema operativo guest e così via).
4. L'agente host avvia il sistema operativo guest e comunica con l'agente guest (WindowsAzureGuestAgent). L'host invia heartbeat al guest per assicurarsi che il ruolo funzioni per lo stato dell'obiettivo.
5. WindowsAzureGuestAgent configura il sistema operativo guest (firewall, ACL, LocalStorage e così via), copia un nuovo file di configurazione XML in c:Config e quindi avvia il processo WaHostBootstrapper.
6. Per i ruoli Web IIS completi, WaHostBootstrapper avvia IISConfigurator e indica di eliminare eventuali AppPool esistenti per il ruolo Web da IIS.
7. WaHostBootstrapper legge le attività di **avvio** da E: , RoleModel.xml e inizia l'esecuzione di attività di avvio. WaHostBootstrapper attende fino a quando tutte le attività di avvio semplice sono state completate e ha restituito un messaggio di "successo".
8. Per i ruoli Web IIS completi, WaHostBootstrapper indica a IISConfigurator `E:\Sitesroot\<index>`di `<index>` configurare l'AppPool IIS e punta il sito a , dove è un indice in base 0 al numero di `<Sites>` elementi definiti per il servizio.
9. WaHostBootstrapper avvierà il processo host a seconda del tipo di ruolo:
    1. **Ruolo di lavoro:** WaWorkerHost.exe avviato. WaHostBootstrapper esegue il metodo OnStart(). Dopo la restituzione, WaHostBootstrapper inizia a eseguire il metodo Run(), quindi contrassegna contemporaneamente il ruolo come Ready e lo inserisce nella rotazione del bilanciamento del carico (se InputEndpoints sono definiti). WaHostBootsrapper quindi entra in un ciclo di controllo dello stato del ruolo.
    1. **Ruolo Web HWC SDK 1.2**: WaWebHost avviato. WaHostBootstrapper esegue il metodo OnStart(). Dopo la restituzione, WaHostBootstrapper inizia a eseguire il metodo Run(), quindi contrassegna contemporaneamente il ruolo come Ready e lo inserisce nella rotazione del bilanciamento del carico. WaWebHost invia una richiesta di riscaldamento (GET /do.rd_runtime_init). Tutte le richieste Web vengono inviate a WaWebHost.exe. WaHostBootsrapper quindi entra in un ciclo di controllo dello stato del ruolo.
    1. **Ruolo Web IIS completo:** aIISHost viene avviato. WaHostBootstrapper esegue il metodo OnStart(). Dopo la restituzione, inizia a eseguire il metodo Run(), quindi contrassegna contemporaneamente il ruolo come Ready e lo inserisce nella rotazione del bilanciamento del carico. WaHostBootsrapper quindi entra in un ciclo di controllo dello stato del ruolo.
10. Le richieste Web in ingresso a un ruolo Web IIS completo attiva IIS per avviare il processo W3WP e servire la richiesta, come in un ambiente IIS locale.

## <a name="log-file-locations"></a>Percorsi dei file di registro

**WindowsAzureGuestAgent**

- Registro di Analisi.  
Questo registro contiene modifiche al servizio, inclusi gli avvii, gli arresti e le nuove configurazioni. Se il servizio non cambia, ci si può aspettare di vedere grandi lacune di tempo in questo file di registro.
- C: . . . . . . . . . . . . . . . . . . . . . . . . . . . .  
Questo registro contiene gli aggiornamenti di stato e le notifiche heartbeat e viene aggiornato ogni 2-3 secondi.  Questo log contiene una visualizzazione cronologica dello stato dell'istanza e indica quando l'istanza non era nello stato Pronto.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Registri IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Registri eventi di Windows**

`D:\Windows\System32\Winevt\Logs`
 



