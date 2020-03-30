---
title: Accesso alle reti virtuali di AzureAccess to Azure virtual networks
description: Panoramica su come gli ambienti del servizio di integrazione (ISE) aiutano le app per la logica ad accedere alle reti virtuali di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127257"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accedere alle risorse di Rete virtuale di Azure da App per la logica di Azure usando ambienti del servizio di integrazione (ISE)

In alcuni casi, le app per la logica devono accedere alle risorse protette, ad esempio macchine virtuali (VM) e altri sistemi o servizi, che si trovano all'interno di una rete virtuale di Azure.Sometimes, your logic apps need access to secured resources, such as virtual machines (VMs) and other systems or services, that are inside an [Azure virtual network.](../virtual-network/virtual-networks-overview.md) Per impostare questo accesso, è possibile creare un ambiente del servizio di [ *integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Un ISE è un'istanza isolata del servizio App per la logica che utilizza risorse dedicate e viene eseguita separatamente dal servizio App per la logica multi-tenant "globale".

L'esecuzione di app per la logica in un'istanza isolata separata consente di ridurre l'impatto che altri tenant di Azure potrebbero avere sulle prestazioni delle app, noto anche come [effetto "vicini rumorosi".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Un ISE offre inoltre i seguenti vantaggi:

* I propri indirizzi IP statici, separati dagli indirizzi IP statici condivisi dalle app per la logica nel servizio multi-tenant. È inoltre possibile impostare un singolo indirizzo IP in uscita pubblico, statico e prevedibile per comunicare con i sistemi di destinazione. In questo modo, non è necessario impostare ulteriori aperture del firewall in tali sistemi di destinazione per ogni ISE.

* Aumento dei limiti di durata dell'esecuzione, conservazione dell'archiviazione, velocità effettiva, timeout delle richieste e risposte HTTP, dimensioni dei messaggi e richieste di connettori personalizzati. Per altre informazioni, vedere Limiti e configurazione per le app per la logica di Azure.For more [information,](logic-apps-limits-and-config.md)see Limits and configuration for Azure Logic Apps .

> [!NOTE]
> Alcune reti virtuali di Azure usano endpoint privati ( Azure Private Link ) per fornire l'accesso ai servizi di Azure PaaS, ad esempio Archiviazione di Azure, Database cosmos di Azure o Database SQL di Azure, servizi partner o servizi clienti ospitati in Azure.Some Azure virtual networks use private endpoints ([Azure Private Link](../private-link/private-link-overview.md)) for providing access to Azure PaaS services, such as Azure Storage, Azure Cosmos DB, or Azure SQL Database, partner services, or customer services that are hosted on Azure. Se le app per la logica devono accedere a reti virtuali che usano endpoint privati, è necessario creare, distribuire ed eseguire tali app per la logica all'interno di un ISE.

Quando si crea un ISE, Azure inserisce o distribuisce tale ISE nella rete virtuale di Azure.When you create an ISE, Azure *injects* that ISE into your Azure virtual network. È quindi possibile usare questo ISE come posizione per le app per la logica e gli account di integrazione che richiedono l'accesso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Le app per la logica possono accedere alle risorse all'interno o connesse alla rete virtuale usando questi elementi, che vengono eseguiti nelle stesse app per la logica delle app per la logica:Logic apps can access resources that are inside or connected to your virtual network by using these items, which run in the same ISE as your logic apps:

* Un trigger incorporato o un'azione con etichetta **core,** ad esempio il trigger o l'azione HTTP
* Un connettore con etichetta **ISE**per tale sistema o servizio
* Un connettore personalizzato

È comunque possibile usare connettori che non dispongono dell'etichetta **CORE** o **ISE** con le app per la logica in ISE. Questi connettori vengono invece eseguiti nel servizio App per la logica multi-tenant. Per altre informazioni, vedere le sezioni seguenti:For more information, see these sections:

