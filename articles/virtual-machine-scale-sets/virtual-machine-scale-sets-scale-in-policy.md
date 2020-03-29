---
title: Usare criteri di scalabilità in verticale personalizzati con i set di scalabilità di macchine virtuali di AzureUse custom scale-in policies with Azure virtual machine scale sets
description: Informazioni su come usare criteri di scalabilità orizzontale personalizzati con i set di scalabilità di macchine virtuali di Azure che usano la configurazione di scalabilità automatica per gestire il numero di istanzeLearn how to use custom scale-in policies with Azure virtual machine scale sets that use autoscale configuration to manage instance count
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919839"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Usare criteri di scalabilità in verticale personalizzati con i set di scalabilità di macchine virtuali di AzureUse custom scale-in policies with Azure virtual machine scale sets

La distribuzione di un set di scalabilità di macchine virtuali può essere scalata orizzontalmente o con scalabilità orizzontale in base a una matrice di metriche, tra cui metriche personalizzate definite dalla piattaforma e dall'utente. Mentre una scalabilità orizzontale crea nuove macchine virtuali basate sul modello di set di scalabilità, una scalabilità orizzontale influisce sulle macchine virtuali in esecuzione che possono avere configurazioni e/o funzioni diverse con l'evoluzione del carico di lavoro del set di scalabilità. 

La funzionalità dei criteri di scalabilità verticale offre agli utenti un modo per configurare l'ordine in cui le macchine virtuali vengono ridimensionate, tramite tre configurazioni con scalabilità verticale:The scale-in policy feature provides users a way to configure the order in which virtual machines are scaled-in, by way of three scale-in configurations: 

1. Predefinito
2. Nuovo VM
3. Vm meno recente

### <a name="default-scale-in-policy"></a>Criteri di scalabilità verticale predefiniti

Per impostazione predefinita, il set di scalabilità delle macchine virtuali applica questo criterio per determinare in quali istanze verrà ridimensionata. Con il criterio *predefinito,* le macchine virtuali vengono selezionate per la scalabilità verticale nell'ordine seguente:With the Default policy, VMs are selected for scale-in in in the following order:

1. Bilanciare le macchine virtuali tra le zone di disponibilità (se il set di scalabilità viene distribuito nella configurazione zonale)Balance virtual machines across availability zones (if the scale set is deployed in zonal configuration)
2. Bilanciare le macchine virtuali tra domini di errore (massimo sforzo)
3. Eliminare la macchina virtuale con l'ID istanza più altoDelete virtual machine with the highest instance ID

Non è necessario che gli utenti specifichino un criterio di scalabilità verticale se desiderano solo che venga seguito l'ordinamento predefinito.

Si noti che il bilanciamento tra zone di disponibilità o domini di errore non comporta lo spostamento delle istanze tra zone di disponibilità o domini di errore. Il bilanciamento viene ottenuto tramite l'eliminazione di macchine virtuali dalle zone di disponibilità non bilanciate o dai domini di errore fino a quando la distribuzione delle macchine virtuali non diventa bilanciata.

### <a name="newestvm-scale-in-policy"></a>Criteri di scalabilità verticale più recente

Questo criterio eliminerà la macchina virtuale creata più recente nel set di scalabilità, dopo il bilanciamento delle macchine virtuali tra le zone di disponibilità (per le distribuzioni zonali). L'abilitazione di questo criterio richiede una modifica della configurazione nel modello di set di scalabilità della macchina virtuale.

### <a name="oldestvm-scale-in-policy"></a>Criteri di scalabilità verticale OldestVM

Questo criterio eliminerà la macchina virtuale creata meno recente nel set di scalabilità, dopo il bilanciamento delle macchine virtuali tra le zone di disponibilità (per le distribuzioni zonali). L'abilitazione di questo criterio richiede una modifica della configurazione nel modello di set di scalabilità della macchina virtuale.

## <a name="enabling-scale-in-policy"></a>Abilitazione dei criteri di scalabilità orizzontaleEnabling scale-in policy

Un criterio di scalabilità verticale viene definito nel modello di set di scalabilità delle macchine virtuali. Come indicato nelle sezioni precedenti, è necessaria una definizione dei criteri di scalabilità verticale quando si utilizzano i criteri 'NewestVM' e 'OldestVM'. Il set di scalabilità delle macchine virtuali utilizzerà automaticamente i criteri di scalabilità verticale 'Predefinito' se nel modello del set di scalabilità non viene trovata alcuna definizione di criteri di scalabilità verticale. 

Un criterio di scalabilità verticale può essere definito nel modello di set di scalabilità delle macchine virtuali nei modi seguenti:A scale-in policy can be defined on the virtual machine scale set model in the following ways:

### <a name="azure-portal"></a>Portale di Azure
 
I passaggi seguenti definiscono i criteri di scalabilità verticale durante la creazione di un nuovo set di scalabilità. 
 
