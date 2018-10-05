---
title: Accedere alle reti virtuali di Azure da App per la logica di Azure
description: Questa panoramica illustra come app per la logica isolate possano connettersi a reti virtuali di Azure da ambienti del servizio di integrazione che usano risorse private e dedicate
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 9546b8ca33ef7da2d570b547446858e2a4099234
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393146"
---
# <a name="access-to-azure-virtual-network-resources-from-isolated-azure-logic-apps"></a>Accedere alle risorse della rete virtuale di Azure da app per la logica di Azure isolate

> [!NOTE]
> Questa funzionalità è in *anteprima privata*. Per richiedere l'accesso, [creare la richiesta di iscrizione qui](https://aka.ms/iseprivatepreview).

A volte, le app per la logica e gli account di integrazione devono accedere a risorse protette, ad esempio macchine virtuali e altri sistemi o servizi in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per fornire l'accesso, è possibile [creare un *ambiente del servizio di integrazione*](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) (ISE, Integration Service Environment) in cui creare le app per la logica e gli account di integrazione. 

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Con la creazione di un ambiente del servizio di integrazione, un'istanza di App per la logica privata e isolata viene distribuita nella rete virtuale di Azure. L'istanza privata usa risorse dedicate, ad esempio lo spazio di archiviazione, e viene eseguita separatamente dal servizio App per la logica pubblico "globale". Questa separazione consente anche di ridurre il potenziale impatto degli altri tenant di Azure, che [possono influire negativamente](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) sulle prestazioni delle app. 

Questa panoramica descrive come la creazione di un ambiente del servizio di integrazione consenta alle app per la logica e agli account di integrazione di accedere direttamente alle risorse nella rete virtuale di Azure e confronta le differenze tra un ambiente del servizio di integrazione e il servizio App per la logica globale.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Ambiente isolato e ambiente globale

Quando si crea un ambiente del servizio di integrazione in Azure, è possibile selezionare una rete virtuale di Azure come *peer* per l'ambiente. Azure distribuisce un'istanza privata del servizio App per la logica nella rete virtuale, dando vita a un ambiente isolato in cui è possibile creare ed eseguire le app per la logica in risorse dedicate. Quando si crea un'app per la logica, è possibile selezionare questo ambiente come percorso dell'app, fornendo così all'app per la logica l'accesso diretto alle risorse nella rete virtuale.  

Le app per la logica in un ambiente del servizio di integrazione offrono le stesse esperienze utente e funzionalità simili a quelle del servizio App per la logica globale. Non solo è possibile usare le stesse risorse predefinite e gli stessi connettori offerti dal servizio App per la logica globale, ma è possibile scegliere tra connettori che forniscono versioni diverse dell'ambiente del servizio di integrazione. Ecco ad esempio alcuni connettori standard che offrono versioni eseguite in un ambiente del servizio di integrazione:
 
* Archiviazione BLOB, Archiviazione file e Archiviazione tabelle di Azure
* Code di Azure
* Bus di servizio di Azure
* FTP
* SSH FTP (SFTP)
* SQL Server
* AS2, X12 ed EDIFACT

La differenza tra connettori dell'ambiente del servizio di integrazione e quelli non dell'ambiente del servizio di integrazione è data dalle posizioni in cui i trigger e le azioni vengono eseguiti:

* Se si usano trigger e azioni predefiniti, ad esempio HTTP, nell'ambiente del servizio di integrazione, questi trigger e azioni vengono sempre eseguiti nello stesso ambiente del servizio di integrazione dell'app per la logica. 

* Per i connettori che offrono due versioni: una versione viene eseguita in un ambiente del servizio di integrazione, mentre l'altra versione viene eseguita nel servizio App per la logica globale.  

  I connettori con l'etichetta **ISE** vengono sempre eseguiti nello stesso ambiente del servizio di integrazione dell'app per la logica. I connettori senza l'etichetta **ISE** vengono eseguiti nel servizio App per la logica globale. 

  ![Selezionare i connettori ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* I connettori configurati nell'ambiente del servizio di integrazione possono essere usati anche nel servizio App per la logica globale. 

> [!IMPORTANT]
> Le app per la logica, le azioni predefinite e i connettori eseguiti nell'ambiente del servizio di integrazione usano un piano tariffario diverso, non quello con pagamento in base al consumo. Per altre informazioni, vedere [Prezzi di App per la logica](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Autorizzazioni per l'accesso alla rete virtuale

Quando si crea un ambiente del servizio di integrazione in Azure, è possibile selezionare una rete virtuale di Azure come *peer* per l'ambiente. È tuttavia possibile creare *solo* questa relazione, o *peering*, quando si crea l'ambiente del servizio di integrazione. Questa relazione consente all'ambiente del servizio di integrazione di accedere alle risorse nella rete virtuale. In questo modo le app per la logica in tale ambiente del servizio di integrazione possono connettersi direttamente alle risorse nella rete virtuale. Le app per la logica possono accedere direttamente ai sistemi locali in una rete virtuale collegata a un ambiente del servizio di integrazione usando uno di questi elementi: 

* Connettore ISE per tale sistema, ad esempio, SQL Server

* Azione HTTP 

* Connettore personalizzato

È possibile connettersi ai sistemi locali non inclusi in una rete virtuale o senza connettori ISE dopo aver [configurato e usato il gateway dati locale](../logic-apps/logic-apps-gateway-install.md).

Prima di poter selezionare una rete virtuale di Azure come peer per l'ambiente, è necessario configurare le autorizzazioni del controllo degli accessi in base al ruolo nella rete virtuale per il servizio App per la logica di Azure. Per eseguire questa attività, è necessario assegnare i ruoli **Collaboratore Rete** e **Collaboratore Rete classica** al servizio App per la logica di Azure. Per altre informazioni sulle autorizzazioni dei ruoli necessarie per il peering, vedere la [sezione Autorizzazioni in Creare, modificare o eliminare un peering reti virtuali](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Account di integrazione con ambiente del servizio di integrazione

È possibile usare gli account di integrazione con le app per la logica eseguite in un ambiente del servizio di integrazione, ma tali account di integrazione devono anche usare lo *stesso ambiente del servizio di integrazione* delle app per la logica collegate. Le app per la logica in un ambiente del servizio di integrazione possono fare riferimento solo agli account di integrazione che sono nello stesso ambiente del servizio di integrazione. Quando si crea un account di integrazione, è possibile selezionare l'ambiente del servizio di integrazione come posizione per l'account di integrazione.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum di App per la logica di Azure</a>.
* Per votare o inviare idee relative alle funzionalità, visitare il <a href="http://aka.ms/logicapps-wish" target="_blank">sito dei commenti e suggerimenti degli utenti di App per la logica</a>.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi alle reti virtuali di Azure da app per la logica isolate](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
