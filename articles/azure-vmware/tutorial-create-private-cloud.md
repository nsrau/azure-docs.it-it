---
title: 'Esercitazione: Distribuire un cluster vSphere in Azure'
description: Informazioni su come distribuire un cluster vSphere in Azure con la soluzione Azure VMWare
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 8aeedeeb785f149239f2bf9a4b58a18ec8bfeb77
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750487"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Esercitazione: Distribuire un cloud privato della soluzione Azure VMware in Azure

La soluzione Azure VMware consente di distribuire un cluster vSphere in Azure. La distribuzione iniziale minima è di tre host. È possibile aggiungere altri host uno alla volta, fino a un massimo di 16 host per cluster. 

Poiché la soluzione Azure VMware non consente di gestire il cloud privato con l'istanza locale di vCenter all'avvio, è necessario eseguire una configurazione aggiuntiva e una connessione a tale istanza locale, a una rete virtuale e altre operazioni. Queste procedure e i prerequisiti correlati verranno illustrati in questa esercitazione.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un cloud privato della soluzione Azure VMware
> * Verificare la distribuzione del cloud privato

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Diritti amministrativi appropriati e l'autorizzazione per creare un cloud privato.
- Assicurarsi di avere la rete appropriata configurata come descritto in [Esercitazione: Elenco di controllo per la rete](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Per usare la soluzione Azure VMware, è prima necessario registrare il provider di risorse con la sottoscrizione.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Per informazioni su come registrare il provider di risorse in altri modi, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).


## <a name="create-a-private-cloud"></a>Creare un cloud privato

Per creare un cloud privato della soluzione Azure VMware, è possibile usare il [portale di Azure](#azure-portal) oppure l'[interfaccia della riga di comando di Azure](#azure-cli).

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una nuova risorsa**. Nella casella di testo **Cerca nel marketplace** digitare `Azure VMware Solution` e selezionare **Soluzione Azure VMware** nell'elenco. Nella finestra **Soluzione Azure VMware** selezionare **Crea**

1. Nella scheda **Informazioni di base** immettere i valori per i campi. La tabella seguente elenca le proprietà per i campi.

   | Campo   | valore  |
   | ---| --- |
   | **Sottoscrizione** | La sottoscrizione che si intende usare per la distribuzione.|
   | **Gruppo di risorse** | Il gruppo di risorse per le risorse del cloud privato. |
   | **Posizione** | Selezionare una località, ad esempio **Stati Uniti orientali**.|
   | **Nome risorsa** | Nome del cloud privato della soluzione Azure VMware. |
   | **SKU** | Selezionare il valore di SKU seguente: AV36 |
   | **Host** | Numero di host da aggiungere al cluster del cloud privato. Il valore predefinito è 3 e può essere aumentato o ridotto dopo la distribuzione.  |
   | **Password dell'amministratore di vCenter** | Immettere la password dell'amministratore del cloud. |
   | **Password di NSX-T Manager** | Immettere la password amministratore di NSX-T. |
   | **Blocco di indirizzi** | Immettere un blocco di indirizzi IP per la rete CIDR per il cloud privato, ad esempio 10.175.0.0/22. |

   :::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Nella scheda Informazioni di base immettere i valori per i campi." border="true":::

1. Al termine, selezionare **Rivedi e crea**. Nella schermata successiva verificare le informazioni immesse. Se tutte le informazioni sono corrette, selezionare **Crea**.

   > [!NOTE]
   > Questo passaggio richiede circa due ore. 

1. Verificare che la distribuzione sia riuscita. Passare al gruppo di risorse creato e selezionare il cloud privato.  Al termine della distribuzione, verrà visualizzato lo stato **Completato**. 

   :::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Verificare che la distribuzione sia riuscita." border="true":::

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Anziché usare il portale di Azure creare un cloud privato della soluzione Azure VMware, è possibile usare l'interfaccia della riga di comando di Azure e Azure Cloud Shell. Si tratta di una shell interattiva gratuita in cui sono disponibili gli strumenti comuni di Azure preinstallati e configurati per l'uso con l'account corrente. 

#### <a name="open-azure-cloud-shell"></a>Aprire Azure Cloud Shell

Per aprire Cloud Shell, selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **Invio** per eseguirli.

#### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Creare un cloud privato

Specificare un nome per il gruppo di risorse, un nome per il cloud privato, una località e le dimensioni del cluster.

| Proprietà  | Descrizione  |
| --------- | ------------ |
| **-g** (nome del gruppo di risorse)     | Nome del gruppo di risorse per le risorse del cloud privato.        |
| **-n** (nome del cloud privato)     | Nome del cloud privato della soluzione Azure VMware.        |
| **--location**     | Località per il cloud privato.         |
| **--cluster-size**     | Le dimensioni del cluster. Il valore minimo è 3.         |
| **--network-block**     | Il blocco di rete degli indirizzi IP CIDR da usare per il cloud privato. Il blocco di indirizzi non deve sovrapporsi ai blocchi di indirizzi usati in altre reti virtuali presenti nella sottoscrizione e nelle reti locali.        |
| **--sku** | Valore SKU: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Eliminare un cloud privato (portale di Azure)

Se si dispone di un cloud privato della soluzione Azure VMware che non è più necessario, è possibile eliminarlo. Quando si elimina un cloud privato, vengono eliminati anche tutti i cluster e i relativi componenti.

A questo scopo, passare al cloud privato nel portale di Azure e selezionare **Elimina**. Nella pagina di conferma verificare il nome del cloud privato e selezionare **Sì**.

> [!CAUTION]
> L'eliminazione del cloud privato è un'operazione irreversibile. Una volta eliminato il cloud privato, non è possibile recuperare i dati perché questa operazione termina tutti i carichi di lavoro in esecuzione e i componenti ed elimina definitivamente tutti i dati e le impostazioni di configurazione del cloud privato, inclusi gli indirizzi IP pubblici. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un cloud privato della soluzione Azure VMware
> * Verificare la distribuzione del cloud privato

Continuare con l'esercitazione successiva per informazioni su come creare una rete virtuale da usare con il cloud privato come parte della configurazione della gestione locale per i cluster del cloud privato.

> [!div class="nextstepaction"]
> [Creare una rete virtuale](tutorial-configure-networking.md)
