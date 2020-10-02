---
title: 'Avvio rapido: Connettersi a una macchina virtuale usando un indirizzo IP privato: Azure Bastion'
description: Questo articolo illustra come creare un host bastion di Azure da una macchina virtuale e connettersi in modo sicuro usando un indirizzo IP privato.
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: b0155ae92e3179918273d6a19773aa15b67949ea
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985590"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Avvio rapido: Connettersi a una macchina virtuale usando un indirizzo IP privato e Azure Bastion

Questo articolo di avvio rapido mostra come connettersi a una macchina virtuale usando un indirizzo IP privato. Quando ci si connette tramite Bastion, le macchine virtuali non necessitano di un indirizzo IP pubblico. I passaggi descritti in questo articolo consentono di distribuire Bastion nella rete virtuale tramite la macchina virtuale nel portale. Una volta effettuato il provisioning del servizio, l'esperienza RDP/SSH è disponibile per tutte le macchine virtuali nella stessa rete virtuale.

## <a name="prerequisites"></a><a name="prereq"></a>Prerequisiti

* Una rete virtuale di Azure.
* Una macchina virtuale di Azure all'interno della rete virtuale con la porta 3389 aperta.

### <a name="example-values"></a>Valori di esempio

|**Nome** | **Valore** |
| --- | --- |
| Nome |  VNet1Bastion |
| Region | eastus |
| Rete virtuale |  VNet1 |
| + Nome subnet | AzureBastionSubnet |
| Indirizzi della subnet AzureBastionSubnet |  10.1.254.0/27 |
| Indirizzo IP pubblico |  Creare un nuovo gruppo di risorse |
| Nome dell'indirizzo IP pubblico | VNet1BastionPIP  |
| SKU indirizzo IP pubblico |  Standard  |
| Assegnazione  | Statico |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Creare un host bastion

Quando si crea un host bastion nel portale usando una macchina virtuale esistente, i valori predefiniti di varie impostazioni corrisponderanno automaticamente a quelli della macchina virtuale e/o della rete virtuale.

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale e quindi fare clic su **Connetti**.

   ![impostazioni della macchina virtuale](./media/quickstart-host-portal/vm-settings.png)
1. Nell'elenco a discesa selezionare **Bastion**.
1. Nella pagina Connetti selezionare **Usa Bastion**.

   ![selezionare Bastion](./media/quickstart-host-portal/select-bastion.png)

1. Nella pagina Bastion compilare i campi delle impostazioni seguenti:

   * **Name**: nome dell'host bastion
   * **Subnet**: subnet all'interno della rete virtuale in cui verrà distribuita la risorsa Bastion. È necessario creare la subnet con il nome **AzureBastionSubnet**. Il nome indica ad Azure la subnet in cui distribuire la risorsa Bastion. Questa subnet è diversa rispetto a una subnet del gateway. Usare una subnet con dimensioni di almeno /27 o superiori (/27, /26, /25 e così via).
   
      * Selezionare **Gestisci configurazione subnet** e quindi fare clic su **+ subnet**.
      * Nella pagina Aggiungi subnet digitare **AzureBastionSubnet**.
      * Specificare l'intervallo di indirizzi nella notazione CIDR, ad esempio 10.1.254.0/27.
      * Selezionare **OK** per creare la subnet. Nella parte superiore della pagina tornare a Bastion per completare le altre impostazioni.

         ![passare alle impostazioni di Bastion](./media/quickstart-host-portal/navigate-bastion.png)
   * **Indirizzo IP pubblico**: si tratta dell'indirizzo IP pubblico della risorsa Bastion per l'accesso a RDP/SSH (sulla porta 443). Creare un nuovo indirizzo IP pubblico o usarne uno esistente. L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.
   * **Nome indirizzo IP pubblico**: nome della risorsa indirizzo IP pubblico.
1. Nella schermata di convalida fare clic su **Crea**. Attendere circa 5 minuti per la creazione e la distribuzione della risorsa Bastion.

   ![creare l'host bastion](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Connettersi

Al termine della distribuzione della risorsa Bastion nella rete virtuale, la schermata passa alla pagina di connessione.

1. Digitare il nome utente e la password per la macchina virtuale. Selezionare **Connect** (Connetti).

   ![Screenshot della finestra di dialogo Connetti con Azure Bastion, che chiede di immettere un nome utente e una password.](./media/quickstart-host-portal/connect.png)
1. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

   ![Connessione RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando la rete virtuale e le macchine virtuali non sono più necessarie, rimuovere il gruppo di risorse e tutte le risorse al suo interno:

1. Immettere *TestRG1* nella casella **Cerca** nella parte superiore del portale e selezionare **TestRG1** nei risultati della ricerca.

2. Selezionare **Elimina gruppo di risorse**.

3. Immettere *TestRG1* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un host bastion per la rete virtuale ed è stata stabilita una connessione sicura a una macchina virtuale tramite l'host bastion.

* Per altre informazioni su Azure Bastion, vedere la [Panoramica di Bastion](bastion-overview.md) e le [Domande frequenti su Bastion](bastion-faq.md).
* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi di sicurezza di rete](bastion-nsg.md).
* Per istruzioni contenenti le spiegazioni delle impostazioni dell'host Azure Bastion, vedere l'[esercitazione](bastion-create-host-portal.md).
* Per connettersi a un set di scalabilità di macchine virtuali, vedere [Connettersi a un set di scalabilità di macchine virtuali con Azure Bastion](bastion-connect-vm-scale-set.md).