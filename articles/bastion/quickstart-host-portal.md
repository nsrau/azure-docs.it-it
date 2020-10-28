---
title: 'Avvio rapido: Configurare Azure Bastion e connettersi a una VM tramite un indirizzo IP privato e un browser'
titleSuffix: Azure Bastion
description: Questo articolo di avvio rapido illustra come creare un host di Azure Bastion da una macchina virtuale e connettersi alla VM in modo sicuro tramite un browser e un indirizzo IP privato.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150432"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Avvio rapido: Connettersi a una VM in modo sicuro tramite un browser e un indirizzo IP privato

È possibile connettersi a una macchina virtuale (VM) tramite il browser usando il portale di Azure e Azure Bastion. Questo articolo di avvio rapido illustra come configurare Azure Bastion in base alle impostazioni della VM e quindi connettersi alla VM tramite il portale. La VM non deve necessariamente avere un indirizzo IP pubblico, il software client, un agente o una configurazione speciale. Una volta effettuato il provisioning del servizio, l'esperienza RDP/SSH è disponibile per tutte le macchine virtuali nella stessa rete virtuale. Per altre informazioni su Azure Bastion, vedere [Che cos'è Azure Bastion?](bastion-overview.md)

## <a name="prerequisites"></a><a name="prereq"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non è disponibile, [crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Per potersi connettere a una VM tramite il browser con Bastion, è necessario essere in grado di accedere al portale di Azure.

* Una macchina virtuale Windows in una rete virtuale. Se non si ha una macchina virtuale, crearne una come descritto in [Avvio rapido: Creare una macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

  * Se necessario, vedere i [valori di esempio](#values) disponibili.
  * Se si ha già una rete virtuale, assicurarsi di selezionarla nella scheda Rete quando si crea la VM.
  * Se non si ha una rete virtuale, è possibile crearne una contemporaneamente alla VM.
  * Non è necessario avere un indirizzo IP pubblico per connettersi alla VM tramite Azure Bastion.

* Ruoli VM richiesti:
  * Ruolo Lettore nella macchina virtuale.
  * Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale.
  
* Porte della VM richieste:
  * Porte in ingresso: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Valori di esempio

Quando si crea questa configurazione, è possibile usare i valori di esempio seguenti oppure è possibile sostituirli con altri personalizzati.

**Valori di base per la rete virtuale e la VM:**

|**Nome** | **Valore** |
| --- | --- |
| Macchina virtuale| TestVM |
| Resource group | TestRG |
| Area | Stati Uniti orientali |
| Rete virtuale | TestVNet1 |
| Spazio degli indirizzi | 10.0.0.0/16 |
| Subnet | FrontEnd: 10.0.0.0/24 |

**Valori di Azure Bastion:**

|**Nome** | **Valore** |
| --- | --- |
| Nome | TestVNet1-bastion |
| + Nome subnet | AzureBastionSubnet |
| Indirizzi della subnet AzureBastionSubnet | Una subnet con lo spazio indirizzi della rete virtuale con una subnet mask /27, ad esempio 10.0.1.0/27.  |
| Indirizzo IP pubblico |  Creare un nuovo gruppo di risorse |
| Nome dell'indirizzo IP pubblico | VNet1BastionPIP  |
| SKU indirizzo IP pubblico |  Standard  |
| Assegnazione  | Statico |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Creare un host bastion

È possibile configurare un host bastion in vari modi diversi. Nei passaggi seguenti si creerà un host bastion nel portale di Azure direttamente dalla macchina virtuale. Quando si crea un host da una macchina virtuale, le varie impostazioni verranno popolate automaticamente in base alla macchina virtuale e/o alla rete virtuale.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla macchina virtuale a cui connettersi e selezionare **Connetti** .

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="impostazioni della macchina virtuale" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Nell'elenco a discesa selezionare **Bastion** .
1. Nella pagina **TestVM | Connetti** selezionare **Usa Bastion** .

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="impostazioni della macchina virtuale" border="false":::

1. Nella pagina **Bastion** compilare i campi delle impostazioni seguenti:

   * **Nome** : nome dell'host bastion.
   * **Subnet** : corrisponde allo spazio indirizzi della rete virtuale in cui verrà distribuita la risorsa Bastion. È necessario creare la subnet con il nome **AzureBastionSubnet** . Usare una subnet con dimensioni di almeno /27 o superiori (/27, /26, /25 e così via).
   * Selezionare **Gestisci configurazione subnet** .
1. Nella pagina **Subnet** selezionare **+Subnet** .

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="impostazioni della macchina virtuale":::
    
1. Nella pagina **Aggiungi** subnet digitare **AzureBastionSubnet** per **Nome** .
   * Per l'intervallo di indirizzi della subnet, scegliere un indirizzo incluso nello spazio indirizzi della rete virtuale.
   * Non modificare altre impostazioni. Selezionare **OK** per accettare e salvare le modifiche della subnet.

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="impostazioni della macchina virtuale":::
1. Fare clic sul pulsante Indietro del browser per tornare nella pagina **Bastion** e continuare a specificare valori.
   * **Indirizzo IP pubblico** : lasciare **Crea nuovo** .
   * **Nome indirizzo IP pubblico** : nome della risorsa indirizzo IP pubblico.
   * **Assegnazione** : l'impostazione predefinita è Statico. Non è possibile usare un'assegnazione dinamica per Azure Bastion.
   * **Gruppo di risorse** : lo stesso gruppo di risorse della VM.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="impostazioni della macchina virtuale":::
1. Selezionare **Crea** per creare l'host bastion. Azure convalida le impostazioni e quindi crea l'host. Per la creazione e la distribuzione dell'host e delle relative risorse sono necessari circa 5 minuti.

## <a name="connect"></a><a name="connect"></a>Connettersi

Al termine della distribuzione della risorsa Bastion nella rete virtuale, la schermata passa alla pagina di connessione.

1. Digitare il nome utente e la password per la macchina virtuale. Selezionare **Connect** (Connetti).

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="impostazioni della macchina virtuale":::
1. La connessione RDP a questa macchina virtuale verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="impostazioni della macchina virtuale":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando la rete virtuale e le macchine virtuali non sono più necessarie, rimuovere il gruppo di risorse e tutte le risorse al suo interno:

1. Immettere il nome del gruppo di risorse nella casella **Cerca** nella parte superiore del portale e selezionarlo nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse** .

1. Immettere il nome del gruppo di risorse in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina** .

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un host bastion per la rete virtuale ed è stata stabilita una connessione sicura a una macchina virtuale tramite Bastion. Per connettersi a un set di scalabilità di macchine virtuali, continuare con il passaggio successivo.

> [!div class="nextstepaction"]
> [Connettersi a un set di scalabilità di macchine virtuali tramite Azure Bastion](bastion-connect-vm-scale-set.md)
