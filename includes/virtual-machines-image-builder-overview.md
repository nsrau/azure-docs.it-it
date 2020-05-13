---
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 11a9b8609218a6cf56a789b18094d048e26d4af8
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343338"
---
Le immagini di macchine virtuali (VM) standardizzate consentono alle organizzazioni di eseguire la migrazione al cloud e garantire la coerenza nelle distribuzioni. Le immagini includono in genere impostazioni di configurazione e sicurezza predefinite e il software necessario. Per configurare la pipeline di imaging, è necessario disporre di tempo, infrastruttura e configurazione, ma con generatore di immagini di VM di Azure è sufficiente fornire una semplice configurazione che descriva l'immagine, la invii al servizio e l'immagine venga compilata e distribuita.
 
Il generatore di immagini di macchine virtuali di Azure (Generatore di immagini di Azure) consente di iniziare con un'immagine di Azure Marketplace basata su Windows o Linux, immagini personalizzate esistenti o Red Hat Enterprise Linux (RHEL) ISO e iniziare ad aggiungere personalizzazioni personalizzate. Poiché il generatore di immagini si basa su [HashiCorp Packer](https://packer.io/), è anche possibile importare gli script di provisioning della shell di Packer esistenti. È anche possibile specificare la posizione in cui si desidera che le immagini siano ospitate, nella [raccolta di immagini condivise di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)come immagine gestita o VHD.

> [!IMPORTANT]
> Azure Image Builder è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funzionalità di anteprima

Per l'anteprima, queste funzionalità sono supportate:

- Creazione di immagini di base di riferimento, che include la sicurezza e le configurazioni aziendali minime, e consentire ai reparti di personalizzarla ulteriormente per le proprie esigenze.
- Con l'applicazione di patch alle immagini esistenti, Image Builder consentirà di applicare continuamente patch alle immagini personalizzate esistenti.
- Connetti Image Builder alle tue reti virtuali esistenti, per consentirti di connetterti a server di configurazione esistenti (DSC, chef, Puppet e così via), condivisioni file o qualsiasi altro server/servizio instradabile.
- L'integrazione con la raccolta di immagini condivise di Azure consente di distribuire, applicare la versione e ridimensionare le immagini a livello globale e fornisce un sistema di gestione delle immagini.
- Per l'integrazione con le pipeline di compilazione di immagini esistenti, è sufficiente chiamare Image Builder dalla pipeline o usare l'attività di Azure DevOps per il generatore di immagini di anteprima semplice.
- Eseguire la migrazione di una pipeline di personalizzazione immagini esistente in Azure. USA gli script, i comandi e i processi esistenti per personalizzare le immagini.
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

## <a name="os-support"></a>Supporto del sistema operativo
AIB supporterà le immagini del sistema operativo di base di Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multisessione/Professional
- Windows 2016
- Windows 2019

Il supporto di RHEL ISOs non è più supportato.
## <a name="how-it-works"></a>Come funziona