* [Isolato e multi-tenant](#difference)
* [Connettersi da un ambiente di servizio di integrazioneConnect from an integration service environment](../connectors/apis-list.md#integration-service-environment)
* [Connettori ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Le app per la logica, i trigger predefiniti, le azioni predefinite e i connettori eseguiti in ISE usano un piano tariffario diverso dal piano tariffario basato sul consumo. Per ulteriori informazioni, vedere Modello di [determinazione dei prezzi delle app per](../logic-apps/logic-apps-pricing.md#fixed-pricing)la logica . Per informazioni dettagliate sui prezzi, vedere [App per la logica.](../logic-apps/logic-apps-pricing.md)

Questa panoramica descrive altre informazioni su come un ISE consente alle app per la logica l'accesso diretto alla rete virtuale di Azure e confronta le differenze tra un ISE e il servizio App per la logica multi-tenant.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Isolato e multi-tenant

Quando si creano ed eseguono app per la logica in un ISE, si ottengono le stesse esperienze utente e funzionalità simili del servizio App per la logica multi-tenant. È possibile usare tutti gli stessi trigger incorporati, azioni e connettori gestiti disponibili nel servizio App per la logica multi-tenant. Alcuni connettori gestiti offrono versioni ISE aggiuntive. La differenza tra i connettori ISE e connettori non ISE esiste nella posizione in cui vengono eseguiti e le etichette che hanno in Progettazione app per la logica quando si lavora all'interno di un ISE.

![Connettori con e senza etichette in un ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* I trigger e le azioni incorporati visualizzano l'etichetta **CORE.** Vengono sempre eseguiti nella stessa ISE dell'app per la logica. I connettori gestiti che visualizzano l'etichetta **ISE** vengono eseguiti anche nella stessa ISE dell'app per la logica.

  Ad esempio, di seguito sono riportati alcuni connettori che offrono versioni ISE:

  * Archiviazione BLOB, Archiviazione file e Archiviazione tabelle di Azure
  * Code di Azure, bus di servizio di Azure, hub eventi di Azure e IBM MQ
  * FTP e SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, database Cosmos di Azure
  * AS2, X12 ed EDIFACT

* I connettori gestiti che non visualizzano etichette aggiuntive vengono sempre eseguiti nel servizio App per la logica multi-tenant, ma è comunque possibile usare questi connettori in un'app per la logica ospitata da ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Accesso ai sistemi locali

Per accedere a i sistemi locali o le origini dati connesse a una rete virtuale di Azure, le app per la logica in un ISE possono usare questi elementi:To access on-premises systems or data sources that are connected to an Azure virtual network, logic apps in an ISE can use these items:

* Azione HTTP

* Connettore con etichetta ISE per tale sistema

  > [!NOTE]
  > Per utilizzare l'autenticazione di Windows con il connettore SQL Server in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)utilizzare la versione non ISE del connettore con il [gateway dati locale.](../logic-apps/logic-apps-gateway-install.md) La versione con etichetta ISE non supporta l'autenticazione di Windows.

* Connettore personalizzato

  * Se si dispone di connettori personalizzati che richiedono il gateway dati locale e tali connettori sono stati creati all'esterno di ISE, anche le app per la logica in ISE possono utilizzare tali connettori.

  * I connettori personalizzati creati in ISE non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente alle origini dati locali connesse alla rete virtuale che ospita ISE. Pertanto, le app per la logica in un ISE molto probabilmente non hanno bisogno del gateway dati durante la comunicazione con tali risorse.

Per i sistemi locali che non sono connessi a una rete virtuale o non dispongono di connettori con etichetta ISE, è necessario [configurare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) prima che le app per la logica possano connettersi a tali sistemi.

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKU ISE

Quando crei ISE, puoi selezionare lo SKU per sviluppatori o lo SKU Premium. Di seguito sono riportate le differenze tra questi SKU:Here are the differences between these SKUs:

* **Sviluppatore**

  Fornisce un ISE a basso costo che è possibile utilizzare per la sperimentazione, lo sviluppo e il test, ma non per la produzione o il test delle prestazioni. Lo SKU per sviluppatori include trigger e azioni incorporati, connettori standard, connettori Enterprise e un singolo account di integrazione di [livello gratuito](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) a un prezzo mensile fisso. Tuttavia, questo SKU non include alcun contratto di servizio (SLA), opzioni per la scalabilità verticale della capacità o ridondanza durante il riciclo, il che significa che potrebbero verificarsi ritardi o tempi di inattività.

* **Premium**

  Fornisce un ISE che è possibile utilizzare per la produzione e include il supporto SLA, trigger e azioni incorporati, connettori Standard, connettori Enterprise, un singolo account di integrazione [a livello Standard,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) opzioni per la scalabilità verticale della capacità e ridondanza durante il riciclaggio a un prezzo mensile fisso.

> [!IMPORTANT]
> L'opzione SKU è disponibile solo al momento della creazione di ISE e non può essere modificata in un secondo momento.

Per i prezzi, vedere [Prezzi di App per la logica.](https://azure.microsoft.com/pricing/details/logic-apps/) Per informazioni sul funzionamento dei prezzi e della fatturazione per ISE, vedere il modello di determinazione dei prezzi app per la [logica.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Accesso all'endpoint ISE

Quando si crea ISE, è possibile scegliere di utilizzare endpoint di accesso interni o esterni. La selezione determina se i trigger di richiesta o webhook nelle app per la logica in ISE possono ricevere chiamate dall'esterno della rete virtuale.

Questi endpoint influiscono anche sul modo in cui è possibile accedere agli input e agli output nella cronologia di esecuzione delle app per la logica.

* **Interno:** endpoint privati che consentono chiamate alle app per la logica in ISE in cui è possibile visualizzare e accedere agli input e agli output delle app per la logica nella cronologia di esecuzione *solo dall'interno della rete virtuale*

* **Esterno:** endpoint pubblici che consentono chiamate alle app per la logica nelle app per la logica in ISE in cui è possibile visualizzare e accedere agli input e agli output delle app per la logica nella cronologia di esecuzione *dall'esterno della rete virtuale.* Se si usano gruppi di sicurezza di rete, assicurarsi che siano impostati con regole in ingresso per consentire l'accesso agli input e agli output della cronologia di esecuzione. Per ulteriori informazioni, vedere [Abilitare l'accesso per ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

> [!IMPORTANT]
> L'opzione dell'endpoint di accesso è disponibile solo al momento della creazione di ISE e non può essere modificata in un secondo momento.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Account di integrazione con ambiente del servizio di integrazione

È possibile usare gli account di integrazione con App per la logica all'interno di un ambiente del servizio di integrazione (ISE). È necessario tuttavia che gli account di integrazione usino lo *stesso ISE* delle app per la logica collegate. Le app per la logica in un ambiente del servizio di integrazione possono fare riferimento solo agli account di integrazione che sono nello stesso ambiente del servizio di integrazione. Quando si crea un account di integrazione, è possibile selezionare l'ambiente del servizio di integrazione come posizione per l'account di integrazione. Per informazioni sul funzionamento dei prezzi e della fatturazione per gli account di integrazione con ISE, vedere il modello di determinazione dei prezzi app per la [logica.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Per i prezzi, vedere [Prezzi di App per la logica.](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
