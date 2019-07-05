---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 6eedc095f155a77cddf48211dbc4a677bf188112
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509865"
---
Le immagini standardizzate macchina virtuale (VM) consentono alle organizzazioni di eseguire la migrazione al cloud e garantire la coerenza nelle distribuzioni. Le immagini includono in genere le impostazioni predefinite di sicurezza e la configurazione e software necessari. La configurazione di pipeline di propria creazione dell'immagine richiede tempo, l'infrastruttura e il programma di installazione, ma con Generatore di immagini di macchina virtuale di Azure, è sufficiente fornire una semplice configurazione che descrivono l'immagine, inviarlo al servizio e l'immagine viene compilata e distribuita.
 
Nel generatore di immagini di macchina virtuale di Azure (Azure Image Builder) consente di iniziare con un Windows o immagine di Marketplace di Azure basato su Linux, immagini personalizzate esistenti o Red Hat Enterprise Linux (RHEL) ISO e iniziare ad aggiungere le proprie personalizzazioni. Poiché si basa il generatore di immagini [HashiCorp Packer](https://packer.io/), è anche possibile importare gli script esistenti in strumento di provisioning Packer shell. È inoltre possibile specificare dove si desidera che le immagini in ospitato il [raccolta immagini di Azure condiviso](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), come un'immagine gestita o un disco rigido virtuale.

> [!IMPORTANT]
> Azure Image Builder è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funzionalità di anteprima

Per l'anteprima, queste funzionalità sono supportate:

- Creazione di immagini finale della linea di base, che include i minimi di sicurezza e le configurazioni aziendale e consentire al reparto di personalizzarlo ulteriormente per le proprie esigenze.
- L'applicazione di patch di immagini esistenti, Image Builder consentirà di applicare patch continuamente le immagini personalizzate esistenti.
- Integrazione con la raccolta di immagini condiviso di Azure, consente di distribuire, versione, e immagini a livello globale, scalabilità e ti offre un sistema di gestione di immagini.
- Integrazione con l'immagine esistente creare pipeline, semplicemente chiamare Image Builder dalla tua pipeline o usare l'attività di DevOps Azure semplice del generatore immagine di anteprima.
- Eseguire la migrazione di una pipeline di personalizzazione di immagine esistente in Azure. Usare i processi, comandi e gli script esistenti per personalizzare le immagini.
- Usare il supporto di Red Hat Bring Your Own Subscription. Creare immagini di Red Hat Enterprise per l'uso con le sottoscrizioni di Red Hat idonei e non usate.
- Creazione di immagini in formato VHD.
 

## <a name="regions"></a>Regions
Il servizio di generatore di immagini di Azure sarà disponibile in anteprima nelle aree seguenti. Le immagini possono essere distribuite di fuori di queste aree.
- East US
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2

## <a name="os-support"></a>Supporto del sistema operativo
Un file AIB offrirà supporto per immagini del sistema operativo base di Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019

Un file AIB supporterà RHEL ISO, come origine per:
- RHEL 7.3
- RHEL 7.4
- RHEL 7.5

7\.6 RHEL non è supportato, ma sottoposto a test.

## <a name="how-it-works"></a>Funzionamento


![Rappresentazione concettuale del generatore di immagini di Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

Nel generatore di immagini di Azure è un servizio di Azure completamente gestito che è accessibile da un provider di risorse di Azure. Il processo di generazione di immagini di Azure ha tre parti principali: origine, personalizzare e distribuire, sono rappresentati in un modello. Il diagramma seguente illustra i componenti, con alcune delle relative proprietà. 
 


**Processo generatore immagine** 

![Rappresentazione concettuale del processo di generazione di immagini di Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Creare il modello di immagine come un file con estensione JSON. Questo file con estensione JSON contiene informazioni su origine dell'immagine, le personalizzazioni e la distribuzione. Sono presenti più esempi nel [repository GitHub di Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Inviarlo al servizio, verrà creato un elemento di modello per immagine nel gruppo di risorse specificato. In background, Image Builder scaricherà l'immagine di origine o ISO e script in base alle esigenze. Questi vengono archiviati in un gruppo di risorse distinto che viene creato automaticamente nella sottoscrizione, nel formato: IT_\<DestinationResourceGroup > _\<TemplateName >. 
1. Dopo aver creato il modello di immagine, è quindi possibile compilare l'immagine. In background Image Builder Usa i file di origine e modello per creare una macchina virtuale, rete e archiviazione nel IT_\<DestinationResourceGroup > _\<TemplateName > gruppo di risorse.
1. Durante la creazione dell'immagine, Image builder distribuisce l'immagine in base al modello, quindi Elimina le risorse aggiuntive nel IT_\<DestinationResourceGroup > _\<TemplateName > gruppo di risorse che è stato creato per il processo.


## <a name="permissions"></a>Autorizzazioni

Per consentire la generazione di immagini di macchina virtuale Azure distribuire le immagini per entrambe le immagini gestite o a una raccolta di immagini condivise, si dovrà fornire le autorizzazioni di "Collaboratore" per il servizio "Azure Virtual Machine Image Builder" (ID app: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) nei gruppi di risorse. 

Se si usa un'immagine gestita personalizzata esistente o una versione dell'immagine, il generatore di immagini di Azure sarà necessario un minimo di accesso 'Reader' a tali gruppi di risorse.

È possibile assegnare l'accesso tramite la CLI di Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Se non viene trovato l'account del servizio, che può significare che la sottoscrizione in cui si aggiunge l'assegnazione di ruolo non è ancora registrato per il provider di risorse.


## <a name="costs"></a>Costi
Ti verranno addebitate alcune calcolo, rete e i costi di archiviazione durante la creazione, compilazione e l'archiviazione delle immagini con il generatore di immagini di Azure. Questi costi sono simili ai costi sostenuti per la creazione manuale delle immagini personalizzate. Per le risorse, ti verrà addebitata alle tariffe di Azure. 

Durante il processo di creazione di immagini, i file vengono scaricati e archiviati nel `IT_<DestinationResourceGroup>_<TemplateName>` gruppo di risorse che comporterà un costi di archiviazione di piccole dimensioni. f non si desidera mantenere queste, eliminare il modello di immagine al termine della compilazione di immagini.
 
Image Builder crea una macchina virtuale usando una dimensione di VM D1v2 e lo spazio di archiviazione e rete necessari per la macchina virtuale. Queste risorse saranno validi per la durata del processo di compilazione e verranno eliminate al termine della creazione dell'immagine di Image Builder. 
 
Azure Image Builder distribuirà l'immagine per le aree scelte, che possono venire addebitati i costi di uscita di rete.
 
## <a name="next-steps"></a>Passaggi successivi 
 
Per provare il generatore di immagini di Azure, vedere gli articoli per la compilazione [Linux](../articles/virtual-machines/linux/image-builder.md) oppure [Windows](../articles/virtual-machines/windows/image-builder.md) immagini.
 
 
