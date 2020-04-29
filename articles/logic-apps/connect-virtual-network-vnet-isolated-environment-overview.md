---
title: Accedere alle reti virtuali di Azure
description: Panoramica su come gli ambienti di Integration Services (ISEs) consentono alle app per la logica di accedere alle reti virtuali di Azure (reti virtuali)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127257"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accedere alle risorse di Rete virtuale di Azure da App per la logica di Azure usando ambienti del servizio di integrazione (ISE)

A volte, le app per la logica devono accedere alle risorse protette, ad esempio macchine virtuali (VM) e altri sistemi o servizi, che si trovano all'interno di una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per configurare questo accesso, è possibile [creare un *ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). ISE è un'istanza isolata del servizio app per la logica che usa risorse dedicate e viene eseguita separatamente dal servizio "globale" di app per la logica multi-tenant.

L'esecuzione di app per la logica nell'istanza isolata separata consente di ridurre l'impatto che altri tenant di Azure potrebbero avere sulle prestazioni delle app, noto anche come [effetto "vicini rumorosi"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Un ISE offre inoltre i vantaggi seguenti:

* Indirizzi IP statici distinti, separati dagli indirizzi IP statici condivisi dalle app per la logica nel servizio multi-tenant. È anche possibile configurare un unico indirizzo IP in uscita pubblico, statico e prevedibile per comunicare con i sistemi di destinazione. In questo modo, non è necessario configurare ulteriori aperture del firewall in questi sistemi di destinazione per ogni ISE.

* Aumento dei limiti di durata dell'esecuzione, conservazione dell'archiviazione, velocità effettiva, timeout di richieste e risposte HTTP, dimensioni dei messaggi e richieste di connettori personalizzati. Per altre informazioni, vedere [limiti e configurazione per app per la logica di Azure](logic-apps-limits-and-config.md).

> [!NOTE]
> Alcune reti virtuali di Azure usano endpoint privati ([collegamento privato di Azure](../private-link/private-link-overview.md)) per fornire l'accesso ai servizi PaaS di Azure, ad esempio archiviazione di azure, Azure Cosmos DB o il database SQL di Azure, i servizi dei partner o i servizi dei clienti ospitati in Azure. Se le app per la logica necessitano dell'accesso alle reti virtuali che usano endpoint privati, è necessario creare, distribuire ed eseguire tali app per la logica all'interno di ISE.

Quando si crea un ISE, Azure *inserisce* o distribuisce ISE nella rete virtuale di Azure. È quindi possibile usare questo ISE come percorso per le app per la logica e gli account di integrazione che richiedono l'accesso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Le app per la logica possono accedere alle risorse che si trovano all'interno o connesse alla rete virtuale usando questi elementi, eseguiti nello stesso ISE delle app per la logica:

* Un trigger o un'azione incorporata con etichetta **Core**, ad esempio il trigger o l'azione http
* Un connettore con etichetta **ISE**per quel sistema o servizio
* Un connettore personalizzato

È comunque possibile usare anche i connettori che non hanno l'etichetta **Core** o **ISE** con le app per la logica in ISE. Questi connettori vengono eseguiti nel servizio app per la logica multi-tenant. Per ulteriori informazioni, vedere le sezioni seguenti:

