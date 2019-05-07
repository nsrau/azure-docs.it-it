---
title: Istanze di contenitore Azure - domande frequenti
description: Risposte alle domande frequenti relative al servizio di istanze di contenitore di Azure
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 5657ac9f10c42c2201641c9af447b7ad2e6a3507
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079016"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Domande frequenti su istanze di contenitore di Azure

Questo articolo risponde alle domande frequenti sulle istanze di contenitore di Azure.

## <a name="deployment"></a>Distribuzione

### <a name="how-large-can-my-container-image-be"></a>Le dimensioni dell'immagine contenitore è possibile?

Le dimensioni massime per un'immagine del contenitore distribuibile in istanze di contenitore di Azure sono di 15 GB. Potrebbe essere in grado di distribuire le immagini più grandi a seconda della disponibilità esattamente nel momento in cui si distribuisce, ma non è garantita.

Le dimensioni dell'immagine del contenitore influisce sul tempo necessario per distribuire, quindi, in genere che si desidera mantenere al minimo le immagini del contenitore.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Come è possibile velocizzare la distribuzione del contenitore?

Poiché tra i principali fattori determinanti dei tempi di distribuzione è la dimensione dell'immagine, cercare i modi per ridurre le dimensioni. Rimuovere i livelli non necessarie o ridurre le dimensioni dei livelli dell'immagine (selezionando un'immagine del sistema operativo di base più chiara). Se si eseguono i contenitori Linux, si consideri ad esempio usando Alpine come immagine di base anziché una versione completa di Ubuntu Server. Analogamente, per i contenitori di Windows, usare un'immagine di base di Nano Server se possibile. 

Controllare inoltre l'elenco di immagini pre-cache in immagini contenitore di Azure, disponibile tramite il [elencare le immagini memorizzate nella cache](/rest/api/container-instances/listcachedimages) API. È possibile attivare un livello di immagine per una delle immagini pre-cache. 

Vedi altri [indicazioni dettagliate](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) di ridurre i tempi di avvio di contenitore.

### <a name="what-windows-base-os-images-are-supported"></a>Le immagini del sistema operativo di base di Windows sono supportati?

#### <a name="windows-server-2016-base-images"></a>Immagini di base di Windows Server 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Non sono supportate immagini di Windows basate su canale semestrale versione 1709 o 1803.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Client e Windows Server 2019 immagini di base (anteprima)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Quale livello di immagine .NET Core o .NET usare nel contenitore? 

Usare l'immagine più piccola che soddisfa i requisiti. Per Linux, è possibile usare una *runtime alpine* immagine .NET Core, che è stata supportata dal rilascio di .NET Core 2.1. Per Windows, se si usa la versione completa di .NET Framework, quindi è necessario usare un'immagine di Windows Server Core (immagine solo runtime, ad esempio *4.7.2-windowsservercore-ltsc2016*). Immagini solo di runtime sono più piccoli, ma non supportano i carichi di lavoro che richiedono .NET SDK.

## <a name="availability-and-quotas"></a>Disponibilità e le quote

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Il numero di core e memoria devo allocare per i contenitori o il gruppo di contenitori?

Ciò dipende in realtà il carico di lavoro. Iniziare in piccolo e testare le prestazioni per vedere come i contenitori. [Monitorare l'utilizzo delle risorse di CPU e memoria](container-instances-monitor.md)e quindi aggiungere core o memoria in base al tipo di processi che si distribuisce nel contenitore. 

Assicurarsi anche di controllare la [la disponibilità delle risorse](container-instances-region-availability.md#availability---general) per l'area in cui si distribuisce per i limiti superiori per core CPU e memoria disponibile per ogni gruppo di contenitori. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Il tipo di infrastruttura sottostante viene eseguita ACI?

Istanze di contenitore di Azure ha lo scopo di essere un servizio contenitori on demand senza server, pertanto è opportuno concentrare lo sviluppo dei contenitori e senza doversi preoccupare dell'infrastruttura. Per coloro che sono curioso di scoprire o necessità per eseguire confronti sulle prestazioni, ACI in esecuzione nel set di macchine virtuali di Azure dei vari SKU, principalmente dalla serie D e F. Si prevede che questa opzione per cambiare in futuro per continuare a sviluppare e ottimizzare il servizio. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Vuole distribuire migliaia di core su ACI - è possibile ottenere l'aumento della quota?
 
Sì (a volte). Vedere le [quote e limiti](container-instances-quotas.md) articolo per le quote correnti e limiti che possono essere incrementati di richiesta.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>È possibile distribuire con più di 4 core e 16 GB di RAM?

Per il momento no. Attualmente, questi sono i valori massimi per un gruppo di contenitori. Contattare il supporto tecnico di Azure con le richieste o requisiti specifici. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando potranno essere ACI in un'area specifica?

Aree di disponibilità corrente viene pubblicata [qui](container-instances-region-availability.md#availability---general), e informazioni aggiornate sono disponibile tramite il [funzionalità dell'elenco](/rest/api/container-instances/listcapabilities) API. Se si dispone di un requisito per un'area specifica, contattare il supporto di Azure.

## <a name="features-and-scenarios"></a>Scenari e funzionalità

### <a name="how-do-i-scale-a-container-group"></a>Come si ridimensiona un gruppo di contenitori?

Attualmente, la scalabilità non è disponibile per i contenitori o gruppi di contenitori. Se è necessario eseguire più istanze, usare l'API per automatizzare e creare più richieste per la creazione del gruppo contenitore nel servizio. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quali funzionalità sono disponibili nelle istanze in esecuzione in una rete virtuale personalizzata?

È possibile distribuire gruppi di contenitori in una rete virtuale di Azure di propria scelta e delegare gli indirizzi IP privati per i gruppi di contenitori per instradare il traffico all'interno della rete virtuale tra le risorse di Azure. Distribuzione di un gruppo di contenitori in una rete virtuale è attualmente in anteprima e alcuni aspetti di questa funzionalità possono cambiare prima della disponibilità generale (GA). Visualizzare [limitazioni dell'anteprima](container-instances-vnet.md#preview-limitations) per informazioni aggiornate.

## <a name="pricing"></a>Prezzi

### <a name="when-does-the-meter-start-running"></a>Quando il contatore inizia in esecuzione?

Durata del gruppo di contenitori viene calcolata dal momento in cui si inizia a pull dell'immagine del contenitore prima (di una nuova distribuzione) o il gruppo di contenitori viene riavviato (se già stata distribuita), fino a quando non viene arrestato il gruppo di contenitori. Vedere i dettagli in [prezzi per le istanze di contenitore](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>È possibile interrompere l'addebito quando i contenitori sono arrestati?

I contatori di interrompere l'esecuzione di una volta arrestato il gruppo intero contenitore. Fino a quando un contenitore nel gruppo di contenitori è in esecuzione, abbiamo tenuto le risorse nel caso in cui si desidera avviare contenitori nuovamente. 

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni](container-instances-overview.md) sulle istanze di contenitore di Azure.
* [Risolvere i problemi comuni](container-instances-troubleshooting.md) in istanze di contenitore di Azure.