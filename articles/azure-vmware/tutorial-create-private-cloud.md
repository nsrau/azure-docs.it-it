---
title: 'Esercitazione: Distribuire un cluster vSphere in Azure'
description: Informazioni su come distribuire un cluster vSphere in Azure con la soluzione Azure VMWare (AVS)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fc753f43563650357cf43c102e94f0057b62a406
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873743"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Esercitazione: Distribuire un cloud privato di AVS in Azure

La soluzione Azure VMware (AVS) offre la possibilità di distribuire un cluster vSphere in Azure. La distribuzione iniziale minima è di tre host. È possibile aggiungere altri host uno alla volta, fino a un massimo di 16 host per cluster. 

Poiché la soluzione AVS non consente di gestire il cloud privato con l'istanza locale di vCenter all'avvio, è necessario eseguire una configurazione aggiuntiva e una connessione a tale istanza locale, a una rete virtuale e altro ancora. Queste procedure e i prerequisiti correlati verranno trattati in questa serie di esercitazioni.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un cloud privato di AVS
> * Verificare la distribuzione del cloud privato

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Diritti amministrativi appropriati e l'autorizzazione per creare un cloud privato.
- Assicurarsi di avere la rete appropriata configurata come descritto in [Esercitazione: Elenco di controllo per la rete](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Per usare la soluzione Azure VMware, è prima necessario registrare il provider di risorse. Nell'esempio seguente il provider di risorse viene registrato con la sottoscrizione.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Per informazioni su come registrare il provider di risorse in altri modi, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Creare un cloud privato

È possibile creare un cloud privato di AVS usando il [portale di Azure](#azure-portal) oppure l'[interfaccia della riga di comando di Azure](#azure-cli).

### <a name="azure-portal"></a>Portale di Azure

Nel portale di Azure selezionare **+ Crea nuova risorsa**. Nella casella di testo **Cerca nel marketplace** digitare `Azure VMware Solution` e selezionare **Soluzione Azure VMware** nell'elenco. Nella finestra **Soluzione Azure VMware** selezionare **Crea**

Nella scheda **Informazioni di base** immettere i valori per i campi. La tabella seguente include un elenco dettagliato delle proprietà.

| Campo   | valore  |
| ---| --- |
| **Sottoscrizione** | La sottoscrizione che si intende usare per la distribuzione.|
| **Gruppo di risorse** | Il gruppo di risorse per le risorse del cloud privato. |
| **Posizione** | Selezionare una località, ad esempio **Stati Uniti orientali**.|
| **Nome risorsa** | Il nome del cloud privato di AVS. |
| **SKU** | Selezionare il valore di SKU seguente: AV36 |
| **Host** | Il numero di host da aggiungere al cluster del cloud privato. Il valore predefinito è 3. Questo valore può essere aumentato o ridotto dopo la distribuzione.  |
| **Password dell'amministratore di vCenter** | Immettere la password dell'amministratore del cloud. |
| **Password di NSX-T Manager** | Immettere la password dell'amministratore di NSX-T. |
| **Blocco di indirizzi** | Immettere un blocco di indirizzi IP per la rete CIDR per il cloud privato, ad esempio 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Creare un cloud privato" border="true":::

Al termine, selezionare **Rivedi e crea**. Nella schermata successiva verificare le informazioni immesse. Se tutte le informazioni sono corrette, selezionare **Crea**.

> [!NOTE]
> Questo passaggio richiede circa due ore. 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure per creare un cloud privato di AVS in Azure. A questo scopo, è possibile usare Azure Cloud Shell, come illustrato nella procedura seguente.

#### <a name="open-azure-cloud-shell"></a>Aprire Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando https://shell.azure.com/bash. Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **Invio** per eseguirli.

#### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Creare un cloud privato

Per creare un cloud privato di AVS, è necessario specificare un nome per il gruppo di risorse, un nome per il cloud privato, una località e le dimensioni del cluster


|Proprietà  |Descrizione  |
|---------|---------|
|Nome del gruppo di risorse     | Il nome del gruppo di risorse in cui distribuire il cloud privato.        |
|Nome cloud privato     | Il nome del cloud privato.        |
|Location     | La località usata per il cloud privato         |
|Dimensioni del cluster     | Le dimensioni del cluster. Il valore minimo è 3.         |
|Blocco di rete     | L'intervallo CIDR da usare per il cloud privato. È consigliabile che sia univoco per l'ambiente locale oltre che per l'ambiente di Azure.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Verificare se la distribuzione è riuscita

Passare al gruppo di risorse creato e selezionare il cloud privato. Al termine della distribuzione verrà visualizzata la schermata seguente e sarà possibile vedere lo stato **Operazione riuscita**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Verificare la distribuzione del cloud privato" border="true":::

## <a name="delete-a-private-cloud"></a>Eliminare un cloud privato

Se è disponibile un cloud privato di AVS ed è stato verificato che non è più necessario, è possibile eliminarlo. Quando si elimina un cloud privato, vengono eliminati anche tutti i cluster e i relativi componenti.

A questo scopo, passare al cloud privato nel portale di Azure e selezionare **Elimina**. Nella pagina di conferma verificare il nome del cloud privato e selezionare **Sì**.

> [!CAUTION]
> L'eliminazione del cloud privato è un'operazione irreversibile. Una volta eliminato il cloud privato, non è possibile recuperare i dati perché questa operazione termina tutti i carichi di lavoro in esecuzione e i componenti ed elimina definitivamente tutti i dati e le impostazioni di configurazione del cloud privato, inclusi gli indirizzi IP pubblici. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un cloud privato di AVS
> * Verificare la distribuzione del cloud privato

Continuare con l'esercitazione successiva per informazioni su come creare una rete virtuale da usare con il cloud privato come parte della configurazione della gestione locale per i cluster del cloud privato.

> [!div class="nextstepaction"]
> [Creare una rete virtuale](tutorial-configure-networking.md)