1. Passare a Set di **scalabilità macchine virtuali**.
1. Selezionare **: Aggiungi** per creare un nuovo set di scalabilità.
1. Passare alla scheda **Ridimensionamento.** 
1. Individuare la sezione **Dei criteri di scalabilità verticale.**
1. Selezionare un criterio di scalabilità verticale dall'elenco a discesa.
1. Al termine della creazione del nuovo set di scalabilità, selezionare il pulsante **Rivedi e crea.**

### <a name="using-api"></a>Uso dell'API

Eseguire un PUT nel set di scalabilità della macchina virtuale usando API 2019-03-01:Execute a PUT on the virtual machine scale set using API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Creare un gruppo di risorse, quindi creare un nuovo set di scalabilità orizzontale con i criteri di scalabilità verticale impostati come *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

L'esempio seguente aggiunge un criterio di scalabilità orizzontale durante la creazione di un nuovo set di scalabilità. Creare innanzitutto un gruppo di risorse, quindi creare un nuovo set di scalabilità orizzontale con i criteri di scalabilità verticale come *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Utilizzo del modello

Nel modello, in "proprietà", aggiungere quanto segue:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

I blocchi precedenti specificano che il set di scalabilità della macchina virtuale eliminerà la macchina virtuale meno recente in un set di scalabilità bilanciato per zona, quando viene attivata una scalabilità verticale (tramite scalabilità automatica o eliminazione manuale).

Quando un set di scalabilità di macchine virtuali non è bilanciato in una zona, il set di scalabilità eliminerà innanzitutto le macchine virtuali nelle zone sbilanciate. All'interno delle zone sbilanciate, il set di scalabilità utilizzerà i criteri di scalabilità in di scalabilità specificati in precedenza per determinare in quale macchina virtuale ridimensionare. In questo caso, all'interno di una zona sbilanciata, il set di scalabilità selezionerà la macchina virtuale meno recente in tale zona da eliminare.

Per il set di scalabilità di macchine virtuali non di zona, il criterio seleziona la macchina virtuale meno recente nel set di scalabilità per l'eliminazione.

Lo stesso processo si applica quando si utilizza 'NewestVM' nei criteri di scalabilità precedenti.

## <a name="modifying-scale-in-policies"></a>Modifica dei criteri di scalabilità verticale

La modifica dei criteri di scalabilità verticale segue lo stesso processo di applicazione dei criteri di scalabilità verticale. Ad esempio, se nell'esempio precedente si desidera modificare il criterio da "OldestVM" a "NewestVM", è possibile farlo:

### <a name="azure-portal"></a>Portale di Azure

È possibile modificare i criteri di scalabilità verticale di un set di scalabilità esistente tramite il portale di Azure.You can modify the scale-in policy of an existing scale set through the Azure portal. 
 
1. In un set di scalabilità di macchine virtuali esistente selezionare **Scalabilità** dal menu a sinistra.
1. Selezionare la scheda **Criteri di scalabilità verticale.**
1. Selezionare un criterio di scalabilità verticale dall'elenco a discesa.
1. Al termine, selezionare **Salva**. 

### <a name="using-api"></a>Uso dell'API

Eseguire un PUT nel set di scalabilità della macchina virtuale usando API 2019-03-01:Execute a PUT on the virtual machine scale set using API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Aggiornare i criteri di scalabilità verticale di un set di scalabilità esistente:Update the scale-in policy of an existing scale set:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Di seguito è riportato un esempio per l'aggiornamento dei criteri di scalabilità verticale di un set di scalabilità esistente:The following is an example for updating the scale-in policy of an existing scale set: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Utilizzo del modello

Nel modello, in "proprietà", modificare il modello come indicato di seguito e ridistribuire: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Lo stesso processo si applicherà se si decide di modificare 'NewestVM' su 'Default' o 'OldestVM'

## <a name="instance-protection-and-scale-in-policy"></a>Protezione delle istanze e criteri di scalabilità verticale

