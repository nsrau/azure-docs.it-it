---
title: Connettersi a una rete peer in Azure Lab Services | Microsoft Docs
description: Informazioni su come connettere una rete lab a un'altra rete come peer. Ad esempio, connettere la rete aziendale/universitaria locale alla rete virtuale del lab in Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 20e47113d5c2439c9c8ea355288442b5f41d90ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445832"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Connettere la rete di un lab a una rete virtuale peer in Azure Lab Services

Questo articolo fornisce informazioni sul peering della rete lab con un'altra rete.

## <a name="overview"></a>Panoramica

Il peering di reti virtuali consente di connettere facilmente le reti virtuali di Azure. Dopo avere eseguito il peering, le reti virtuali vengono visualizzate come una sola per scopi di connettività. Il traffico tra le macchine virtuali presenti nelle reti virtuali con peering viene instradato attraverso l'infrastruttura backbone di Microsoft, in modo simile all'instradamento del traffico tra macchine virtuali presenti nella stessa rete virtuale, solo tramite indirizzi IP privati. Per altre informazioni, vedere [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md).

Potrebbe essere necessario connettere la rete del lab a una rete virtuale peer in alcuni scenari, tra cui quelli riportati di seguito:

- Le macchine virtuali nel lab hanno un software che si connette ai server licenze locali per acquisire la licenza.
- Le macchine virtuali nel lab devono accedere ai set di dati (o a qualsiasi altro file) nelle condivisioni di rete dell'università.

Alcune reti locali vengono connesse alla rete virtuale di Azure tramite [ExpressRoute](../expressroute/expressroute-introduction.md) o un [gateway di rete virtuale](../vpn-gateway/vpn-gateway-about-vpngateways.md). Questi servizi devono essere configurati al di fuori di Azure Lab Services. Per altre informazioni sulla connessione di una rete locale ad Azure tramite ExpressRoute, vedere [Panoramica di ExpressRoute](../expressroute/expressroute-introduction.md). Per la connettività locale tramite un gateway di rete virtuale, il gateway, la rete virtuale specificata e l'account lab devono trovarsi tutti nella stessa area.

> [!NOTE]
> Quando si crea una rete virtuale di Azure di cui verrà eseguito il peering con un account lab, è importante comprendere in che modo l'area della rete virtuale influisce sull'area in cui vengono creati i lab per le classi.  Per altre informazioni, vedere la sezione della guida dell'amministratore in [regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Eseguire la configurazione al momento della creazione dell'account lab

