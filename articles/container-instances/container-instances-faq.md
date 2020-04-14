---
title: Domande frequenti
description: Risposte per le domande frequenti relative al servizio Istanze del contenitore di AzureAnswers for frequently asked questions related to the Azure Container Instances service
author: dkkapur
ms.topic: article
ms.date: 04/10/2020
ms.openlocfilehash: 8730dcb24af61730d7f93ea37a53cf87435eb9f9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261619"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Domande frequenti sulle istanze del contenitore di AzureFrequently asked questions about Azure Container Instances

Questo articolo risponde alle domande frequenti sulle istanze del contenitore di Azure.This article addresses frequently asked questions about Azure Container Instances.

## <a name="deployment"></a>Distribuzione

### <a name="how-large-can-my-container-image-be"></a>Quanto può essere grande l'immagine del contenitore?

La dimensione massima per un'immagine del contenitore distribuibile nelle istanze del contenitore di Azure è 15 GB. Potrebbe essere possibile distribuire immagini più grandi a seconda della disponibilità esatta al momento della distribuzione, ma ciò non è garantito.

Le dimensioni dell'immagine del contenitore influiscono sul tempo necessario per la distribuzione, pertanto in genere si desidera mantenere le immagini del contenitore il più piccole possibile.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Come posso velocizzare la distribuzione del mio contenitore?

