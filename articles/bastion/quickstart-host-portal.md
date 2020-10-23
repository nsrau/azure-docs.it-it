---
title: 'Avvio rapido: Creare un host bastion da una VM e connettersi tramite indirizzo IP privato'
titleSuffix: Azure Bastion
description: Questo articolo di avvio rapido illustra come creare un host bastion di Azure da una macchina virtuale e connettersi in modo sicuro usando un indirizzo IP privato.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019053"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Avvio rapido: Connettersi a una macchina virtuale usando un indirizzo IP privato e Azure Bastion

Questo articolo di avvio rapido illustra come connettersi a una macchina virtuale tramite il browser usando Azure Bastion e il portale di Azure. Nel portale di Azure è possibile distribuire Bastion dalla VM di Azure alla rete virtuale. Dopo la distribuzione di Bastion, è possibile connettersi alla VM tramite il relativo indirizzo IP privato usando il portale di Azure. Per la VM non è necessario un indirizzo IP pubblico o un software speciale. Uno dei vantaggi della creazione di un host bastion per la rete virtuale direttamente dalla VM è che molte impostazioni vengono prepopolate automaticamente.

Una volta effettuato il provisioning del servizio, l'esperienza RDP/SSH è disponibile per tutte le macchine virtuali nella stessa rete virtuale. Per altre informazioni su Azure Bastion, vedere [Che cos'è Azure Bastion?](bastion-overview.md)

## <a name="prerequisites"></a><a name="prereq"></a>Prerequisiti

* Una rete virtuale.
* Una macchina virtuale Windows nella rete virtuale.
* I ruoli obbligatori seguenti:
  * Ruolo Lettore nella macchina virtuale.
  * Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale.

* Porte: per connettersi alla VM, devono essere aperte le porte seguenti nella VM:
  * Porte in ingresso: RDP (3389)

### <a name="example-values"></a>Valori di esempio

|**Nome** | **Valore** |
| --- | --- |
| Nome |  TestVNet1-bastion |
| Rete virtuale |  TestVNet1 (basata sulla VM) |
| + Nome subnet | AzureBastionSubnet |
| Indirizzi della subnet AzureBastionSubnet |  10.1.254.0/27 |
| Indirizzo IP pubblico |  Creare un nuovo gruppo di risorse |
| Nome dell'indirizzo IP pubblico | VNet1BastionPIP  |
| SKU indirizzo IP pubblico |  Standard  |
| Assegnazione  | Statico |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Creare un host bastion

Quando si crea un host bastion nel portale di Azure usando una macchina virtuale esistente, i valori predefiniti di varie impostazioni corrisponderanno automaticamente a quelli della macchina virtuale e/o della rete virtuale.

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale e quindi selezionare **Connetti**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="impostazioni della macchina virtuale" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Nell'elenco a discesa selezionare **Bastion**.
1. Nella pagina **TestVM | Connetti** selezionare **Usa Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="impostazioni della macchina virtuale" border="false":::

1. Nella pagina **Bastion** compilare i campi delle impostazioni seguenti:

   * **Nome**: nome dell'host bastion.
   * **Subnet**: subnet all'interno della rete virtuale in cui verrà distribuita la risorsa Bastion. È necessario creare la subnet con il nome **AzureBastionSubnet**. Il nome indica ad Azure la subnet in cui distribuire la risorsa Bastion. Questa subnet è diversa rispetto a una subnet del gateway. Usare una subnet con dimensioni di almeno /27 o superiori (/27, /26, /25 e così via).
   
      * Selezionare **Gestisci configurazione subnet**.
      * Selezionare **AzureBastionSubnet**.
      * Se necessario, modificare l'intervallo di indirizzi specificando la notazione CIDR, ad esempio 10.1.254.0/27.
      * Non modificare altre impostazioni. Selezionare **OK** per accettare e salvare le modifiche della subnet oppure selezionare **x** nella parte superiore della pagina se non si vogliono apportare modifiche.
1. Fare clic sul pulsante Indietro del browser per tornare nella pagina **Bastion** e continuare a specificare valori.
   * **Nome indirizzo IP pubblico**: nome della risorsa indirizzo IP pubblico.
   * **Indirizzo IP pubblico**: si tratta dell'indirizzo IP pubblico della risorsa Bastion per l'accesso a RDP/SSH (sulla porta 443). Creare un nuovo IP pubblico.
1. Selezionare **Crea** per creare l'host bastion. Azure convalida le impostazioni e quindi crea l'host. Per la creazione e la distribuzione dell'host e delle relative risorse sono necessari circa 5 minuti.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="impostazioni della macchina virtuale":::

## <a name="connect"></a><a name="connect"></a>Connettersi

Al termine della distribuzione della risorsa Bastion nella rete virtuale, la schermata passa alla pagina di connessione.

1. Digitare il nome utente e la password per la macchina virtuale. Selezionare **Connect** (Connetti).

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="impostazioni della macchina virtuale":::
1. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="impostazioni della macchina virtuale":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando la rete virtuale e le macchine virtuali non sono più necessarie, rimuovere il gruppo di risorse e tutte le risorse al suo interno:

1. Immettere il nome del gruppo di risorse nella casella **Cerca** nella parte superiore del portale e selezionarlo nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere il nome del gruppo di risorse in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un host bastion per la rete virtuale ed è stata stabilita una connessione sicura a una macchina virtuale tramite l'host bastion. Per connettersi a un set di scalabilità di macchine virtuali, continuare con il passaggio successivo.

> [!div class="nextstepaction"]
> [Connettersi a un set di scalabilità di macchine virtuali tramite Azure Bastion](bastion-connect-vm-scale-set.md)
