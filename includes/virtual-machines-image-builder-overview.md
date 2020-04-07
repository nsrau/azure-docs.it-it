---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80419276"
---
Le immagini di macchine virtuali (VM) standardizzate consentono alle organizzazioni di eseguire la migrazione al cloud e garantire la coerenza nelle distribuzioni. Le immagini includono in genere impostazioni di sicurezza e configurazione predefinite e il software necessario. La configurazione della pipeline di creazione di immagini richiede tempo, infrastruttura e configurazione, ma con Azure VM Image Builder è sufficiente fornire una configurazione semplice che descriva l'immagine, la invii al servizio e l'immagine venga compilata e distribuita.
 
Azure VM Image Builder (Azure Image Builder) consente di iniziare con un'immagine di Azure Marketplace basata su Windows o Linux, immagini personalizzate esistenti o ISO Red Hat Enterprise Linux (RHEL) e iniziare ad aggiungere personalizzazioni personalizzate. Poiché Image Builder è basato su [HashiCorp Packer](https://packer.io/), è anche possibile importare gli script del provisioner della shell Packer esistenti. È anche possibile specificare la posizione in cui si desidera che le immagini siano ospitate nella [raccolta di immagini condivise](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)di Azure come immagine gestita o disco rigido virtuale.

> [!IMPORTANT]
> Azure Image Builder è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funzionalità di anteprima

Per l'anteprima sono supportate le seguenti funzionalità:

- Creazione di immagini di base dorate, che includono la sicurezza minima e le configurazioni aziendali, e consentono ai reparti di personalizzarle ulteriormente in base alle loro esigenze.
- Applicazione di patch alle immagini esistenti, Image Builder consente di applicare continuamente patch alle immagini personalizzate esistenti.
- Connettere Image Builder alle reti virtuali esistenti, in modo da potersi connettere a server di configurazione esistenti (DSC, Chef, Puppet e così via), a condivisioni file o a qualsiasi altro server/servizio instradabile.
- L'integrazione con azure Shared Image Gallery consente di distribuire, versione e ridimensionare le immagini a livello globale e offre un sistema di gestione delle immagini.
- Integrazione con le pipeline di compilazione delle immagini esistenti, è sufficiente chiamare Image Builder dalla pipeline o usare la semplice attività Azure DevOps di Preview Image Builder.Integration with existing image build pipelines, just call Image Builder from your pipeline, or use the simple Preview Image Builder Azure DevOps Task.
- Eseguire la migrazione di una pipeline di personalizzazione dell'immagine esistente in Azure.Migrate an existing image customization pipeline to Azure. Utilizzare gli script, i comandi e i processi esistenti per personalizzare le immagini.
- Creazione di immagini in formato VHD.
 

## <a name="regions"></a>Regioni
Il servizio Azure Image Builder sarà disponibile per l'anteprima in queste aree. Le immagini possono essere distribuite al di fuori di queste aree.
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale

## <a name="os-support"></a>Supporto del sistema operativo
AIB supporterà le immagini del sistema operativo di base di Azure Marketplace:AIB will support Azure Marketplace base OS images:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multisessione/Professional
- Windows 2016
- Windows 2019 (informazioni in due)

Il supporto isOR RHEL è deprecato, consultare la documentazione del modello per ulteriori dettagli.

## <a name="how-it-works"></a>Funzionamento


![Disegno concettuale di Azure Image BuilderConceptual drawing of Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder è un servizio di Azure completamente gestito accessibile da un provider di risorse di Azure.The Azure Image Builder is a fully managed Azure service that is accessible by an Azure resource provider. Il processo di Azure Image Builder è basato su tre parti principali: origine, personalizzazione e distribuzione, rappresentate in un modello. Il diagramma seguente mostra i componenti, con alcune delle relative proprietà. 
 


**Processo di creazione di immagini** 

![Disegno concettuale del processo di Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Creare il modello di immagine come file .json. Questo file .json contiene informazioni sull'origine dell'immagine, le personalizzazioni e la distribuzione. Esistono più esempi nel [repository GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)di Azure Image Builder .
1. Inviarlo al servizio, verrà creato un elemento modello di immagine nel gruppo di risorse specificato. In background, Image Builder scaricherà l'immagine di origine o ISO e gli script in base alle esigenze. Questi vengono archiviati in un gruppo di risorse separato creato\<automaticamente nella\<sottoscrizione, nel formato: IT_ DestinationResourceGroup>_ TemplateName>. 
1. Una volta creato il modello di immagine, è possibile creare l'immagine. In Background Image Builder usa il modello e i file di origine per creare una macchina virtuale\<(dimensione predefinita: Standard_D1_v2), rete, IP pubblico, gruppo di sicurezza di rete e archiviazione nel gruppo di risorse IT_ DestinationResourceGroup>_\<TemplateName>.
1. Come parte della creazione dell'immagine, Image builder distribuisce l'immagine in base\<al modello,\<quindi elimina le risorse aggiuntive nell'IT_ DestinationResourceGroup>_ TemplateName> gruppo di risorse creato per il processo.


## <a name="permissions"></a>Autorizzazioni

Per consentire a Generatore immagini VM di Azure di distribuire immagini alle immagini gestite o a una raccolta immagini condivise, è necessario fornire le autorizzazioni 'Collaboratore' per il servizio "Azure Virtual Machine Image Builder" (ID app: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) nei gruppi di risorse. 

Se si usa una versione di immagine o immagine personalizzata esistente, Azure Image Builder avrà bisogno di un accesso minimo a "Lettore" a tali gruppi di risorse.

È possibile assegnare l'accesso usando l'interfaccia della riga di comando di Azure:You can assign access using the Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

È possibile assegnare l'accesso tramite PowerShell:You can assign access using the PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Se l'account di servizio non viene trovato, è possibile che la sottoscrizione a cui si sta aggiungendo l'assegnazione di ruolo non sia ancora registrata per il provider di risorse.


## <a name="costs"></a>Costi
Durante la creazione, la creazione, la creazione e l'archiviazione di immagini con Azure Image Builder verranno sostenuti alcuni costi di elaborazione, rete e archiviazione. Questi costi sono simili ai costi sostenuti per la creazione manuale di immagini personalizzate. Per le risorse, verrà addebitato quanto alle tariffe di Azure.For the resources, you will be charged at your Azure rates. 

Durante il processo di creazione dell'immagine, i file vengono scaricati e archiviati nel gruppo di `IT_<DestinationResourceGroup>_<TemplateName>` risorse, che comporterà costi di archiviazione ridotti. Se non si desidera mantenere questi, eliminare il modello di **immagine** dopo la compilazione dell'immagine.
 
Image Builder crea una macchina virtuale usando una dimensione della macchina virtuale D1v2 e l'archiviazione e la rete necessarie per la macchina virtuale. Queste risorse dureranno per la durata del processo di compilazione e verranno eliminate una volta che Image Builder ha completato la creazione dell'immagine. 
 
Azure Image Builder distribuirà l'immagine nelle aree scelte, che potrebbero comportare costi in uscita di rete.
 
## <a name="next-steps"></a>Passaggi successivi 
 
Per provare Azure Image Builder, vedere gli articoli per la creazione di immagini Linux o [Windows.To](../articles/virtual-machines/windows/image-builder.md) try out the Azure Image Builder, see the articles for building [Linux](../articles/virtual-machines/linux/image-builder.md) or Windows images.
 
 
