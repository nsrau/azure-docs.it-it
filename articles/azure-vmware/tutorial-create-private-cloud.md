---
title: 'Esercitazione: Distribuire un cluster vSphere in Azure'
description: Informazioni su come distribuire un cluster vSphere con la soluzione Azure VMware
ms.topic: tutorial
ms.date: 11/19/2020
ms.openlocfilehash: 93937f8ca0918494810885f5cb45de571a6e1529
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966310"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Esercitazione: Distribuire un cloud privato della soluzione Azure VMware in Azure

La soluzione Azure VMware consente di distribuire un cluster vSphere in Azure. La distribuzione iniziale minima è di tre host. È possibile aggiungere altri host uno alla volta, fino a un massimo di 16 host per cluster. 

Poiché la soluzione Azure VMware non consente di gestire il cloud privato con l'istanza locale di vCenter all'avvio, è necessario definire una configurazione aggiuntiva. Queste procedure e i prerequisiti correlati verranno illustrati in questa esercitazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un cloud privato della soluzione Azure VMware
> * Verificare la distribuzione del cloud privato

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Diritti amministrativi appropriati e l'autorizzazione per creare un cloud privato.
- Assicurarsi di avere la rete appropriata configurata come descritto in [Esercitazione: Elenco di controllo per la rete](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Creare un cloud privato

Per creare un cloud privato della soluzione Azure VMware, è possibile usare il [portale di Azure](#azure-portal) oppure l'[interfaccia della riga di comando di Azure](#azure-cli).

### <a name="azure-portal"></a>Portale di Azure

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Anziché usare il portale di Azure creare un cloud privato della soluzione Azure VMware, è possibile usare l'interfaccia della riga di comando di Azure e Azure Cloud Shell.  Per un elenco di comandi che è possibile usare con la soluzione Azure VMware, vedere [Comandi di Azure VMware](/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Aprire Azure Cloud Shell

Selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **Invio** per eseguirli.

#### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando `[az group create](/cli/azure/group)`. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Creare un cloud privato

Specificare un nome per il gruppo di risorse e per il cloud privato, una località e le dimensioni del cluster.

| Proprietà  | Descrizione  |
| --------- | ------------ |
| **-g** (nome del gruppo di risorse)     | Nome del gruppo di risorse per le risorse del cloud privato.        |
| **-n** (nome del cloud privato)     | Nome del cloud privato della soluzione Azure VMware.        |
| **--location**     | Località per il cloud privato.         |
| **--cluster-size**     | Le dimensioni del cluster. Il valore minimo è 3.         |
| **--network-block**     | Il blocco di rete degli indirizzi IP CIDR da usare per il cloud privato. Il blocco di indirizzi non deve sovrapporsi ai blocchi di indirizzi usati in altre reti virtuali presenti nella sottoscrizione e nelle reti locali.        |
| **--sku** | Valore SKU: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>Eliminare un cloud privato della soluzione Azure VMware

Se si dispone di un cloud privato della soluzione Azure VMware che non è più necessario, è possibile eliminarlo. Un cloud privato della soluzione Azure VMware include un dominio di rete isolato, uno o più cluster vSphere di cui è stato effettuato il provisioning negli host server dedicati e diverse macchine virtuali. Quando viene eliminato un cloud privato, vengono eliminate tutte le macchine virtuali, i relativi dati e i cluster. Gli host bare metal dedicati vengono cancellati e restituiti in modo sicuro al pool gratuito. Il dominio di rete di cui è stato effettuato il provisioning per il cliente viene eliminato.  

> [!CAUTION]
> L'eliminazione del cloud privato è un'operazione irreversibile. Una volta eliminato il cloud privato, non è possibile recuperare i dati perché questa operazione termina tutti i carichi di lavoro in esecuzione e i componenti ed elimina definitivamente tutti i dati e le impostazioni di configurazione del cloud privato, inclusi gli indirizzi IP pubblici.

### <a name="prerequisites"></a>Prerequisiti

Una volta eliminato un cloud privato, non è possibile recuperare le macchine virtuali e i relativi dati. Se i dati della macchina virtuale saranno necessari in un secondo momento, l'amministratore deve prima eseguire il backup di tutti i dati prima di eliminare il cloud privato.

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>Passaggi per eliminare un cloud privato della soluzione Azure VMware

1. Accedere alla pagina Soluzioni Azure VMware nel portale di Azure.

2. Selezionare il cloud privato da eliminare.
 
3. Immettere il nome del cloud privato e selezionare **Sì**. Il processo di eliminazione viene completato in poche ore.  

## <a name="azure-vmware-commands"></a>Comandi di Azure VMware

Per un elenco di comandi che è possibile usare con la soluzione Azure VMware, vedere [Comandi di Azure VMware](/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un cloud privato della soluzione Azure VMware
> * Verificare la distribuzione del cloud privato
> * Eliminare un cloud privato della soluzione Azure VMware

Continuare con l'esercitazione successiva per apprendere come creare una jumpbox, che consente di connettersi all'ambiente in uso per poter gestire il cloud privato in locale.


> [!div class="nextstepaction"]
> [Accedere a un cloud privato della soluzione Azure VMware](tutorial-access-private-cloud.md)