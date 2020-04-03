---
title: 'Guida introduttiva: Connettersi a una macchina virtuale usando un indirizzo IP privato: Azure Bastion'
description: In questo articolo viene illustrato come creare un host di Azure Bastion da una macchina virtuale e connettersi in modo sicuro usando un indirizzo IP privato.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619262"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Guida introduttiva: Connettersi a una macchina virtuale usando un indirizzo IP privato e Un Bastion di AzureQuickstart: Connect to a virtual machine using a private IP address and Azure Bastion

Questo articolo della Guida introduttiva illustra come connettersi a una macchina virtuale usando un indirizzo IP privato. Quando ci si connette tramite Bastion, le macchine virtuali non necessitano di un indirizzo IP pubblico. I passaggi descritti in questo articolo consentono di distribuire Bastion nella rete virtuale tramite la macchina virtuale nel portale. Dopo il provisioning del servizio, l'esperienza RDP/SSH è disponibile per tutte le macchine virtuali nella stessa rete virtuale.

## <a name="prerequisites"></a><a name="prereq"></a>Prerequisiti

* Una rete virtuale di Azure.
* Una macchina virtuale di Azure situata nella rete virtuale con la porta 3389 aperta.

### <a name="example-values"></a>Valori di esempio

|**Nome** | **Valore** |
| --- | --- |
| Nome |  VNet1Bastion |
| Region | eastus |
| Rete virtuale |  VNet1 |
| Nome subnet | AzureBastionSubnet |
| Indirizzi di AzureBastionSubnetAzureBastionSubnet addresses |  10.1.254.0/27 |
| Indirizzo IP pubblico |  Creare un nuovo gruppo di risorse |
| Nome dell'indirizzo IP pubblico | VNet1BastionPIP  |
| SKU indirizzo IP pubblico |  Standard  |
| Assegnazione  | Statico |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Creare un host Bastion

Quando si crea un bastion host nel portale utilizzando una macchina virtuale esistente, per impostazione predefinita varie impostazioni corrisponderà alla macchina virtuale e/o alla rete virtuale.

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale, quindi fare clic su **Connetti**.

   ![impostazioni della macchina virtuale](./media/quickstart-host-portal/vm-settings.png)
1. Dall'elenco a discesa, selezionare **Bastione**.
1. Nella pagina Connetti selezionare **Usa Bastione**.

   ![selezionare Bastione](./media/quickstart-host-portal/select-bastion.png)

1. Nella pagina Bastione compilare i seguenti campi delle impostazioni:

   * **Nome**: Nome dell'host bastion
   * **Subnet:** subnet all'interno della rete virtuale in cui verrà distribuita la risorsa Bastion.Subnet : The subnet inside your virtual network to which Bastion resource will be deployed. La subnet deve essere creata con il nome **AzureBastionSubnet**. Il nome consente ad Azure di sapere in quale subnet distribuire la risorsa Bastion. Questo è diverso da una subnet del gateway. Utilizzare una subnet di almeno /27 o superiore (/27, /26, /25 e così via).
   
      * Selezionare **Gestisci configurazione subnet**, quindi selezionare **Subnet**.
      * Nella pagina Aggiungi subnet digitare **AzureBastionSubnet**.
      * Specificare l'intervallo di indirizzi in notazione CIDR. Ad esempio, 10.1.254.0/27.
      * Selezionare **OK** per creare la subnet. Nella parte superiore della pagina, torna al Bastione per completare le altre impostazioni.

         ![passare alle impostazioni bastion](./media/quickstart-host-portal/navigate-bastion.png)
   * **Indirizzo IP pubblico**: Si tratta dell'indirizzo IP pubblico della risorsa Bastion su cui si accederà a RDP/SSH (tramite la porta 443). Creare un nuovo indirizzo IP pubblico o utilizzarne uno esistente. L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.
   * **Nome indirizzo IP pubblico**: il nome della risorsa indirizzo IP pubblico.
1. Nella schermata di convalida, fare clic su **Crea**. Attendere circa 5 minuti per la risorsa Bastion creare e distribuire.

   ![creare bastion host](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>connettersi

Dopo che Bastion è stato distribuito nella rete virtuale, lo schermo passa alla pagina di connessione.

1. Digitare il nome utente e la password per la macchina virtuale. Selezionare **Connect** (Connetti).

   ![connessione](./media/quickstart-host-portal/connect.png)
1. La connessione RDP a questa macchina virtuale tramite Bastion verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

   ![Connessione RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'uso della rete virtuale e delle macchine virtuali, eliminare il gruppo di risorse e tutte le risorse in esso contenute:When you're done using the virtual network and the virtual machines, delete the resource group and all of the resources it contains:

1. Immettere *TestRG1* nella casella **di ricerca** nella parte superiore del portale e selezionare **TestRG1** nei risultati della ricerca.

2. Selezionare **Elimina gruppo di risorse**.

3. Immettere *TestRG1* per **TYPE THE RESOURCE GROUP NAME** e selezionare **Delete**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un host Bastion per la rete virtuale e quindi si è connessi a una macchina virtuale in modo sicuro tramite l'host Bastion.

* Per altre informazioni su Azure Bastion, leggere Panoramica dei [Bastion](bastion-overview.md) e Domande frequenti su [Bastion](bastion-faq.md).
* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi](bastion-nsg.md)di sicurezza di rete.
* Per istruzioni contenenti spiegazioni sulle impostazioni dell'host Bastion di Azure, vedere [l'esercitazione](bastion-create-host-portal.md).
* Per connettersi a un set di scalabilità di macchine virtuali, vedere Connettersi a un set di scalabilità di [macchine virtuali tramite Azure Bastion.](bastion-connect-vm-scale-set.md)