Poiché uno dei principali determinanti dei tempi di distribuzione è la dimensione dell'immagine, cercare modi per ridurre le dimensioni. Rimuovere i livelli non necessari o ridurre le dimensioni dei livelli nell'immagine (selezionando un'immagine del sistema operativo di base più chiara). Ad esempio, se si eseguono contenitori Linux, è consigliabile usare Alpine come immagine di base anziché un server Ubuntu completo. Analogamente, per i contenitori Windows, usare un'immagine di base Nano Server, se possibile. 

È inoltre necessario controllare l'elenco delle immagini pre-memorizzate nella cache nelle immagini del contenitore di Azure, disponibile tramite l'API [Elenca immagini memorizzate nella cache.](/rest/api/container-instances/listcachedimages) Potrebbe essere possibile disattivare un livello di immagine per una delle immagini pre-memorizzate nella cache. 

Per [ulteriori informazioni](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) sulla riduzione del tempo di avvio del contenitore, vedere .

### <a name="what-windows-base-os-images-are-supported"></a>Quali immagini del sistema operativo di base di Windows sono supportate?

#### <a name="windows-server-2016-base-images"></a>Immagini di base di Windows Server 2016Windows Server 2016 base images

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `10.0.14393.x`: ,`sac2016`
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2016`Core : ,`10.0.14393.x`

> [!NOTE]
> Le immagini Windows basate sulla versione 1709 o 1803 del canale semestrale non sono supportate.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Immagini di base di Windows Server 2019 e client (anteprima)Windows Server 2019 and client base images (preview)

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `1809` `10.0.17763.914` : , o versioni precedenti
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019`Core `1809` `10.0.17763.914` : , , o versioni precedenti
* [Windows](https://hub.docker.com/_/microsoft-windows) `1809`: `10.0.17763.914` , o versioni precedenti

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Quale livello di immagine .NET o .NET Core è necessario usare nel contenitore? 

Usa l'immagine più piccola che soddisfa le tue esigenze. Per Linux, è possibile usare un'immagine .NET Core di runtime, supportata dopo il rilascio di .NET Core 2.1.For Linux, you could use a *runtime-alpine* .NET Core image, which has been supported since the release of .NET Core 2.1. Per Windows, se si utilizza .NET Framework completo, è necessario utilizzare un'immagine Windows Server Core (immagine di sola runtime, ad esempio *4.7.2-windowsservercore-ltsc2016*). Le immagini di sola runtime sono più piccole ma non supportano carichi di lavoro che richiedono .NET SDK.

## <a name="availability-and-quotas"></a>Disponibilità e quote

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Quanti core e memoria è necessario allocare per i contenitori o il gruppo di contenitori?

Questo dipende davvero dal carico di lavoro. Inizia le prestazioni piccole e testa per vedere come funzionano i tuoi contenitori. [Monitorare l'utilizzo](container-instances-monitor.md)delle risorse di CPU e memoria , quindi aggiungere core o memoria in base al tipo di processi distribuiti nel contenitore. 

Assicurarsi inoltre di controllare la [disponibilità delle risorse](container-instances-region-availability.md#availability---general) per l'area in cui si esegue la distribuzione per i limiti superiori dei core CPU e della memoria disponibile per ogni gruppo di contenitori. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Su quale infrastruttura sottostante funziona ACI?

Le istanze dei contenitori di Azure mirano a essere un servizio di container su richiesta senza server, pertanto vogliamo che tu sia incentrato sullo sviluppo dei contenitori e non preoccuparti dell'infrastruttura. Per coloro che sono curiosi o che desiderano eseguire confronti sulle prestazioni, ACI viene eseguito su set di macchine virtuali di Azure di vari SKU, principalmente dalla serie F e D. Ci aspettiamo che questo cambi in futuro mentre continuiamo a sviluppare e ottimizzare il servizio. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Voglio distribuire migliaia di core su ACI - posso aumentare la mia quota?
 
Sì (a volte). Vedere l'articolo [quote e limiti](container-instances-quotas.md) per le quote correnti e i limiti che possono essere aumentati su richiesta.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>È possibile eseguire la distribuzione con più di 4 core e 16 GB di RAM?

Per il momento no. Attualmente, questi sono i valori massimi per un gruppo di contenitori. Contattare il supporto di Azure con richieste o requisiti specifici. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando sarà l'ACI in una regione specifica?

La disponibilità dell'area corrente è pubblicata [qui](container-instances-region-availability.md#availability---general). Se si ha un requisito per un'area specifica, contattare il supporto di Azure.If you have a requirement for a specific region, contact Azure Support.

## <a name="features-and-scenarios"></a>Funzionalità e scenari

### <a name="how-do-i-scale-a-container-group"></a>Come si ridimensiona un gruppo di contenitori?

Attualmente, il ridimensionamento non è disponibile per contenitori o gruppi di contenitori. Se è necessario eseguire più istanze, usare l'API per automatizzare e creare altre richieste per la creazione di gruppi di contenitori nel servizio. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quali funzionalità sono disponibili per le istanze in esecuzione in una rete virtuale personalizzata?

È possibile distribuire gruppi di [contenitori in](container-instances-vnet.md) una rete virtuale di Azure di propria scelta e delegare indirizzi IP privati ai gruppi di contenitori per instradare il traffico all'interno della rete virtuale tra le risorse di Azure.You can deploy container groups in an Azure virtual network of your choice, and delegate private IPs to the container groups to route within the VNet across your Azure resources. La distribuzione di un gruppo di contenitori in una rete virtuale è attualmente disponibile per i carichi di lavoro di produzione in un sottoinsieme di aree di Azure.Deployment of a container group into a virtual network is currently available for production workloads in a subset of Azure regions.

## <a name="pricing"></a>Prezzi

### <a name="when-does-the-meter-start-running"></a>Quando inizia a funzionare il contatore?

La durata del gruppo di contenitori viene calcolata dal momento in cui iniziamo a estrarre l'immagine del primo contenitore (per una nuova distribuzione) o il gruppo di contenitori viene riavviato (se già distribuito), fino all'arresto del gruppo di contenitori. Vedere i dettagli in Prezzi per [le istanze di contenitori](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Smetto di essere addebitato quando i miei container vengono fermati?

I contatori smettono di funzionare una volta arrestato l'intero gruppo di contenitori. Finché un contenitore nel gruppo di contenitori è in esecuzione, consentiamo le risorse nel caso in cui si desideri avviare nuovamente i contenitori. 

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni](container-instances-overview.md) sulle istanze del contenitore di Azure.Learn more about Azure Container Instances.
* Risolvere i problemi comuni nelle istanze del contenitore di [Azure.Troubleshoot common issues](container-instances-troubleshooting.md) in Azure Container Instances.