* [Isolato rispetto a multi-tenant](#difference)
* [Connettersi da un ambiente del servizio di integrazione](../connectors/apis-list.md#integration-service-environment)
* [Connettori ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Le app per la logica, i trigger incorporati, le azioni predefinite e i connettori eseguiti in ISE usano un piano tariffario diverso dal piano tariffario in base al consumo. Per altre informazioni, vedere [modello di determinazione prezzi per app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica. Per informazioni dettagliate sui prezzi, vedere [prezzi di app](../logic-apps/logic-apps-pricing.md)per la logica.

Questa panoramica descrive altre informazioni sul modo in cui un ISE fornisce alle app per la logica l'accesso diretto alla rete virtuale di Azure e confronta le differenze tra un ISE e il servizio app per la logica multi-tenant.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Isolato rispetto a multi-tenant

Quando si creano ed eseguono app per la logica in ISE, si ottengono le stesse esperienze utente e funzionalità simili a quelle del servizio app per la logica multi-tenant. È possibile usare tutti gli stessi trigger, azioni e connettori gestiti predefiniti disponibili nel servizio app per la logica multi-tenant. Alcuni connettori gestiti offrono versioni di ISE aggiuntive. La differenza tra i connettori ISE e i connettori non ISE esiste nei punti in cui vengono eseguiti e le etichette presenti nella finestra di progettazione delle app per la logica quando si lavora all'interno di ISE.

![Connettori con e senza etichette in ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Trigger e azioni predefiniti visualizzano l'etichetta **principale** . Vengono sempre eseguiti nello stesso ISE dell'app per la logica. I connettori gestiti che visualizzano l'etichetta **ISE** vengono eseguiti anche nello stesso ISE dell'app per la logica.

  Ad esempio, di seguito sono riportati alcuni connettori che offrono le versioni di ISE:

  * Archiviazione BLOB, Archiviazione file e Archiviazione tabelle di Azure
  * Code di Azure, bus di servizio di Azure, hub eventi di Azure e IBM MQ
  * FTP e SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 ed EDIFACT

* I connettori gestiti che non visualizzano etichette aggiuntive vengono sempre eseguiti nel servizio app per la logica multi-tenant, ma è comunque possibile usare questi connettori in un'app per la logica ospitata ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Accesso ai sistemi locali

Per accedere a sistemi locali o origini dati connesse a una rete virtuale di Azure, le app per la logica in ISE possono usare questi elementi:

* Azione HTTP

* Connettore con etichetta ISE per quel sistema

  > [!NOTE]
  > Per usare l'autenticazione di Windows con il connettore SQL Server in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), usare la versione non ISE del connettore con il [gateway dati locale](../logic-apps/logic-apps-gateway-install.md). La versione con etichetta ISE non supporta l'autenticazione di Windows.

* Connettore personalizzato

  * Se sono presenti connettori personalizzati che richiedono il gateway dati locale e i connettori sono stati creati al di fuori di un ISE, le app per la logica in un ISE possono anche usare tali connettori.

  * I connettori personalizzati creati in un ISE non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente alle origini dati locali connesse alla rete virtuale che ospita ISE. Quindi, le app per la logica in un ISE probabilmente non necessitano del gateway dati durante la comunicazione con tali risorse.

Per i sistemi locali che non sono connessi a una rete virtuale o che non hanno connettori con etichetta ISE, è necessario prima [configurare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) prima che le app per la logica possano connettersi a tali sistemi.

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKU ISE

Quando si crea ISE, è possibile selezionare lo SKU per sviluppatori o lo SKU Premium. Ecco le differenze tra gli SKU seguenti:

* **Sviluppatore**

  Fornisce un costo di ISE più basso che è possibile usare per la sperimentazione, lo sviluppo e il test, ma non per i test di produzione e delle prestazioni. Lo SKU Developer include trigger e azioni predefiniti, connettori standard, connettori aziendali e un singolo account di integrazione del [livello gratuito](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) per un prezzo mensile fisso. Tuttavia, questo SKU non include alcun contratto di servizio (SLA), opzioni per la scalabilità verticale o ridondanza durante il riciclo, il che significa che è possibile che si verifichino ritardi o tempi di inattività.

* **Premium**

  Fornisce un ISE che è possibile usare per la produzione e include supporto del contratto di servizio, trigger e azioni predefiniti, connettori standard, connettori aziendali, un unico account di integrazione del [livello standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , opzioni per la scalabilità verticale e ridondanza durante il riciclo per un prezzo mensile fisso.

> [!IMPORTANT]
> L'opzione SKU è disponibile solo alla creazione di ISE e non può essere modificata in un secondo momento.

Per informazioni sui prezzi, vedere [prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica. Per informazioni sul funzionamento dei prezzi e della fatturazione per ISEs, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Accesso endpoint ISE

Quando si crea ISE, è possibile scegliere di usare endpoint di accesso interni o esterni. La selezione determina se i trigger di richiesta o webhook nelle app per la logica in ISE possono ricevere chiamate dall'esterno della rete virtuale.

Questi endpoint influiscono anche sul modo in cui è possibile accedere agli input e agli output nella cronologia di esecuzione delle app per la logica.

* **Interno**: endpoint privati che consentono chiamate a app per la logica in ISE, in cui è possibile visualizzare e accedere agli input e agli output delle app per la logica nella cronologia di esecuzione *solo dall'interno della rete virtuale*

* **External**: endpoint pubblici che consentono chiamate a app per la logica in ISE, in cui è possibile visualizzare e accedere agli input e agli output delle app per la logica nella cronologia di esecuzione *dall'esterno della rete virtuale*. Se si usano i gruppi di sicurezza di rete (gruppi), assicurarsi che siano configurati con regole in ingresso per consentire l'accesso agli input e agli output della cronologia di esecuzione. Per altre informazioni, vedere [abilitare l'accesso per ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> L'opzione endpoint di accesso è disponibile solo alla creazione di ISE e non può essere modificata in un secondo momento.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Account di integrazione con ambiente del servizio di integrazione

È possibile usare gli account di integrazione con App per la logica all'interno di un ambiente del servizio di integrazione (ISE). È necessario tuttavia che gli account di integrazione usino lo *stesso ISE* delle app per la logica collegate. Le app per la logica in un ambiente del servizio di integrazione possono fare riferimento solo agli account di integrazione che sono nello stesso ambiente del servizio di integrazione. Quando si crea un account di integrazione, è possibile selezionare l'ambiente del servizio di integrazione come posizione per l'account di integrazione. Per informazioni sul funzionamento dei prezzi e della fatturazione per gli account di integrazione con ISE, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica. Per informazioni sui prezzi, vedere [prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica.

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
