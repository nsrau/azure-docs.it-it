---
title: Creare un host Bastion di Azure | Microsoft Docs
description: Questo articolo illustra come creare un host Bastion di Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 4c283e840208ab9f53d084ca17221ea2c835b794
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814148"
---
# <a name="create-an-azure-bastion-host-preview"></a>Creare un host Bastion di Azure (anteprima)

Questo articolo illustra come creare un host Bastion di Azure. Dopo aver effettuato il provisioning del servizio Azure Bastion nella rete virtuale, l'esperienza RDP/SSH trasparente è disponibile per tutte le macchine virtuali nella stessa rete virtuale. Questa distribuzione viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

È possibile creare una risorsa host Bastion in due modi:

* Creare una risorsa Bastion usando il portale di Azure.
* Creare una risorsa Bastion nel portale di Azure usando le impostazioni della macchina virtuale esistente.

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

L'anteprima pubblica è limitata alle aree pubbliche di Azure seguenti:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Creare un host Bastion

Questa sezione consente di creare una nuova risorsa di Azure Bastion dalla portale di Azure.

1. Dal home page nell' [anteprima portale di Azure-Bastion](https://aka.ms/BastionHost), fare clic su **+ Crea una risorsa**. Assicurarsi di usare il collegamento fornito per accedere al portale per questa anteprima, non per l'portale di Azure regolare.

1. Nella **nuova** pagina, nel campo *Cerca nel Marketplace* digitare **Bastion**, quindi premere **invio** per ottenere i risultati della ricerca.

1. Nei risultati fare clic su **Bastion (anteprima)** . Verificare che il server di pubblicazione sia *Microsoft* e che la categoria sia di *rete*.

1. Nella pagina **Bastion (anteprima)** fare clic su **Crea** per aprire la pagina **creare un Bastion** .

1. Nella pagina **creare un Bastion** configurare una nuova risorsa Bastion. Specificare le impostazioni di configurazione per la risorsa Bastion.

    ![creare un Bastion](./media/bastion-create-host-portal/settings.png)

    * **Sottoscrizione** Sottoscrizione di Azure che si vuole usare per creare una nuova risorsa Bastion.
    * **Gruppo di risorse**: Gruppo di risorse di Azure in cui verrà creata la nuova risorsa Bastion. Se non si dispone di un gruppo di risorse esistente, è possibile crearne uno nuovo.
    * **Nome**: Nome della nuova risorsa Bastion
    * **Area**: Area pubblica di Azure in cui verrà creata la risorsa.
    * **Rete virtuale**: Rete virtuale in cui verrà creata la risorsa Bastion. È possibile creare una nuova rete virtuale nel portale durante questo processo, nel caso in cui non si disponga o non si desideri usare una rete virtuale esistente. Se si usa una rete virtuale esistente, assicurarsi che la rete virtuale esistente disponga di spazio di indirizzi libero sufficiente per soddisfare i requisiti della subnet Bastion.
    * **Subnet**: La subnet nella rete virtuale in cui verrà distribuita la nuova risorsa host Bastion. È necessario creare una subnet usando il valore nome **AzureBastionSubnet**. Questo valore consente ad Azure di stabilire la subnet in cui distribuire le risorse Bastion. Questa operazione è diversa rispetto a una subnet del gateway. È necessario usare una subnet di almeno una subnet/27 o più grande (/27,/26 e così via). Creare **AzureBastionSubnet** senza alcuna tabella o delega di route. Quando si usano i gruppi di sicurezza di rete in **AzureBastionSubnet**, vedere [lavorare con gruppi](bastion-nsg.md).
    * **Indirizzo IP pubblico**: Indirizzo IP pubblico della risorsa Bastion a cui verrà eseguito l'accesso a RDP/SSH (sulla porta 443). Creare un nuovo indirizzo IP pubblico o utilizzarne uno esistente. L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.
    * **Nome indirizzo IP pubblico**: Nome della risorsa indirizzo IP pubblico.
    * **SKU indirizzo IP pubblico**: Prepopolato per impostazione predefinita in **standard**. Azure Bastion USA/supporta solo lo SKU IP pubblico standard.
    * **Assegnazione**: Prepopolato per impostazione predefinita in **static**.

1. Al termine della specifica delle impostazioni, fare clic su **Verifica + crea**. Vengono convalidati i valori. Al termine della convalida, è possibile avviare il processo di creazione.
1. Nella pagina Crea Bastion fare clic su **Crea**.
1. Verrà visualizzato un messaggio che informa che la distribuzione è in corso. Lo stato verrà visualizzato in questa pagina durante la creazione delle risorse. Per la creazione e la distribuzione della risorsa Bastion sono necessari circa 5 minuti.

## <a name="createvmset"></a>Creare un host Bastion usando le impostazioni della macchina virtuale

Se si crea un host Bastion nel portale usando una macchina virtuale esistente, le impostazioni predefinite di diverse impostazioni verranno automaticamente corrispondenti alla macchina virtuale e/o alla rete virtuale.

1. Nell' [anteprima portale di Azure-Bastion](https://aka.ms/BastionHost)passare alla macchina virtuale e quindi fare clic su **Connetti**.

    ![Connessione VM](./media/bastion-create-host-portal/vmsettings.png)

1. Sulla barra laterale destra fare clic su **Bastion**, quindi **usare Bastion**.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. Nella pagina Bastion compilare i campi delle impostazioni seguenti:

    * **Nome**: Nome dell'host Bastion che si vuole creare.
    * **Subnet**: La subnet all'interno della rete virtuale in cui verrà distribuita la risorsa Bastion. La subnet deve essere creata con il nome **AzureBastionSubnet**. Ciò consente ad Azure di individuare la subnet in cui distribuire la risorsa bastione. Questa operazione è diversa rispetto a una subnet del gateway. Fare clic su **Gestisci configurazione subnet** per creare la subnet di Azure Bastion. Si consiglia vivamente di utilizzare almeno una subnet/27 o più grande (/27,/26 e così via). Creare **AzureBastionSubnet** senza gruppi di sicurezza di rete, tabelle di route o deleghe. Fare clic su **Crea** per creare la subnet, quindi procedere con le impostazioni successive.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Indirizzo IP pubblico**: Indirizzo IP pubblico della risorsa Bastion a cui verrà eseguito l'accesso a RDP/SSH (sulla porta 443). Creare un nuovo indirizzo IP pubblico o utilizzarne uno esistente. L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.
    * **Nome indirizzo IP pubblico**: Nome della risorsa indirizzo IP pubblico.
1. Nella schermata convalida fare clic su **Crea**. Attendere circa 5 minuti per la creazione e la distribuzione della risorsa Bastion.

## <a name="next-steps"></a>Passaggi successivi

Leggi le [domande frequenti sul Bastion](bastion-faq.md)
