---
title: Panoramica dell'isolamento e della privacy della rete virtuale
titleSuffix: Azure Machine Learning
description: Usare una rete virtuale di Azure isolata con Azure Machine Learning per proteggere le risorse dell'area di lavoro e gli ambienti di calcolo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperfq1
ms.openlocfilehash: 7bc56f6296bf41933348fad9ea4aeb640b9afbf0
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776018"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Panoramica dell'isolamento e della privacy della rete virtuale

Questo articolo illustra come usare le reti virtuali (reti virtuali) per proteggere le comunicazioni di rete in Azure Machine Learning. Questo articolo usa uno scenario di esempio per illustrare come configurare una rete virtuale completa.

Questo articolo fa parte di una serie in cinque parti che illustra come proteggere un flusso di lavoro Azure Machine Learning. Si consiglia vivamente di leggere questo articolo introduttivo per conoscere prima i concetti. 

Ecco gli altri articoli di questa serie:

**1. Panoramica di VNet**  >  [2. Proteggere l'area di lavoro](how-to-secure-workspace-vnet.md)  >  [3. Proteggere l'ambiente di training](how-to-secure-training-vnet.md)  >  [4. Proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)  >  [5. Abilitare la funzionalità di studio](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con gli argomenti seguenti:
+ [Reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
+ [Rete IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
+ [Collegamento privato di Azure](how-to-configure-private-link.md)
+ [Gruppi di sicurezza di rete (NGS)](../virtual-network/security-overview.md)
+ [Firewall di rete](../firewall/overview.md)

## <a name="example-scenario"></a>Scenario di esempio

In questa sezione viene illustrato come configurare uno scenario di rete comune per proteggere Azure Machine Learning la comunicazione con indirizzi IP privati.

La tabella seguente confronta il modo in cui i servizi accedono a parti diverse di una rete Azure Machine Learning sia con VNet che senza VNet.

| Scenario | Area di lavoro | Risorse associate | Ambiente di calcolo di training | Inferenza dell'ambiente di calcolo |
|-|-|-|-|-|-|
|**Nessuna rete virtuale**| IP pubblico | IP pubblico | IP pubblico | IP pubblico |
|**Proteggere le risorse in una rete virtuale**| IP privato (endpoint privato) | IP pubblico (endpoint servizio) <br> **o** <br> IP privato (endpoint privato) | IP privato | IP privato  | 

* **Area di lavoro** : creare un endpoint privato dalla VNet per connettersi al collegamento privato nell'area di lavoro. L'endpoint privato connette l'area di lavoro alla VNET tramite diversi indirizzi IP privati.
* **Risorse associate** : usare endpoint del servizio o endpoint privati per connettersi alle risorse dell'area di lavoro, ad esempio archiviazione di azure, Azure Key Vault e servizi contenitore di Azure.
    * Gli **endpoint di servizio** forniscono l'identità della rete virtuale al servizio di Azure. Una volta abilitati gli endpoint di servizio nella rete virtuale, è possibile aggiungere una regola della rete virtuale per proteggere le risorse dei servizi di Azure nella rete virtuale. Gli endpoint di servizio utilizzano indirizzi IP pubblici.
    * Gli **endpoint privati** sono interfacce di rete che si connettono in modo sicuro a un servizio basato sul collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della VNet, in modo da portare il servizio in VNet.
* **Training** delle destinazioni di calcolo dell'accesso alle risorse di calcolo, come Azure Machine Learning istanza di calcolo e Azure Machine Learning cluster di calcolo in modo sicuro con indirizzi IP privati. 
* Inferenza dei cluster di calcolo dell'accesso di **calcolo** di Azure Kubernetes Services (AKS) con indirizzi IP privati.


Nelle cinque sezioni successive viene illustrato come proteggere lo scenario di rete descritto in precedenza. Per proteggere la rete, è necessario:

1. Proteggere l' [**area di lavoro e le risorse associate**](#secure-the-workspace-and-associated-resources).
1. Proteggere l' [**ambiente di training**](#secure-the-training-environment).
1. Proteggere l' [**ambiente di inferenza**](#secure-the-inferencing-environment).
1. Facoltativamente, [**abilitare la funzionalità di studio**](#optional-enable-studio-functionality).
1. Configurare [ **le impostazioni del firewall**](#configure-firewall-settings)

## <a name="secure-the-workspace-and-associated-resources"></a>Proteggere l'area di lavoro e le risorse associate

Usare la procedura seguente per proteggere l'area di lavoro e le risorse associate. Questi passaggi consentono ai servizi di comunicare nella rete virtuale.

1. Creare un' [area di lavoro privata abilitata](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) per il collegamento per abilitare la comunicazione tra VNet e l'area di lavoro.
1. Aggiungere Azure Key Vault alla rete virtuale con un [endpoint del servizio](../key-vault/general/overview-vnet-service-endpoints.md) o un [endpoint privato](../key-vault/general/private-link-service.md). Impostare Key Vault su ["Consenti ai servizi Microsoft attendibili di ignorare questo firewall"](how-to-secure-workspace-vnet.md#secure-azure-key-vault).
1. Aggiungere l'account di archiviazione di Azure alla rete virtuale con un [endpoint del servizio](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) o un [endpoint privato](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints).
1. [Configurare container Registry di Azure per l'uso di un endpoint privato](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr) e [abilitare la delega della subnet nelle istanze di contenitore di Azure](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

![Diagramma dell'architettura che mostra il modo in cui l'area di lavoro e le risorse associate comunicano tra di loro sugli endpoint di servizio o privati all'interno di un VNet](./media/how-to-network-security-overview/secure-workspace-resources.png)

Per istruzioni dettagliate su come eseguire questa procedura, vedere [proteggere un'area di lavoro Azure Machine Learning](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Limitazioni

La protezione dell'area di lavoro e delle risorse associate all'interno di una rete virtuale presenta le limitazioni seguenti:
- L'uso di un'area di lavoro Azure Machine Learning con collegamento privato non è disponibile nelle aree 21Vianet di Azure per enti pubblici o Azure Cina.
- Tutte le risorse devono trovarsi dietro la stessa VNet. Tuttavia, le subnet all'interno dello stesso VNet sono consentite.

## <a name="secure-the-training-environment"></a>Proteggere l'ambiente di training

In questa sezione viene illustrato come proteggere l'ambiente di training in Azure Machine Learning. Si apprenderà anche come Azure Machine Learning completa un processo di training per comprendere il funzionamento combinato delle configurazioni di rete.

Per proteggere l'ambiente di training, attenersi alla procedura seguente:

1. Creare un' [istanza di calcolo e un cluster di computer Azure Machine Learning nella rete virtuale](how-to-secure-training-vnet.md#compute-instance) per eseguire il processo di training.
1. [Consentire la comunicazione in ingresso da Azure batch servizio](how-to-secure-training-vnet.md#mlcports) in modo che il servizio batch possa inviare processi alle risorse di calcolo. 

![Diagramma dell'architettura che illustra come proteggere le istanze e i cluster di calcolo gestiti](./media/how-to-network-security-overview/secure-training-environment.png)

Per istruzioni dettagliate su come eseguire questa procedura, vedere [proteggere un ambiente di training](how-to-secure-training-vnet.md). 

### <a name="example-training-job-submission"></a>Esempio di invio del processo di training 

In questa sezione viene illustrato come Azure Machine Learning comunica in modo sicuro tra i servizi per inviare un processo di training. In questo modo viene illustrato il modo in cui tutte le configurazioni interagiscono per proteggere la comunicazione.

1. Il client carica gli script di training e i dati di training negli account di archiviazione protetti con un servizio o un endpoint privato.

1. Il client invia un processo di training all'area di lavoro di Azure Machine Learning tramite l'endpoint privato.

1. Azure Batch Services riceve il processo dall'area di lavoro e invia il processo di training all'ambiente di calcolo tramite il servizio di bilanciamento del carico pubblico di cui è stato effettuato il provisioning con la risorsa di calcolo. 

1. La risorsa di calcolo riceve il processo e inizia il training. Le risorse di calcolo accedono ad account di archiviazione protetti per scaricare i file di training e caricare l'output. 

![Diagramma dell'architettura che mostra il modo in cui un processo di training Azure Machine Learning viene inviato quando si usa un VNet](./media/how-to-network-security-overview/secure-training-job-submission.png)


### <a name="limitations"></a>Limitazioni

- L'istanza di calcolo di Azure e i cluster di calcolo di Azure devono trovarsi nella stessa VNet, nella stessa area e nella stessa sottoscrizione dell'area di lavoro e delle risorse associate. 

## <a name="secure-the-inferencing-environment"></a>Proteggere l'ambiente di inferenza

In questa sezione vengono illustrate le opzioni disponibili per la protezione di un ambiente di inferenza. Si consiglia di usare i cluster dei servizi Kubernetes di Azure per le distribuzioni di produzione su vasta scala.

Sono disponibili due opzioni per i cluster AKS in una rete virtuale:

- Distribuire o alleghi un cluster AKS predefinito a VNet.
- Alleghi un cluster AKS privato alla tua VNet.

I **cluster AKS predefiniti** hanno un piano di controllo con indirizzi IP pubblici. È possibile aggiungere un cluster AKS predefinito al VNet durante la distribuzione o alleghi un cluster dopo che è stato creato.

I **cluster AKS privati** hanno un piano di controllo, a cui è possibile accedere solo tramite indirizzi IP privati. Dopo la creazione del cluster, è necessario collegare i cluster AKS privati.

Per istruzioni dettagliate su come aggiungere i cluster predefiniti e privati, vedere [proteggere un ambiente di inferenza](how-to-secure-inferencing-vnet.md). 

Il diagramma di rete seguente mostra un'area di lavoro Azure Machine Learning protetta con un cluster AKS privato collegato alla rete virtuale.

![Diagramma dell'architettura che Mostra come alleghi un cluster AKS privato alla rete virtuale. Il piano di controllo AKS si trova al di fuori del cliente VNet](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Limitazioni
- I cluster AKS devono appartenere allo stesso VNet dell'area di lavoro e delle risorse associate. 

## <a name="optional-enable-studio-functionality"></a>Facoltativo: abilitare la funzionalità di studio

[Proteggere l'area di lavoro](#secure-the-workspace-and-associated-resources)  >  [Proteggere l'ambiente](#secure-the-training-environment)  >  di training [Proteggere l'ambiente](#secure-the-inferencing-environment)  >  di inferenza **Abilitare la funzionalità**  >  di studio [Configurare le impostazioni del firewall](#configure-firewall-settings)

Se la risorsa di archiviazione si trova in una VNet, è necessario prima eseguire ulteriori passaggi di configurazione per abilitare la funzionalità completa in [Studio](overview-what-is-machine-learning-studio.md). Per impostazione predefinita, la funzionalità seguente è disabilitata:

* Visualizzare in anteprima i dati in studio.
* Visualizza i dati nella finestra di progettazione.
* Inviare un esperimento AutoML.
* Avviare un progetto di assegnazione di etichette.

Per abilitare la funzionalità di studio completo all'interno di un VNet, vedere [usare Azure Machine Learning Studio in una rete virtuale](how-to-enable-studio-virtual-network.md#access-data-using-the-studio). Studio supporta gli account di archiviazione usando gli endpoint di servizio o gli endpoint privati.

### <a name="limitations"></a>Limitazioni
- L' [assegnazione di etichette ai dati assistiti da ml](how-to-create-labeling-projects.md#use-ml-assisted-labeling) non supporta gli account di archiviazione predefiniti protetti dietro una rete virtuale. È necessario usare un account di archiviazione non predefinito per l'assegnazione di etichette ai dati assistiti da ML. Si noti che l'account di archiviazione non predefinito può essere protetto dietro la rete virtuale. 

## <a name="configure-firewall-settings"></a>Configurare le impostazioni del firewall

Configurare il firewall per controllare l'accesso alle risorse dell'area di lavoro Azure Machine Learning e alla rete Internet pubblica. Sebbene sia consigliabile usare il firewall di Azure, è possibile usare altri prodotti firewall per proteggere la rete. Per domande su come consentire la comunicazione attraverso il firewall, consultare la documentazione relativa al firewall in uso.

Per altre informazioni sulle impostazioni del firewall, vedere [usare l'area di lavoro dietro un firewall](how-to-access-azureml-behind-firewall.md).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo fa parte di una serie di reti virtuali in quattro parti. Vedere il resto degli articoli per informazioni su come proteggere una rete virtuale:

* [Parte 2: Panoramica di rete virtuale](how-to-secure-workspace-vnet.md)
* [Parte 3: proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Parte 4: proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)
* [Parte 5: abilitare la funzionalità di studio](how-to-enable-studio-virtual-network.md)
