---
title: 'Esercitazione: Creare un host Azure Bastion: VM Windows: portale'
description: Questo articolo illustra come creare un host Azure Bastion e connettersi a una VM Windows.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: daf3b6c36b191f52f0d7ac7638ef59695325f541
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078545"
---
# <a name="tutorial-create-an-azure-bastion-host-and-connect-to-a-windows-vm-through-a-browser"></a>Esercitazione: Creare un host Azure Bastion e connettersi a una VM Windows tramite un browser

Questa esercitazione illustra come connettersi a una macchina virtuale dal browser usando Azure Bastion e il portale di Azure. Nel portale di Azure si distribuisce Bastion nella rete virtuale. Dopo la distribuzione di Bastion, ci si connette a una macchina virtuale tramite il relativo indirizzo IP privato usando il portale di Azure. Per la VM non è necessario un indirizzo IP pubblico o un software speciale. Una volta effettuato il provisioning del servizio, l'esperienza RDP/SSH è disponibile per tutte le macchine virtuali nella stessa rete virtuale. Per altre informazioni su Azure Bastion, vedere [Che cos'è Azure Bastion?](bastion-overview.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un host bastion per la rete virtuale
> * Connettersi a una macchina virtuale Windows

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Una rete virtuale.
* Una macchina virtuale Windows nella rete virtuale.
* I ruoli obbligatori seguenti:
  * Ruolo Lettore nella macchina virtuale.
  * Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale.
  * Ruolo Lettore nella risorsa Azure Bastion.

* Porte: per connettersi alla VM Windows, devono essere aperte le porte seguenti nella VM:
  * Porte in ingresso: RDP (3389)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creare un host bastion

Questa sezione illustra come creare l'oggetto bastion nella rete virtuale. Questo oggetto è necessario per creare una connessione sicura a una VM nella rete virtuale.

1. Nella **Home page** selezionare **+ Crea una risorsa**.
1. Nella casella di ricerca della pagina **Nuovo** digitare **Bastion**, quindi premere **INVIO** per ottenere i risultati della ricerca. Nel risultato relativo a **Bastion** verificare che l'editore sia Microsoft.
1. Selezionare **Crea**.
1. Nella pagina **Crea un'istanza di Bastion** configurare una nuova risorsa Bastion.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Creare un host bastion" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Sottoscrizione** sottoscrizione di Azure che si vuole usare per creare una nuova risorsa Bastion.
    * **Gruppo di risorse**: gruppo di risorse di Azure in cui verrà creata la nuova risorsa Bastion. Se non esiste già un gruppo di risorse, è possibile crearne uno nuovo.
    * **Nome**: nome della nuova risorsa Bastion.
    * **Area**: l'area pubblica di Azure in cui verrà creata la risorsa.
    * **Rete virtuale**: rete virtuale in cui verrà creata la risorsa Bastion. È possibile creare una nuova rete virtuale nel portale durante questa procedura oppure usarne una esistente. Se si usa una rete virtuale esistente, assicurarsi che disponga di spazio indirizzi libero sufficiente per soddisfare i requisiti della subnet Bastion. Se la rete virtuale non è presente nell'elenco a discesa, verificare di aver selezionato il gruppo di risorse corretto.
    * **Subnet**: dopo aver creato o selezionato una rete virtuale, viene visualizzato il campo Subnet. Si tratta della subnet della rete virtuale in cui verrà distribuito il nuovo host bastion. La subnet sarà dedicata all'host bastion. Selezionare **Gestisci configurazione subnet** e creare la subnet di Azure Bastion. Selezionare **+Subnet** e creare una subnet seguendo queste indicazioni:

         * La subnet deve essere denominata **AzureBastionSubnet**.
         * La subnet deve essere /27 o superiore.

      Non è necessario compilare altri campi. Scegliere **OK** e quindi, nella parte superiore della pagina, selezionare **Crea un'istanza di Bastion** per tornare alla pagina di configurazione di Bastion.
    * **Indirizzo IP pubblico**: IP pubblico della risorsa Bastion per l'accesso a RDP/SSH (sulla porta 443). Creare un nuovo IP pubblico. L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando. Questo indirizzo IP non ha nulla a che fare con alcune delle VM a cui ci si vuole connettere. È l'IP pubblico della risorsa host bastion.
    * **Nome indirizzo IP pubblico**: nome della risorsa indirizzo IP pubblico. Per questa esercitazione è possibile lasciare l'impostazione predefinita.
    * **SKU indirizzo IP pubblico**: il valore predefinito **Standard** è già inserito. Azure Bastion usa/supporta solo lo SKU indirizzo IP pubblico Standard.
    * **Assegnazione**: il valore predefinito **Statica** è già inserito.

1. Dopo aver finito di specificare le impostazioni, selezionare **Rivedi e crea**. I valori vengono convalidati. Superata la convalida, è possibile creare la risorsa Bastion.
1. Selezionare **Crea**.
1. Verrà visualizzato un messaggio che informa che la distribuzione è in corso. Lo stato verrà visualizzato in questa pagina man mano che le risorse vengono create. La creazione e la distribuzione della risorsa Bastion richiedono circa 5 minuti.

## <a name="connect-to-a-vm"></a>Connettersi a una macchina virtuale

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di continuare a usare questa applicazione, eliminare le risorse seguendo questa procedura:

1. Immettere il nome del gruppo di risorse nella casella **Cerca** nella parte superiore del portale. Quando viene visualizzato il gruppo di risorse nei risultati della ricerca, selezionarlo.
1. Selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un host bastion e lo si è associato a una rete virtuale, quindi ci si è connessi a una VM Windows. È possibile scegliere di usare i gruppi di sicurezza di rete con la subnet di Azure Bastion. A questo scopo, vedere:

> [!div class="nextstepaction"]
> [Uso dei gruppi di sicurezza di rete](bastion-nsg.md)
