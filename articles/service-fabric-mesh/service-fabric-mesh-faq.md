---
title: Domande frequenti su Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni sulle domande frequenti e sulle risposte relative a Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: edd30dc8799ae9e5410ebc862574d632d09b9483
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168674"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Domande frequenti su Service Fabric Mesh

Azure Service Fabric Mesh è un servizio completamente gestito che consente agli sviluppatori di distribuire applicazioni di microservizi senza dover gestire macchine virtuali, archiviazione o connettività di rete. Questo articolo include le risposte alle domande frequenti relative a questo servizio.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Come segnalare un problema o porre una domanda?

Per inviare domande, ottenere risposte dagli esperti Microsoft e segnalare problemi, usare il [repository GitHub service-fabric-mesh-preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Quote e costi

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Quanto costa partecipare all'anteprima?

Non sono attualmente previsti addebiti per la distribuzione di applicazioni o contenitori nell'anteprima mesh. Per abilitare la fatturazione, controllare la disponibilità di aggiornamenti in. Tuttavia, si consiglia di eliminare le risorse distribuite senza lasciarle in esecuzione, a meno che non vengano testate attivamente.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>È previsto un limite di quota per il numero di core e la RAM?

Sì. Per ogni sottoscrizione sono previste le quote seguenti:

- Numero di applicazioni: 5
- Numero di core per applicazione: 12
- Quantità totale di RAM per applicazione: 48 GB
- Numero di endpoint di ingresso e rete: 5
- Numero di volumi di Azure che è possibile collegare: 10
- Numero di repliche dei servizi: 3
- Il contenitore maggiore che è possibile distribuire è limitato a 4 core e 16 GB di RAM.
- È possibile allocare core parziali ai contenitori con incrementi di 0,5 core fino a un massimo di 6 core.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Per quanto tempo è possibile lasciare l'applicazione distribuita?

Al momento la durata di un'applicazione è stata limitata a due giorni. Lo scopo è di massimizzare l'utilizzo dei core disponibili allocati per l'anteprima. È quindi consentito eseguire solo una determinata distribuzione in modo continuativo per 48 ore. Dopo questo periodo la distribuzione viene arrestata.

In questo caso, è possibile convalidare l'arresto da parte del sistema eseguendo il comando `az mesh app show` nell'interfaccia della riga di comando di Azure e verificare se restituisce `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Esempio: 

```cli
~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Per eliminare il gruppo di risorse, usare il comando `az group delete <nameOfResourceGroup>`.

## <a name="deployments"></a>Deployments

### <a name="what-container-images-are-supported"></a>Quali immagini del contenitore sono supportate?

Se le attività di sviluppo vengono eseguite in un computer con Windows Fall Creators Update (versione 1709), è possibile usare solo le immagini Docker di Windows versione 1709.

Se le attività di sviluppo vengono eseguite in un computer Windows 10 con aggiornamento di aprile 2018 (versione 1803), è possibile usare le immagini Docker di Windows versione 1709 o di Windows versione 1803.

Per distribuire i servizi, possono essere usate le immagini del sistema operativo contenitore seguenti:

- Windows: windowsservercore e nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Nessuna limitazione nota

> [!NOTE]
> Gli strumenti di Visual Studio per mesh non supportano ancora la distribuzione nei contenitori di Windows Server 2019 e 1809.

### <a name="what-types-of-applications-can-i-deploy"></a>Quali tipi di applicazioni è possibile distribuire? 

È possibile distribuire tutto ciò che viene eseguito nei contenitori che rientrano nelle limitazioni inserite in una risorsa dell'applicazione (vedere sopra per altre informazioni sulle quote). Se si rileva che si sta usando la rete mesh per eseguire carichi di lavoro non validi o si abusa del sistema (ad esempio, mining), si riserva il diritto di terminare le distribuzioni e di bloccare l'esecuzione della sottoscrizione nel servizio. Rivolgersi a Microsoft per eventuali domande sull'esecuzione di un carico di lavoro specifico. 

## <a name="developer-experience-issues"></a>Problemi relativi all'esperienza di sviluppatore

### <a name="dns-resolution-from-a-container-doesnt-work"></a>La risoluzione DNS da un contenitore non funziona

Le query DNS in uscita da un contenitore al servizio DNS di Service Fabric DNS potrebbero non riuscire in determinate circostanze. Il problema è in fase di analisi. Per attenuare il problema:

- Usare Windows Fall Creators Update (versione 1709) o versione successiva come immagine del contenitore di base.
- Se il nome del servizio da solo non funziona, provare a specificare il nome completo: ServiceName.ApplicationName.
- Nel file Docker per il servizio aggiungere `EXPOSE <port>`, dove "port" indica la porta a cui si espone il servizio. Esempio:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>Il DNS non funziona nello stesso modo nei cluster di sviluppo di Service Fabric e in Mesh

Potrebbe essere necessario fare riferimento ai servizi in modo diverso nel cluster di sviluppo locale rispetto ad Azure Service Fabric Mesh.

Nel cluster di sviluppo locale usare `{serviceName}.{applicationName}`. In Azure Service Fabric Mesh usare `{servicename}`. 

Azure Service Fabric Mesh attualmente non supporta la risoluzione DNS tra le applicazioni.

Per altri problemi noti relativi al DNS durante l'esecuzione di un cluster di sviluppo di Service Fabric in Windows 10, vedere: [Eseguire il debug di contenitori Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) e [Problemi DNS noti](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Rete

Il protocollo di rete NAT ServiceFabric può scomparire durante l'esecuzione dell'app nel computer locale. Per determinare se ciò è accaduto, eseguire questo comando al prompt dei comandi:

`docker network ls` e verificare se `servicefabric_nat` è elencato.  Se non è elencato, eseguire questo comando: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Il problema verrà risolto anche se l'app è già distribuita localmente in uno stato non integro.

### <a name="issues-running-multiple-apps"></a>Problemi durante l'esecuzione di più app

È possibile che sia necessario stabilire la disponibilità e i limiti della CPU tra tutte le applicazioni. Per attenuare il problema:
- Creare un cluster di cinque nodi.
- Ridurre l'uso della CPU nei servizi nell'app distribuita. Nel file service.yaml del servizio modificare `cpu: 1.0` in `cpu: 0.5`

Più applicazioni non possono essere distribuite in un cluster con un solo nodo. Per attenuare il problema:
- Usare un cluster con cinque nodi durante la distribuzione di più app in un cluster locale.
- Rimuovere le app attualmente non in fase di test.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>Gli strumenti di Visual Studio hanno un supporto limitato per i contenitori di Windows

Gli strumenti di Visual Studio supportano solo la distribuzione di contenitori di Windows con una versione del sistema operativo di base di Windows Server 1709 e 1803 oggi. 

## <a name="feature-gaps-and-other-known-issues"></a>Lacune di funzionalità e altri problemi noti

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Dopo la distribuzione dell'applicazione, la risorsa di rete associata non dispone di un indirizzo IP

Esiste un problema noto per cui l'indirizzo IP non è immediatamente disponibile. Controllare lo stato della risorsa di rete dopo qualche minuto per visualizzare l'indirizzo IP associato.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>L'applicazione non riesce ad accedere alla risorsa di rete/volume corretta

Per essere in grado di accedere alla risorsa associata, nel modello applicativo usare l'ID di risorsa completo per le reti e i volumi. Di seguito viene indicato un esempio della guida introduttiva:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Quando si aumenta il numero di istanze, tutti i contenitori risultano interessati, inclusi quelli in esecuzione

Si tratta di un bug di cui è in corso l'implementazione della correzione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Service Fabric Mesh, vedere la [panoramica](service-fabric-mesh-overview.md).
