---
author: cynthn
ms.author: cynthn
ms.date: 08/03/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: daberry
ms.openlocfilehash: a5a201a9f993db2be00645d8d60a11c5be9cdbe0
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89303969"
---
Le immagini di macchine virtuali (VM) standardizzate consentono alle organizzazioni di eseguire la migrazione al cloud e garantire la coerenza nelle distribuzioni. Le immagini includono in genere impostazioni predefinite di configurazione e sicurezza, oltre al software necessario. La configurazione della pipeline di creazione di immagini richiede tempo, infrastruttura e installazioni, ma con Image Builder per macchine virtuali di Azure è sufficiente specificare una semplice configurazione con la descrizione dell'immagine e inviarla al servizio che provvederà a compilare e distribuire l'immagine.
 
Image Builder per macchine virtuali di Azure (Azure Image Builder) consente di iniziare con un'immagine di Azure Marketplace basata su Windows o Linux, con immagini personalizzate o con file ISO di Red Hat Enterprise Linux (RHEL) e di aggiungere le proprie personalizzazioni. Poiché Image Builder è basato su [HashiCorp Packer](https://packer.io/), è anche possibile importare gli script di provisioning della shell Packer esistenti. È inoltre possibile specificare dove ospitare le immagini, ossia nella [raccolta immagini condivise](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), come immagine gestita o come disco rigido virtuale.

> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funzionalità di anteprima

Per l'anteprima, sono supportate queste funzionalità:

- Creazione di immagini valide di riferimento, con configurazioni minime di sicurezza e aziendali, personalizzabili dai reparti in base a specifiche esigenze.
- Applicazione di patch alle immagini esistenti: Image Builder consentirà di applicare continuamente patch alle immagini personalizzate esistenti.
- Connessione di Image Builder alle reti virtuali esistenti, per connettersi a server di configurazione (DSC, Chef, Puppet e così via), condivisioni file o altri server/servizi instradabili esistenti.
- Integrazione con la raccolta immagini condivise di Azure per distribuire, controllare le versioni e dimensionare le immagini a livello globale, con un sistema di gestione delle immagini.
- Integrazione con le pipeline di compilazione di immagini esistenti: è sufficiente chiamare Image Builder dalla pipeline oppure usare la semplice attività Azure DevOps di Image Builder disponibile in anteprima.
- Migrazione di una pipeline di personalizzazione di immagini esistente ad Azure. Uso di script, comandi e processi esistenti per personalizzare le immagini.
- Creazione di immagini in formato VHD per supportare Azure Stack.
 

## <a name="regions"></a>Regioni
Il servizio Azure Image Builder sarà disponibile per l'anteprima in queste aree. Le immagini possono essere distribuite al di fuori di queste aree.
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale

## <a name="os-support"></a>Sistemi operativi supportati
Image Builder supporta immagini dei sistemi operativi di base di Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multi-session/Professional
- Windows 2016
- Windows 2019

Gli ISO RHEL non sono più supportati.

## <a name="how-it-works"></a>Funzionamento