Durante la nuova [creazione dell'account lab](tutorial-setup-lab-account.md) è possibile selezionare una rete virtuale esistente visualizzata nell'elenco a discesa **Rete virtuale peer** della scheda **Avanzate**.  L'elenco mostrerà solo le reti virtuali nella stessa area dell'account lab. La rete virtuale selezionata è connessa (con peering) ai lab creati con l'account lab.  Tutte le macchine virtuali nei lab create dopo l'esecuzione di questa modifica avranno accesso alle risorse nella rete virtuale con peering.

![Selezionare una rete virtuale per il peering](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Intervallo di indirizzi

È disponibile anche un'opzione per fornire un **Intervallo di indirizzi** per le macchine virtuali per i lab.  La proprietà relativa all'**intervallo di indirizzi** si applica solo se per il lab è abilitata una **rete virtuale peer**. Se viene specificato l'intervallo di indirizzi, tutte le macchine virtuali nei lab dell'account lab verranno create in tale intervallo di indirizzi. L'intervallo di indirizzi deve essere specificato nella notazione CIDR, ad esempio 10.20.0.0/20, e non sovrapporsi ad altri intervalli di indirizzi esistenti.  Quando si specifica un intervallo di indirizzi, è importante considerare il numero di *lab* che verrà creato e fornire un intervallo di indirizzi appropriato. Lab Services presuppone un massimo di 512 macchine virtuali per lab.  Ad esempio, un intervallo IP con '/23' può creare solo un lab.  Un intervallo con '/21' consentirà la creazione di quattro lab.

Se l'**Intervallo di indirizzi** non viene specificato, Lab Services userà l'intervallo di indirizzi predefinito fornito da Azure durante la creazione della rete virtuale di cui eseguire il peering con la propria rete virtuale.  L'intervallo è spesso simile a 10.x.0.0/16.  Questo può comportare una sovrapposizione degli intervalli IP, quindi assicurarsi di specificare un intervallo di indirizzi nelle impostazioni del lab o controllare l'intervallo di indirizzi della rete virtuale di cui si sta eseguendo il peering.

> [!NOTE]
> La creazione del lab può non riuscire se l'account lab di cui viene eseguito il peering a una rete virtuale ha un intervallo di indirizzi IP troppo ristretto. È possibile esaurire lo spazio disponibile nell'intervallo di indirizzi se sono presenti troppi lab nell'account lab (ogni lab usa 512 indirizzi). 
> 
> Se la creazione del lab ha esito negativo, contattare il proprietario o l'amministratore dell'account lab e richiedere l'aumento dell'intervallo di indirizzi. L'amministratore può aumentare l'intervallo di indirizzi usando i passaggi indicati nella sezione [Specificare un intervallo di indirizzi per le macchine virtuali in un account lab](#specify-an-address-range-for-vms-in-the-lab-account). 

## <a name="configure-after-the-lab-account-is-created"></a>Eseguire la configurazione dopo la creazione dell'account lab

La stessa proprietà può essere abilitata nella scheda **Configurazione lab** della pagina **Account lab** se non è stata configurata una rete peer al momento della creazione dell'account lab. La modifica apportata a questa impostazione viene applicata solo ai lab creati dopo la modifica. Come si può vedere nell'immagine, è possibile abilitare o disabilitare la **rete virtuale peer** per i lab nell'account lab.

![Abilitare o disabilitare il peering di una rete virtuale dopo la creazione del lab](./media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Quando si seleziona una rete virtuale per il campo **Rete virtuale peer**, l'opzione **Consenti all'autore del lab di selezionare la località del lab** è disabilitata. Questo perché i lab nell'account lab devono trovarsi nella stessa area dell'account lab per connettersi alle risorse nella rete virtuale peer.

> [!IMPORTANT]
> L'impostazione della rete virtuale con peering viene applicata solo ai lab creati dopo l'esecuzione della modifica e non ai lab esistenti.


## <a name="specify-an-address-range-for-vms-in-the-lab-account"></a>Specificare un intervallo di indirizzi per le macchine virtuali nell'account lab
La procedura seguente include i passaggi per specificare un intervallo di indirizzi per le macchine virtuali nel lab. Se si aggiorna l'intervallo specificato in precedenza, l'intervallo di indirizzi modificato viene applicato solo alle macchine virtuali create dopo l'esecuzione della modifica. 

Di seguito sono riportate alcune restrizioni da tenere in considerazione quando si specifica l'intervallo di indirizzi. 

- Il prefisso deve essere minore o uguale a 23. 
- Se il peering di una rete virtuale viene eseguito all'account lab, l'intervallo di indirizzi specificato non può sovrapporsi all'intervallo di indirizzi della rete virtuale con peering.

1. Nella pagina **Account lab** selezionare **Impostazioni lab** nel menu a sinistra.
2. Per il campo **Intervallo di indirizzi** specificare l'intervallo di indirizzi per le macchine virtuali che verranno create nel lab. L'intervallo di indirizzi deve essere nella notazione CIDR (Classless Interdomain Routing). Ad esempio: 10.20.0.0/23. Le macchine virtuali nel lab verranno create in questo intervallo di indirizzi.
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Configurare un intervallo di indirizzi](./media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Consentire all'autore del lab di selezionare la località](allow-lab-creator-pick-lab-location.md)
- [Collegare una raccolta immagini condivisa a un lab](how-to-attach-detach-shared-image-gallery.md)
- [Aggiungere un utente come proprietario del lab](how-to-add-user-lab-owner.md)
- [Visualizzare le impostazioni del firewall di un lab](how-to-configure-firewall-settings.md)
- [Configurare altre impostazioni di un lab](how-to-configure-lab-accounts.md)