![Disegno concettuale di Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder è un servizio di Azure completamente gestito accessibile da un provider di risorse di Azure. Il processo di generatore di immagini di Azure è costituito da tre parti principali: origine, personalizzazione e distribuzione. queste sono rappresentate in un modello. Il diagramma seguente illustra i componenti, con alcune delle relative proprietà. 
 


**Processo del generatore di immagini** 

![Disegno concettuale del processo di Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Creare il modello di immagine come file con estensione JSON. Questo file con estensione JSON contiene informazioni sull'origine, le personalizzazioni e la distribuzione dell'immagine. Nel [repository GitHub di Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)sono disponibili diversi esempi.
1. Inviarlo al servizio. verrà creato un elemento del modello di immagine nel gruppo di risorse specificato. In background, Image Builder Scarica l'immagine di origine o l'ISO e gli script in base alle esigenze. Questi vengono archiviati in un gruppo di risorse distinto creato automaticamente nella sottoscrizione, nel formato: IT_ \< DestinationResourceGroup>_ \< templatename>. 
1. Una volta creato il modello di immagine, è possibile compilare l'immagine. Nel generatore di immagini di sfondo usa il modello e i file di origine per creare una macchina virtuale (dimensioni predefinite: Standard_D1_v2), rete, IP pubblico, NSG e archiviazione nel \< gruppo di risorse it_ DestinationResourceGroup>_ \< templatename>.
1. Nell'ambito della creazione dell'immagine, Image Builder distribuisce l'immagine in base al modello, quindi Elimina le risorse aggiuntive nel IT_ \< DestinationResourceGroup>_ \< templatename> gruppo di risorse creato per il processo.


## <a name="permissions"></a>Autorizzazioni
Quando si esegue la registrazione per il (AIB), viene concessa l'autorizzazione del servizio AIB per creare, gestire ed eliminare un gruppo di risorse di gestione temporanea (IT_ *) e si dispone dei diritti per l'aggiunta di risorse, necessarie per la compilazione dell'immagine. Questa operazione viene eseguita da un nome dell'entità servizio (SPN) AIB reso disponibile nella sottoscrizione durante una registrazione corretta.

Per consentire al generatore di immagini di macchine virtuali di Azure di distribuire immagini nelle immagini gestite o in una raccolta di immagini condivise, è necessario creare un'identità Azure assegnata dall'utente che disponga delle autorizzazioni per la lettura e la scrittura di immagini. Se si accede ad archiviazione di Azure, sarà necessario disporre delle autorizzazioni per leggere i contenitori privati.

Inizialmente è necessario creare la documentazione di [identità gestita assegnata dall'utente di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli) per informazioni su come creare un'identità.

Quando si ha l'identità necessaria per concedere le autorizzazioni, è possibile usare una definizione di ruolo personalizzata di Azure e quindi assegnare l'identità gestita assegnata dall'utente per usare la definizione di ruolo personalizzata.

Le autorizzazioni sono illustrate in modo [più dettagliato e](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)negli esempi viene illustrato come viene implementato.

> [Nota] In precedenza con AIB, si utilizzerebbe il nome SPN AIB e si concedono le autorizzazioni SPN ai gruppi di risorse immagine. Si sta uscendo da questo modello per consentire le funzionalità future. Dal 1 ° giugno 2020, Image Builder non accetterà i modelli che non hanno un'identità assegnata dall'utente. i modelli esistenti dovranno essere inviati nuovamente al servizio con un' [identità utente](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity). Gli esempi qui illustrano già come creare un'identità assegnata dall'utente e aggiungerle a un modello.

## <a name="costs"></a>Costi
Durante la creazione, la compilazione e l'archiviazione di immagini con Azure Image Builder si incorreranno costi di calcolo, di rete e di archiviazione. Questi costi sono simili ai costi sostenuti per la creazione manuale di immagini personalizzate. Per le risorse, ti verranno addebitate le tariffe di Azure. 

Durante il processo di creazione dell'immagine, i file vengono scaricati e archiviati nel `IT_<DestinationResourceGroup>_<TemplateName>` gruppo di risorse, che comporterà un piccolo costo di archiviazione. Se non si desidera conservarli, eliminare il modello di **immagine** dopo la compilazione dell'immagine.
 
Image Builder crea una VM usando le dimensioni della macchina virtuale D1v2 e l'archiviazione e la rete necessarie per la macchina virtuale. Queste risorse dureranno per la durata del processo di compilazione e verranno eliminate al termine della creazione dell'immagine da parte del generatore. 
 
Azure Image Builder distribuirà l'immagine nelle aree scelte, che potrebbero comportare addebiti in uscita dalla rete.
 
## <a name="next-steps"></a>Passaggi successivi 
 
Per provare Azure Image Builder, vedere gli articoli per la compilazione di immagini [Linux](../articles/virtual-machines/linux/image-builder.md) o [Windows](../articles/virtual-machines/windows/image-builder.md) .
 
