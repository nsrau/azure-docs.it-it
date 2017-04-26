---
title: Preparazione alla distribuzione del cluster autonomo di Azure Service Fabric | Documentazione Microsoft
description: Documentazione relativa alla preparazione dell&quot;ambiente e alla creazione della configurazione del cluster, da esaminare prima di distribuire un cluster progettato per gestire un carico di lavoro di produzione.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: maburlik;chackdan
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: f332193f9a53260173a1010b8bf9f08726bea427
ms.lasthandoff: 03/31/2017


---

<a id="preparemachines"></a>

## <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Pianificare e preparare la distribuzione del cluster autonomo di Service Fabric
I passaggi seguenti devono essere eseguiti prima di creare il cluster.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Passaggio 1: pianificazione dell’infrastruttura del cluster
Prima di creare un cluster di Service Fabric sui propri computer, è possibile stabilire da quali tipi di errore il cluster non deve essere compromesso. Ad esempio, sono necessarie linee di alimentazione o connessioni Internet separate per queste macchine? È necessario inoltre considerare la sicurezza fisica di tali macchine. Dove si trovano le macchine e chi ha bisogno di accedervi? Dopo aver preso queste decisioni, è possibile eseguire il mapping logico delle macchine ai vari domini di errore (vedere il passaggio 4). La pianificazione dell'infrastruttura per i cluster di produzione è più complicata rispetto ai cluster di test.

### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Passaggio 2: preparare le macchine con i prerequisiti
Prerequisiti per ogni macchina da aggiungere al cluster:

* Consigliati minimo 16 GB di RAM.
* Consigliati minimo 40 GB di spazio disponibile su disco.
* Consigliata una CPU 4 core o superiore.
* Connessione a una o più reti protette per tutte le macchine.
* Windows Server 2012 R2 o Windows Server 2016. 
* [.NET Framework 4.5.1 o versione successiva](https://www.microsoft.com/download/details.aspx?id=40773), installazione completa.
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
* Il [servizio RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve essere eseguito in tutti i computer.

L'amministratore del cluster che distribuisce e configura il cluster deve disporre dei [privilegi di amministratore](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) in ogni computer. Non è possibile installare Service Fabric in un controller di dominio.

### <a name="step-3-determine-the-initial-cluster-size"></a>Passaggio 3: determinare le dimensioni iniziali del cluster
In ogni nodo di un cluster di Service Fabric autonomo è distribuito il runtime di Service Fabric. Tutti i nodi sono membri del cluster. In una distribuzione di produzione tipica è presente un nodo per istanza (fisica o virtuale) del sistema operativo. La dimensione del cluster viene determinata in base alle esigenze aziendali. È tuttavia necessario avere una dimensione minima del cluster di tre nodi (computer o macchine virtuali).
A scopi di sviluppo è possibile configurare più di un nodo in una macchina specifica. In un ambiente di produzione, il Service Fabric supporta solo un nodo per ogni macchina virtuale o fisica.

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Passaggio 4: identificazione del numero di domini di errore e di aggiornamento
Un *dominio di errore* è un'unità fisica di errore ed è direttamente correlato all'infrastruttura fisica nei data center. È costituito da componenti hardware (computer, commutatori, rete e altro) che condividono un singolo punto di guasto. Sebbene non sia presente una mappatura 1:1 tra domini di errore e rack, ogni rack può essere considerato in senso lato un dominio di errore. Quando si esaminano i nodi nel cluster è consigliabile distribuire i nodi tra gli ultimi tre domini di errore.

Quando si specificano domini di errore nel file ClusterConfig.json, è possibile scegliere il nome di ogni dominio di errore. Il Service Fabric supporta i domini di errore gerarchici, in modo che possano rispecchiare la topologia infrastrutturale.  Di seguito sono riportati esempi di domini di errore validi:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

Un *dominio di aggiornamento* è un'unità logica di nodi. Durante gli aggiornamenti orchestrati di Service Fabric (di applicazione o cluster), tutti i nodi in un dominio di aggiornamento vengono disattivati per eseguire l'aggiornamento, mentre i nodi in altri domini di aggiornamento rimangono disponibili per gestire le richieste. Gli aggiornamenti del firmware sulle macchine non rispettano i domini di aggiornamento ed è pertanto necessario eseguirli su una macchina alla volta.

Il metodo più semplice per descrivere questi concetti è considerare i domini di errore come unità degli errori imprevisti e i domini di aggiornamento come unità della manutenzione pianificata.

Quando si specificano domini di aggiornamento nel file ClusterConfig.json è possibile scegliere il nome di ogni dominio di aggiornamento. Di seguito sono riportati esempi di nomi validi:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Per informazioni più dettagliate sui domini di aggiornamento e di errore, vedere [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>Passaggio 5: scaricare il pacchetto autonomo Service Fabric per Windows Server
[Collegamento per il download - Pacchetto autonomo di Service Fabric - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690); decomprimere il pacchetto in un computer di distribuzione non appartenente al cluster o in uno dei computer che faranno parte del cluster.

### <a name="step-6-modify-cluster-configuration"></a>Passaggio 6: modificare la configurazione del cluster
Per creare un cluster autonomo, è necessario creare un file ClusterConfig.json di configurazione del cluster autonomo che descrive la specifica del cluster. È possibile basare il file di configurazione sui modelli disponibili nel collegamento seguente. <br>
[Configurazioni di cluster autonomi](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Per informazioni dettagliate sulle sezioni di tale file, vedere [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md).

Aprire uno dei file ClusterConfig.json del pacchetto scaricato e modificare le impostazioni seguenti:
| **Impostazioni di configurazione** | **Descrizione** |
| --- | --- |
| **NodeTypes** |I tipi di nodo permettono di separare i nodi del cluster in diversi gruppi. Un cluster deve avere almeno un NodeType. Tutti i nodi in un gruppo possiedono le seguenti caratteristiche comuni:  <br> **Nome** : il nome del tipo di nodo. <br>**Porte di endpoint** : endpoint con nomi diversi (porte) associati a questo tipo di nodo. È possibile usare qualsiasi numero di porta desiderato, purché non entri in conflitto con qualsiasi altro nel manifesto e non sia già utilizzato da un'altra applicazione eseguita nel computer/VM. <br> **Proprietà di posizionamento**: descrivono proprietà per questo tipo di nodo usate come vincoli di posizionamento per i servizi di sistema o i servizi dell'utente. Queste proprietà sono coppie chiave-valore definite dall'utente che forniscono metadati aggiuntivi per un determinato nodo. Le proprietà del nodo possono includere ad esempio la presenza di un disco rigido o di una scheda grafica, il numero di spindle nel disco rigido, le memorie centrali e altre proprietà fisiche. <br> **Capacità** : le capacità del nodo definiscono il nome e la quantità di una particolare risorsa utilizzabile da parte di un determinato nodo. Ad esempio, un nodo può definire la propria capacità per una metrica denominata "MemoryInMb" con un valore predefinito di 2048 MB di memoria disponibile. Queste capacità vengono usate in fase di esecuzione per garantire che i servizi che richiedono una determinata quantità di risorse vengano inseriti nei nodi in cui tali risorse sono disponibili nelle quantità richieste.<br>**IsPrimary** : se sono definiti più NodeType, verificare che solo uno sia impostato come primario, con il valore *true*, ovvero quello in cui vengono eseguiti i servizi di sistema. Tutti gli altri tipi di nodo devono essere impostati sul valore *false* |
| **Nodi** |Questi sono i dettagli per ciascun nodo che farà parte del cluster (tipo di nodo, nome del nodo, indirizzo IP, dominio di errore e dominio di aggiornamento del nodo). I computer in cui si vuole creare il cluster devono essere elencati in questa sezione con il relativo indirizzo IP. <br> Se si usa lo stesso indirizzo IP per tutti i nodi, viene creato un cluster di una casella che può essere usato per scopi di test. Non usare cluster di una casella per la distribuzione dei carichi di lavoro di produzione. |

Dopo che tutte le impostazioni sono state configurate per l'ambiente nella configurazione del cluster, è possibile eseguire il test nell'ambiente del cluster (passaggio 7).

<a id="environmentsetup"></a>

### <a name="step-7-environment-setup"></a>Passaggio 7. Configurazione dell'ambiente

Quando un amministratore di cluster configura un cluster autonomo di Service Fabric, l'ambiente deve essere configurato con i criteri seguenti: <br>
1. L'utente che ha creato il cluster deve disporre dei privilegi di sicurezza a livello di amministratore per tutti i computer elencati come nodi nel file di configurazione del cluster.
2. Il computer da cui viene creato il cluster e ogni computer del nodo del cluster devono rispettare i requisiti seguenti:
* Service Fabric SDK non installato
* Runtime di Service Fabric non installato 
* Servizio Windows Firewall (mpssvc) abilitato
* Servizio Registro di sistema remoto (remoteregistry) abilitato
* Condivisione file (SMB) abilitata
* Porte aperte necessarie in base alle porte di configurazione del cluster
* Porte aperte necessarie per il servizio Registro di sistema remoto e Windows SMB: 135, 137, 138, 139 e 445
* Connettività di rete reciproca
3. Nessuna delle macchine ai nodi del cluster deve essere un controller di dominio.
4. Se il cluster da distribuire è protetto, convalidare che i prerequisiti di sicurezza necessari siano corretti e che siano stati configurati correttamente in base alla configurazione.
5. Se i computer del cluster non sono accessibili da Internet, impostare quanto segue nella configurazione del cluster:
* Disabilitare la telemetria: in *properties* impostare   *"enableTelemetry": false*
* Disabilitare le notifiche e il download automatici della versione di Service Fabric relativi alla condizione che la versione corrente del cluster sta per raggiungere il termine del supporto: in *properties* impostare   *"fabricClusterAutoupgradeEnabled": false*
* In alternativa, se l'accesso a Internet dalla rete è limitato ai domini consentiti, è necessario l'aggiornamento automatico dei domini seguenti: go.microsoft.com download.microsoft.com

6. Impostare le esclusioni antivirus di Service Fabric appropriate:

| **Directory escluse dall'antivirus** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (dalla configurazione del cluster) |
| FabricLogRoot (dalla configurazione del cluster) |

| **Processi esclusi dall'antivirus** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

### <a name="step-8-validate-environment-using-testconfiguration-script"></a>Passaggio 8. Convalidare l'ambiente con lo script TestConfiguration
Lo script TestConfiguration.ps1 è incluso nel pacchetto autonomo. Viene usato come Best Practices Analyzer per convalidare alcuni criteri indicati sopra e deve essere usato come verifica di integrità per convalidare se un cluster può essere distribuito in un determinato ambiente. In caso di errore vedere l'elenco in [Configurazione dell'ambiente](service-fabric-cluster-standalone-deployment-preparation.md) per la risoluzione dei problemi. 

Questo script può essere eseguito su qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Questo modulo di configurazione di test attualmente non convalida la configurazione della sicurezza; questa operazione deve essere eseguita in modo indipendente.  

> [!NOTE]
> Apportiamo costantemente miglioramenti per ottimizzare il modulo; inviare eventuali suggerimenti tramite i [canali del supporto](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support) se si individua un caso errato o mancante non rilevato attualmente da TestConfiguration.   
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Creare un cluster autonomo in esecuzione su Windows Server](service-fabric-cluster-creation-for-windows-server.md)