![Diagramma concettuale di Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Image Builder è un servizio di Azure completamente gestito accessibile tramite un provider di risorse di Azure. Il processo di Azure Image Builder prevede tre parti rappresentate in un modello, ovvero origine, personalizzazione e distribuzione. Il diagramma seguente illustra i componenti, con alcune delle relative proprietà. 
 


**Processo di Image Builder** 

![Diagramma concettuale del processo di Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Creare il modello di immagine come file JSON. Questo file JSON contiene informazioni sull'origine, le personalizzazioni e la distribuzione di immagini. Diversi esempi sono disponibili nel [repository GitHub di Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Inviarlo al servizio. Verrà creato un artefatto del modello di immagine nel gruppo di risorse specificato. In background, Image Builder scarica l'immagine di origine o l'ISO e gli script necessari. Questi vengono archiviati in un gruppo di risorse distinto creato automaticamente nella sottoscrizione, nel formato: IT_ \<DestinationResourceGroup> _ \<TemplateName> . 
1. Una volta creato il modello di immagine, è possibile compilare l'immagine. Nel generatore di immagini di sfondo usa il modello e i file di origine per creare una macchina virtuale (dimensioni predefinite: Standard_D1_v2), rete, IP pubblico, NSG e archiviazione \<DestinationResourceGroup> nel \<TemplateName> gruppo di risorse it_ _.
1. Nell'ambito della creazione dell'immagine, Image Builder distribuisce l'immagine in base al modello, quindi Elimina le risorse aggiuntive nel gruppo di \<DestinationResourceGroup> risorse it_ _ \<TemplateName> creato per il processo.


## <a name="permissions"></a>Autorizzazioni
Quando si esegue la registrazione in Azure Image Builder, si concede al servizio l'autorizzazione per creare, gestire ed eliminare un gruppo di risorse di staging (IT_*) e si acquisiscono i diritti per aggiungervi le risorse necessarie per la compilazione dell'immagine. Questo risultato si ottiene tramite un nome dell'entità servizio (SPN) di Azure Image Builder, reso disponibile nella sottoscrizione durante una registrazione completata.

Per consentire a Image Builder per macchine virtuali di Azure di distribuire immagini nelle immagini gestite o in una raccolta immagini condivise, sarà necessario creare un'identità assegnata dall'utente di Azure con le autorizzazioni di lettura e scrittura di immagini. Se si accede all'archiviazione di Azure, saranno necessarie le autorizzazioni per la lettura di contenitori privati.

Inizialmente è necessario seguire la documentazione per [creare un'identità gestita assegnata dall'utente di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli) per informazioni su come creare un'identità.

Una volta acquisita l'identità, è necessario concederle le autorizzazioni. A questo scopo, è possibile usare una definizione del ruolo personalizzata di Azure a cui assegnare l'identità gestita assegnata dall'utente.

Le autorizzazioni vengono descritte in maggior dettaglio [qui](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements), mentre gli esempi illustrano come vengono implementate.

> [!Note]
> In precedenza con Azure Image Builder era necessario usare il nome dell'entità servizio (SPN) di Azure Image Builder e assegnare le relative autorizzazioni al gruppo di risorse dell'immagine. Questo modello è stato abbandonato per consentire funzionalità future. A partire dal 26 maggio 2020, Image Builder non accetta modelli senza un'identità assegnata dall'utente. I modelli esistenti dovranno essere rinviati al servizio con un'[identità assegnata dall'utente](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity). Gli esempi di questo articolo illustrano come creare un'identità assegnata dall'utente e aggiungerla a un modello. Per altre informazioni, vedere la [documentazione](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) sulla modifica e sugli aggiornamenti delle versioni.

## <a name="costs"></a>Costi
Durante la creazione, la compilazione e l'archiviazione di immagini con Azure Image Builder si incorrerà in alcuni costi di calcolo, rete e archiviazione. Questi costi sono simili a quelli sostenuti per la creazione manuale di immagini personalizzate. Per le risorse, verranno addebitate le tariffe di Azure. 

Durante il processo di creazione dell'immagine, i file vengono scaricati e archiviati nel gruppo di risorse `IT_<DestinationResourceGroup>_<TemplateName>`, con un piccolo costo di archiviazione. Se non si vogliono mantenere questi file, eliminare il **modello di immagine** dopo la compilazione dell'immagine.
 
Image Builder crea una VM di dimensioni D1v2, oltre alle risorse di archiviazione e di rete necessarie. Queste risorse rimarranno disponibili per l'intero processo di compilazione e verranno eliminate quando Image Builder completa la creazione dell'immagine. 
 
Azure Image Builder distribuirà l'immagine nelle aree scelte, che potrebbero comportare addebiti in uscita dalla rete.

## <a name="hyper-v-generation"></a>Generazione di Hyper-V
Il generatore di immagini attualmente supporta solo in modo nativo la creazione di immagini di generazione Hyper-V (Gen1) 1 nella raccolta di immagini condivise (SIG) o nell'immagine gestita di Azure. Se si vuole creare immagini Gen2, è necessario usare un'immagine Gen2 di origine e distribuirla nel disco rigido virtuale. Successivamente, sarà necessario creare un'immagine gestita dal disco rigido virtuale e inserirla nel SIG come immagine Gen2.
 
## <a name="next-steps"></a>Passaggi successivi 
 
Per provare Azure Image Builder, vedere gli articoli per la compilazione di immagini [Linux](../articles/virtual-machines/linux/image-builder.md) o [Windows](../articles/virtual-machines/windows/image-builder.md).
 