I set di scalabilità delle macchine virtuali forniscono due tipi di [protezione dell'istanza:](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)

1. Proteggere dalla scalabilità verticale
2. Proteggere dalle azioni predefinite

Una macchina virtuale protetta non viene eliminata tramite un'azione di scalabilità verticale, indipendentemente dal criterio di scalabilità verticale applicato. Ad esempio, se VM_0 (la macchina virtuale meno recente nel set di scalabilità) è protetta dalla scalabilità verticale e il set di scalabilità è abilitato per i criteri di scalabilità 'OldestVM', VM_0 non verrà considerato per la scalabilità, anche se è la macchina virtuale meno recente nel set di scalabilità. 

Una macchina virtuale protetta può essere eliminata manualmente dall'utente in qualsiasi momento, indipendentemente dai criteri di scalabilità verticale abilitati nel set di scalabilità. 

## <a name="usage-examples"></a>Esempi di utilizzo 

Gli esempi seguenti illustrano come un set di scalabilità di macchine virtuali selezionerà le macchine virtuali da eliminare quando viene attivato un evento di scalabilità verticale. Si presuppone che le macchine virtuali con gli ID istanza più elevati siano le macchine virtuali più recenti nel set di scalabilità e che le macchine virtuali con ID istanza più piccolo siano le macchine virtuali meno recenti nel set di scalabilità. 

### <a name="oldestvm-scale-in-policy"></a>Criteri di scalabilità verticale OldestVM

| Event                 | ID istanza nella zona 1  | ID istanza nella zona 2  | ID istanza nella zona 3  | Selezione scale-in                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Scalabilità verticale              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Scegliere tra la zona 1 e 2, anche se la zona 3 ha la macchina virtuale meno recente. Eliminare VM2 dalla zona 2 perché è la macchina virtuale meno recente in tale zona.   |
| Scalabilità verticale              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Scegliere la zona 1 anche se la zona 3 include la macchina virtuale meno recente. Eliminare VM3 dalla zona 1 poiché è la macchina virtuale meno recente in tale zona.                  |
| Scalabilità verticale              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Le zone sono equilibrate. Eliminare VM1 nella zona 3 poiché è la macchina virtuale meno recente nel set di scalabilità.                                               |
| Scalabilità verticale              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Scegliere tra la zona 1 e la zona 2. Eliminare VM4 nella zona 1 poiché è la macchina virtuale meno recente nelle due zone.                              |
| Scalabilità verticale              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Scegliere la zona 2 anche se la zona 1 include la macchina virtuale meno recente. Eliminare VM6 nella zona 1 poiché è la macchina virtuale meno recente in tale zona.                    |
| Scalabilità verticale              | ***5***, 10            | 9, 11                  | 7, 8                   | Le zone sono equilibrate. Eliminare VM5 nella zona 1 poiché è la macchina virtuale meno recente nel set di scalabilità.                                                |

Per i set di scalabilità di macchine virtuali non di zona, il criterio seleziona la macchina virtuale meno recente nel set di scalabilità per l'eliminazione. Qualsiasi macchina virtuale "protetta" verrà ignorata per l'eliminazione.

### <a name="newestvm-scale-in-policy"></a>Criteri di scalabilità verticale più recente

| Event                 | ID istanza nella zona 1  | ID istanza nella zona 2  | ID istanza nella zona 3  | Selezione scale-in                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Scalabilità verticale              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Scegliere tra le zone 1 e 2. Eliminare VM11 dalla zona 2 in quanto è la macchina virtuale più recente nelle due aree.                                |
| Scalabilità verticale              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Scegliere la zona 1 in quanto contiene più macchine virtuali rispetto alle altre due zone. Eliminare VM10 dalla zona 1 in quanto è la macchina virtuale più recente in tale zona.          |
| Scalabilità verticale              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Le zone sono equilibrate. Eliminare VM9 nella zona 2 in quanto è la macchina virtuale più recente nel set di scalabilità.                                                |
| Scalabilità verticale              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Scegliere tra la zona 1 e la zona 3. Eliminare VM8 nella zona 3 in quanto è la macchina virtuale più recente in tale zona.                                      |
| Scalabilità verticale              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Scegliere la zona 1 anche se la zona 3 contiene la macchina virtuale più recente. Eliminare VM5 nella zona 1 in quanto è la macchina virtuale più recente in tale zona.                    |
| Scalabilità verticale              | 3, 4                   | 2, 6                   | 1, ***7***             | Le zone sono equilibrate. Eliminare VM7 nella zona 3 in quanto è la macchina virtuale più recente nel set di scalabilità.                                                |

Per i set di scalabilità di macchine virtuali non di zona, il criterio seleziona la macchina virtuale più recente nel set di scalabilità per l'eliminazione. Qualsiasi macchina virtuale "protetta" verrà ignorata per l'eliminazione. 

## <a name="troubleshoot"></a>Risolvere i problemi

1. Impossibile abilitare scaleInPolicy Se viene visualizzato un errore 'BadRequest' con un messaggio di errore che indica che non è possibile trovare il membro 'scaleInPolicy' nell'oggetto di tipo 'properties'", quindi controllare la versione dell'API utilizzata per il set di scalabilità della macchina virtuale. Per questa funzionalità è necessaria la versione API 2019-03-01 o successiva.

2. Selezione errata di macchine virtuali per la scalabilità verticale Fare riferimento agli esempi precedenti. Se il set di scalabilità di macchine virtuali è una distribuzione zonale, i criteri di scalabilità verticale vengono applicati prima alle zone sbilanciate e quindi nel set di scalabilità una volta bilanciato la zona. Se l'ordine di scalabilità orizzontale non è coerente con gli esempi precedenti, generare una query con il team del set di scalabilità della macchina virtuale per la risoluzione dei problemi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.