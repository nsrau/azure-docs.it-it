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
ms.date: 03/11/2019
ms.openlocfilehash: b53cd54afdf6243769602971ab77145cfa9ba9cc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57758778"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accedere alle risorse di Rete virtuale di Azure da App per la logica di Azure usando ambienti del servizio di integrazione (ISE)

> [!NOTE]
> Questa funzionalità è disponibile in [ *versione di anteprima pubblica*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A volte, le app per la logica e gli account di integrazione devono accedere a risorse protette, ad esempio macchine virtuali e altri sistemi o servizi in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per impostare l'accesso, è possibile [creare un *ambiente del servizio di integrazione*](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) (ISE, Integration Service Environment) in cui eseguire le app per la logica e gli account di integrazione. Quando si crea un'istanza di ISE, Azure distribuisce un'istanza isolata e privata del servizio App per la logica nella rete virtuale di Azure. L'istanza privata usa risorse dedicate, ad esempio lo spazio di archiviazione, e viene eseguita separatamente dal servizio App per la logica pubblico "globale". La separazione tra l'istanza privata isolata e l'istanza globale pubblico contribuisce a ridurre l'impatto di altri tenant di Azure potrebbe avere sulle prestazioni delle proprie App, che è noto anche come il [effetto "vicini fastidiosi"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Dopo aver creato l'ISE, quando si passa a creare l'account di integrazione o app per la logica, è possibile selezionare l'ISE come percorso per la logica app o l'integrazione del tuo account:

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

App per la logica possa accedere direttamente ai sistemi che si trovano all'interno o connesso alla rete virtuale usando uno di questi elementi:

* Un connettore ISE con controllo delle versioni per tale sistema, ad esempio, SQL Server
* Trigger predefinito o azione, ad esempio il trigger HTTP o l'azione
* Un connettore personalizzato

Questa panoramica descrive altri dettagli sul modo in cui un'istanza di ISE offre App per la logica e integrazione degli account di accesso diretto alla rete virtuale di Azure e confronta le differenze tra un'istanza di ISE e il servizio App per la logica globale.
Per i sistemi in locale che non sono connessi a una rete virtuale o non avere connettori ISE versione, è possibile connettersi a questi sistemi da [impostazione e uso del gateway dati locale](../logic-apps/logic-apps-gateway-install.md).

> [!IMPORTANT]
> Le app per la logica, le azioni predefinite e i connettori eseguiti nell'ambiente del servizio di integrazione usano un piano tariffario diverso, non quello con pagamento in base al consumo. Per altre informazioni, vedere [Prezzi di App per la logica](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Ambiente isolato e ambiente globale

Quando si crea un ambiente del servizio integrato (ISE) in Azure, è possibile selezionare la rete virtuale di Azure in cui si desidera *inserire* di ISE. Azure inserisce quindi o distribuisce, un'istanza privata del servizio App per la logica nella rete virtuale. Questa azione crea un ambiente isolato in cui è possibile creare ed eseguire le app per la logica in risorse dedicate. Quando si crea l'app per la logica, selezionare l'ISE come percorso dell'app, che offre l'accesso diretto di app per la logica per la rete virtuale e le risorse in tale rete.

Le app per la logica in un ambiente del servizio di integrazione offrono le stesse esperienze utente e funzionalità simili a quelle del servizio App per la logica globale. Non solo è possibile usare le stesse azioni predefinite e gli stessi connettori nel servizio App per la logica globale, ma è anche possibile usare connettori specifici dell'ambiente del servizio di integrazione (ISE). Ecco ad esempio alcuni connettori standard che offrono versioni eseguite in un ambiente del servizio di integrazione:

* Archiviazione BLOB, Archiviazione file e Archiviazione tabelle di Azure
* Code di Azure, bus di servizio di Azure, hub eventi di Azure e IBM MQ
* FTP e SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 ed EDIFACT

La differenza tra connettori dell'ambiente del servizio di integrazione e quelli non dell'ambiente del servizio di integrazione è data dalle posizioni in cui i trigger e le azioni vengono eseguiti:

* Nell'ambiente di integrazione (ISE) i trigger predefiniti e le azioni, ad esempio il trigger HTTP, vengono sempre eseguiti nello stesso ambiente del servizio di integrazione dell'app per la logica.

* Per i connettori che offrono due versioni: una versione viene eseguita in un ambiente del servizio di integrazione, mentre l'altra versione viene eseguita nel servizio App per la logica globale.  

  I connettori con l'etichetta **ISE** vengono sempre eseguiti nello stesso ambiente del servizio di integrazione dell'app per la logica. I connettori senza l'etichetta **ISE** vengono eseguiti nel servizio App per la logica globale.

  ![Selezionare i connettori ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* I connettori eseguiti in un ambiente del servizio di integrazione sono disponibili anche nel servizio App per la logica globale.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Account di integrazione con ambiente del servizio di integrazione

È possibile usare gli account di integrazione con App per la logica all'interno di un ambiente del servizio di integrazione (ISE). È necessario tuttavia che gli account di integrazione usino lo *stesso ISE* delle app per la logica collegate. Le app per la logica in un ambiente del servizio di integrazione possono fare riferimento solo agli account di integrazione che sono nello stesso ambiente del servizio di integrazione. Quando si crea un account di integrazione, è possibile selezionare l'ambiente del servizio di integrazione come posizione per l'account di integrazione.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum di App per la logica di Azure</a>.
* Per votare o inviare idee relative alle funzionalità, visitare il <a href="https://aka.ms/logicapps-wish" target="_blank">sito dei commenti e suggerimenti degli utenti di App per la logica</a>.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi alle reti virtuali di Azure da app per la logica isolate](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
