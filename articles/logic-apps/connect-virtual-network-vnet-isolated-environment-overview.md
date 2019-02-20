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
ms.date: 02/12/2019
ms.openlocfilehash: 204138e7b8b3846e2d50607b3c5ec0836abefe24
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162374"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accedere alle risorse di Rete virtuale di Azure da App per la logica di Azure usando ambienti del servizio di integrazione (ISE)

> [!NOTE]
> Questa funzionalità è in *anteprima privata*. Per partecipare all'anteprima privata, [creare la richiesta qui](https://aka.ms/iseprivatepreview).

A volte, le app per la logica e gli account di integrazione devono accedere a risorse protette, ad esempio macchine virtuali e altri sistemi o servizi in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per impostare l'accesso, è possibile [creare un *ambiente del servizio di integrazione*](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) (ISE, Integration Service Environment) in cui eseguire le app per la logica e gli account di integrazione.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Con la creazione di un ambiente del servizio di integrazione, un'istanza di App per la logica privata e isolata viene distribuita nella rete virtuale di Azure. L'istanza privata usa risorse dedicate, ad esempio lo spazio di archiviazione, e viene eseguita separatamente dal servizio App per la logica pubblico "globale". Questa separazione consente anche di ridurre il potenziale impatto degli altri tenant di Azure, che [possono influire negativamente](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) sulle prestazioni delle app. 

Questa panoramica descrive come un ambiente del servizio di integrazione consenta alle app per la logica e agli account di integrazione di accedere direttamente alla rete virtuale di Azure e descrive le differenze tra un ambiente del servizio di integrazione e il servizio App per la logica globale.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Ambiente isolato e ambiente globale

Quando si crea un ambiente del servizio di integrazione (ISE) in Azure, selezionare la rete virtuale di Azure in cui si vuole *inserire* l'ambiente. Azure distribuisce un'istanza privata del servizio App per la logica nella rete virtuale. Questa azione crea un ambiente isolato in cui è possibile creare ed eseguire le app per la logica in risorse dedicate. Quando si crea un'app per la logica, selezionare questo ambiente come percorso dell'app, fornendo così all'app per la logica l'accesso diretto alle risorse nella rete virtuale.

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

> [!IMPORTANT]
> Le app per la logica, le azioni predefinite e i connettori eseguiti nell'ambiente del servizio di integrazione usano un piano tariffario diverso, non quello con pagamento in base al consumo. Per altre informazioni, vedere [Prezzi di App per la logica](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Autorizzazioni per l'accesso alla rete virtuale

Quando si crea un ambiente del servizio di integrazione in Azure, si seleziona una rete virtuale di Azure in cui si *inserisce* l'ambiente. L'inserimento distribuisce un'istanza privata del servizio App per la logica nella rete virtuale. Questa azione crea un ambiente isolato in cui è possibile creare ed eseguire le app per la logica in risorse dedicate. Quando si creano le app per la logica selezionare l'ambiente del servizio di integrazione (ISE) come percorso delle app. Le app per la logica possono quindi accedere direttamente alla rete virtuale e connettersi alle risorse nella rete.

Nei sistemi connessi a una rete virtuale inserire un ambiente del servizio di integrazione (ISE) nella rete virtuale per consentire l'accesso diretto delle app per la logica ai sistemi tramite uno degli elementi seguenti:

* Connettore ISE per tale sistema, ad esempio, SQL Server

* Azione HTTP

* Connettore personalizzato

È possibile connettersi ai sistemi locali non connessi a una rete virtuale o senza connettori ISE [configurando e usando il gateway dati locale](../logic-apps/logic-apps-gateway-install.md).

Prima di poter selezionare una rete virtuale di Azure per l'inserimento dell'ambiente, è necessario configurare le autorizzazioni del controllo degli accessi in base al ruolo nella rete virtuale per il servizio App per la logica di Azure. Per eseguire questa attività, è necessario assegnare i ruoli **Collaboratore Rete** e **Collaboratore Rete classica** al servizio App per la logica di Azure.
Per impostare queste autorizzazioni, vedere [Connettere le reti virtuali di Azure da App per la logica](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access)

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
