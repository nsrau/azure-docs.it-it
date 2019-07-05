---
title: Creare un host di Azure Bastion | Microsoft Docs
description: In questo articolo, informazioni su come creare un Azure Bastion host
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4a52383e6ab24c6ae1e2be0b67293d65dfa04466
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477872"
---
# <a name="create-an-azure-bastion-host-preview"></a>Creare un host di Azure Bastion (anteprima)

Questo articolo illustra come creare un Azure Bastion host. Dopo il provisioning del servizio di Azure Bastion in una rete virtuale, l'esperienza senza problemi di RDP/SSH è disponibile per tutte le macchine virtuali nella stessa rete virtuale. Questa distribuzione viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

Esistono due modi in cui è possibile creare una risorsa dell'host Bastion:

* Creare una risorsa Bastion usando il portale di Azure.
* Creare una risorsa Bastion nel portale di Azure usando le impostazioni della macchina virtuale esistente.

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

L'anteprima pubblica è limitato per le aree pubbliche di Azure seguenti:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Creare un bastion host

In questa sezione consente di creare una nuova risorsa di Azure Bastion dal portale di Azure.

1. Dalla home page nel [portale di Azure - anteprima Bastion](https://aka.ms/BastionHost), fare clic su **+ crea una risorsa**. Assicurarsi di che usare il collegamento fornito per accedere al portale per questa versione di anteprima, non dal portale di Azure regolare.

1. Nel **New** pagina il *Cerca nel Marketplace* digitare **Bastion**, quindi fare clic su **invio** per ottenere i risultati della ricerca.

1. Dai risultati, fare clic su **Bastion (anteprima)** . Assicurarsi che il server di pubblicazione *Microsoft* ed è la categoria *Networking*.

1. Nel **Bastion (anteprima)** pagina, fare clic su **Create** per aprire il **creare un bastion** pagina.

1. Nel **creare un bastion** pagina, configurare una nuova risorsa di Bastion. Specificare le impostazioni di configurazione per la risorsa di Bastion.

    ![creare un bastion](./media/bastion-create-host-portal/settings.png)

    * **Sottoscrizione** Sottoscrizione di Azure da usare per creare una nuova risorsa di Bastion.
    * **Gruppo di risorse**: Il gruppo di risorse di Azure in cui verrà creata la nuova risorsa Bastion in. Se non si dispone di un gruppo di risorse esistente, è possibile creare uno nuovo.
    * **Nome**: Il nome della nuova risorsa Bastion
    * **Area**: L'area pubblica di Azure che verrà creata la risorsa in.
    * **Rete virtuale**: La rete virtuale in cui verrà creata la risorsa Bastion in. È possibile creare una nuova rete virtuale nel portale durante questo processo, nel caso in cui si dispone o non vuole usare una rete virtuale esistente. Se si usa una rete virtuale esistente, assicurarsi che la rete virtuale esistente con uno spazio indirizzi disponibile sufficiente per soddisfare i requisiti di subnet Bastion.
    * **Subnet**: La subnet nella rete virtuale a cui verrà distribuita la nuova risorsa di Bastion host. È necessario creare una subnet usando il valore del nome **AzureBastionSubnet**. Questo valore informa Azure che la subnet in cui distribuire le risorse Bastion. Questo è diverso rispetto a una subnet del Gateway. È consigliabile usare almeno/27 o più grande (/ 27, / 26 e così via). Creare il **AzureBastionSubnet** senza eventuali gruppi di sicurezza di rete, instradare le tabelle o le deleghe.
    * **Indirizzo IP pubblico**: L'indirizzo IP pubblico della risorsa Bastion in cui si accederà RDP o SSH (sulla porta 443). Creare un nuovo indirizzo IP pubblico o usarne uno esistente. L'indirizzo IP pubblico deve essere nella stessa area come risorsa di Bastion a cui che si sta creando.
    * **Nome indirizzo IP pubblico**: Il nome della risorsa indirizzo IP pubblico.
    * **Indirizzo IP pubblico dello SKU**: Pre-popolato per impostazione predefinita per **Standard**.
    * **Assegnazione**: Pre-popolato per impostazione predefinita per **statici**.

1. Dopo aver specificato le impostazioni, fare clic su **revisione + Crea**. Questa convalida i valori. Al termine della convalida, è possibile iniziare il processo di creazione.
1. In una pagina bastion il Create, fare clic su **Create**.
1. Verrà visualizzato un messaggio che informa che la distribuzione è in corso. Stato verrà visualizzato in questa pagina perché vengono create le risorse. Bastano circa 5 minuti per la risorsa Bastion venga creato e distribuito.

## <a name="createvmset"></a>Creare un bastion host usando le impostazioni della macchina virtuale

Se si crea un bastion host nel portale usando una macchina virtuale esistente, le varie impostazioni imposterà automaticamente corrispondente per la macchina virtuale e/o rete virtuale.

1. Nel [portale di Azure - anteprima Bastion](https://aka.ms/BastionHost), passare alla macchina virtuale, quindi fare clic su **Connect**.

    ![Connessione macchina virtuale](./media/bastion-create-host-portal/vmsettings.png)

1. Nella barra laterale destra, fare clic su **Bastion**, quindi **utilizzare Bastion**.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. Nella pagina Bastion, compilare i campi delle impostazioni seguenti:

    * **Nome**: Il nome dell'host bastion a cui si desidera creare.
    * **Subnet**: La subnet all'interno della rete virtuale a cui Bastion verranno distribuiti. La subnet deve essere creata con il nome **AzureBastionSubnet**. In questo modo Azure conoscere la subnet in cui distribuire la risorsa Bastion. Questo è diverso rispetto a una subnet del Gateway. Fare clic su **Gestione configurazione di subnet** per creare la Subnet di Azure Bastion. È consigliabile usare almeno/27 o più grande (/ 27, / 26, ecc.). Creare il **AzureBastionSubnet** senza eventuali gruppi di sicurezza di rete, instradare le tabelle o le deleghe. Fare clic su **Create** per creare la subnet, quindi procedere con le impostazioni di Avanti.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Indirizzo IP pubblico**: L'indirizzo IP pubblico della risorsa Bastion in cui si accederà RDP o SSH (sulla porta 443). Creare un nuovo indirizzo IP pubblico o usarne uno esistente. L'indirizzo IP pubblico deve essere nella stessa area come risorsa di Bastion a cui che si sta creando.
    * **Nome indirizzo IP pubblico**: Il nome della risorsa indirizzo IP pubblico.
1. Nella schermata di convalida, fare clic su **Create**. Attendere circa 5 minuti per la risorsa Bastion venga creato e distribuito.

## <a name="next-steps"></a>Passaggi successivi

Lettura di [Bastion domande frequenti](bastion-faq.md)
