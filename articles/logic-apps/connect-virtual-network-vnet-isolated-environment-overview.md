---
title: Accedere alle reti virtuali di Azure da App per la logica di Azure con ambienti del servizio di integrazione (ISE)
description: Questa panoramica descrive in che modo gli ambienti del servizio di integrazione (ISE) consentono alle app per la logica di accedere alle reti virtuali di Azure (VNET)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: f981452b06ec06f2be1b8fe0319cd49a678ccfe0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441571"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accedere alle risorse di Rete virtuale di Azure da App per la logica di Azure usando ambienti del servizio di integrazione (ISE)

In alcuni casi, l'App per la logica e un account di integrazione devono accedere alle risorse protette, ad esempio macchine virtuali (VM) e altri sistemi o servizi, che sono all'interno di un' [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per configurare l'accesso, è possibile [creare un' *ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) in cui è possibile eseguire le App per la logica e creare l'integrazione di account.

Quando si crea un'istanza di ISE, Azure distribuisce un'istanza isolata e privata del servizio App per la logica nella rete virtuale di Azure. L'istanza privata usa risorse dedicate, ad esempio lo spazio di archiviazione, e viene eseguita separatamente dal servizio App per la logica pubblico "globale". La separazione tra l'istanza privata isolata e l'istanza globale pubblico contribuisce a ridurre l'impatto di altri tenant di Azure potrebbe avere sulle prestazioni delle proprie App, che è noto anche come il [effetto "vicini fastidiosi"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Dopo aver creato l'ISE, quando si passa a creare l'account di integrazione o app per la logica, è possibile selezionare l'ISE come percorso per la logica app o l'integrazione del tuo account:

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

App per la logica possa accedere direttamente ai sistemi che si trovano all'interno o connesso alla rete virtuale usando uno di questi elementi:

* Un' **ISE**-connettore per tale sistema, ad esempio SQL Server con l'etichetta
* Oggetto **Core**-con l'etichetta predefinita trigger o azione, ad esempio il trigger HTTP o l'azione
* Un connettore personalizzato

Questa panoramica descrive altri dettagli sul modo in cui un'istanza di ISE offre App per la logica e integrazione degli account di accesso diretto alla rete virtuale di Azure e confronta le differenze tra un'istanza di ISE e il servizio App per la logica globale.

> [!NOTE]
> App per la logica, incorporato trigger, azioni predefinite e i connettori eseguiti per l'utilizzo ISE un piano tariffario diverso dal piano tariffario in base al consumo. Per altre informazioni, vedere [Prezzi di App per la logica](../logic-apps/logic-apps-pricing.md). L'ISE inoltre dispone di un aumento dei limiti sulla durata dell'esecuzione, conservazione archiviazione, velocità effettiva, richiesta HTTP e timeout risposta, le dimensioni dei messaggi e le richieste di connettore personalizzato. Per altre informazioni, vedere [limiti e configurazione per le App per la logica di Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Ambiente isolato e ambiente globale

Quando si crea un ambiente del servizio integrato (ISE) in Azure, è possibile selezionare la rete virtuale di Azure in cui si desidera *inserire* di ISE. Azure inserisce quindi o distribuisce, un'istanza privata del servizio App per la logica nella rete virtuale. Questa azione crea un ambiente isolato in cui è possibile creare ed eseguire le app per la logica in risorse dedicate. Quando si crea l'app per la logica, selezionare l'ISE come percorso dell'app, che offre l'accesso diretto di app per la logica per la rete virtuale e le risorse in tale rete.

Le app per la logica in un ambiente del servizio di integrazione offrono le stesse esperienze utente e funzionalità simili a quelle del servizio App per la logica globale. Non solo è possibile utilizzare il trigger incorporato stesso, azioni predefinite e i connettori del servizio App per la logica globale, ma è anche possibile usare i connettori di specifiche di ISE. Ad esempio, ecco alcuni connettori Standard che offrono le versioni che vengono eseguiti in un'istanza di ISE:

* Archiviazione BLOB, Archiviazione file e Archiviazione tabelle di Azure
* Code di Azure, bus di servizio di Azure, hub eventi di Azure e IBM MQ
* FTP e SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 ed EDIFACT

La differenza tra connettori dell'ambiente del servizio di integrazione e quelli non dell'ambiente del servizio di integrazione è data dalle posizioni in cui i trigger e le azioni vengono eseguiti:

* Nella finestra di ISE, incorporato trigger e azioni, ad esempio HTTP, sempre eseguiti in ISE stesso come l'app per la logica e la visualizzazione di **Core** etichetta.

  ![Selezionare le azioni e trigger predefinito "Core"](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* I connettori eseguiti in un'istanza di ISE pubblicamente hanno ospitato le versioni disponibili nel servizio App per la logica globale. Per i connettori che offrono due versioni, i connettori con i **ISE** etichettare sempre eseguite in ISE stesso come app per la logica. I connettori senza l'etichetta **ISE** vengono eseguiti nel servizio App per la logica globale.

  ![Selezionare i connettori ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

Un'istanza di ISE fornisce anche un aumento dei limiti per la durata dell'esecuzione, conservazione archiviazione, la velocità effettiva, richiesta HTTP e timeout risposta, le dimensioni dei messaggi e richieste di connettore personalizzato. Per altre informazioni, vedere [limiti e configurazione per le App per la logica di Azure](logic-apps-limits-and-config.md).

### <a name="access-to-on-premises-data-sources"></a>Accesso alle origini dati locali

Per i sistemi locali connessi a una rete virtuale di Azure, inserire un'istanza di ISE in tale rete, in modo che App per la logica possono accedere direttamente a tali sistemi con uno di questi elementi:

* Connettore ISE-version per tale sistema, ad esempio, SQL Server
* Azione HTTP
* Connettore personalizzato

  * Se si sono connettori personalizzati che richiedono il gateway dati locale e i connettori all'esterno di un'istanza di ISE è stato creato, le App per la logica in un'istanza di ISE anche possono usare i connettori.
  
  * I connettori personalizzati creati in un'istanza di ISE non funzionano con il gateway dati locale. Tuttavia, questi connettori è possono accedervi direttamente le origini dati locali che sono connessi alla rete virtuale che ospita l'ambiente ISE. Pertanto, le App per la logica in un'istanza di ISE molto probabilmente non necessario il gateway dati quando si comunica con tali risorse.

Per i sistemi in locale che non sono connessi a una rete virtuale o non avere connettori ISE versione, è necessario innanzitutto [configurare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) prima la logica App possono connettersi a tali sistemi.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Account di integrazione con ambiente del servizio di integrazione

È possibile usare gli account di integrazione con App per la logica all'interno di un ambiente del servizio di integrazione (ISE). È necessario tuttavia che gli account di integrazione usino lo *stesso ISE* delle app per la logica collegate. Le app per la logica in un ambiente del servizio di integrazione possono fare riferimento solo agli account di integrazione che sono nello stesso ambiente del servizio di integrazione. Quando si crea un account di integrazione, è possibile selezionare l'ambiente del servizio di integrazione come posizione per l'account di integrazione.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi alle reti virtuali di Azure da app per la logica isolate](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
