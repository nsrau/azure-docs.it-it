---
title: Creare una raccolta di immagini condivise di Azure usando il portaleCreate an Azure Shared Image Gallery using the portal
description: Informazioni su come usare il portale di Azure per creare e condividere immagini di macchine virtuali.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 6273b58d9db53cfc4f6647885c70148982f0b950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975500"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Creare una raccolta di immagini condivise di Azure usando il portaleCreate an Azure Shared Image Gallery using the portal

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per l'avvio di attività di distribuzione, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

La raccolta di immagini condivise consente di condividere le immagini personalizzate delle macchine virtuali con altri all'interno dell'organizzazione, della propria area o tra aree e all'interno di un tenant AAD. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. È possibile creare più raccolte così da raggruppare in maniera logica le immagini condivise. 

La raccolta è una risorsa di primo livello che offre un completo controllo degli accessi in base al ruolo (RBAC). Le immagini possono essere con versioni ed è possibile scegliere di eseguire la replica di ogni versione dell'immagine in un diverso set di aree di Azure. La raccolta funziona solo con le immagini gestite.

La funzionalità di raccolta di immagini condivise presenta più tipi di risorse. Verranno usate o compilate le seguenti contenute in questo articolo:

| Risorsa | Descrizione|
|----------|------------|
| **Immagine gestita** | Immagine di base che può essere utilizzata da sola o utilizzata per creare una **versione dell'immagine** in una raccolta immagini. Le immagini gestite vengono create da macchine virtuali [generalizzate.](shared-image-galleries.md#generalized-and-specialized-images) Un'immagine gestita è un tipo speciale di disco rigido virtuale che può essere usato per creare più macchine virtuali e può ora essere sfruttato per creare versioni di immagini condivise. |
| **Snapshot** | Copia di un disco rigido virtuale che può essere utilizzata per creare una **versione dell'immagine.** Gli snapshot possono essere ricavati da una macchina virtuale [specializzata](shared-image-galleries.md#generalized-and-specialized-images) (non generalizzata) quindi usata da sola o con snapshot di dischi dati, per creare una versione di immagine specializzata.
| **Raccolta immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le immagini sono definite all'interno di una galleria e contengono informazioni sull'immagine e sui requisiti per il suo utilizzo all'interno dell'organizzazione. È possibile includere informazioni come se l'immagine è generalizzata o specializzata, il sistema operativo, i requisiti minimi e massimi di memoria e le note sulla versione. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |

<br>


> [!IMPORTANT]
> Le immagini specializzate sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitazioni note dell'anteprima** Le macchine virtuali possono essere create solo da immagini specializzate usando il portale o l'API. Il non è un supporto CLI o PowerShell per l'anteprima.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare l'esempio in questo articolo, è necessario disporre di un'immagine gestita esistente di una macchina virtuale generalizzata o di uno snapshot di una macchina virtuale specializzata. È possibile seguire [Esercitazione: Creare un'immagine personalizzata di una macchina virtuale di Azure con Azure PowerShell](tutorial-custom-images.md) per creare un'immagine gestita o [creare uno snapshot](snapshot-copy-managed-disk.md) per una macchina virtuale specializzata. Sia per le immagini gestite che per gli snapshot, le dimensioni del disco dati non possono essere superiori a 1 TB.

Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Creare VM

A questo punto è possibile creare una o più nuove macchine virtuali. In questo esempio viene creata una macchina virtuale denominata *myVM*, nel *myResourceGroup*, nel data center *degli Stati Uniti orientali.*

1. Vai alla definizione dell'immagine. È possibile utilizzare il filtro risorse per visualizzare tutte le definizioni di immagine disponibili.
1. Nella pagina per la definizione dell'immagine selezionare **Crea macchina virtuale** dal menu nella parte superiore della pagina.
1. Per **Gruppo di**risorse selezionare Crea **nuovo** e digitare *myResourceGroup* come nome.
1. In **Nome macchina virtuale**digitare *myVM*.
1. In **Area** selezionare *Stati Uniti orientali*.
1. Per **le opzioni di disponibilità**, lasciare l'impostazione predefinita *Nessuna ridondanza dell'infrastruttura richiesta*.
1. Il valore di **Immagine** viene `latest` compilato automaticamente con la versione dell'immagine se si è partiti dalla pagina per la definizione dell'immagine.
1. In **Dimensioni**scegliere una dimensione vm dall'elenco delle dimensioni disponibili e quindi scegliere **Seleziona**.
1. In **Account amministratore**, se l'immagine è stata generalizzata, è necessario fornire un nome utente, ad esempio *azureuser* e una password. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm). Se l'immagine è specializzata, i campi nome utente e password verranno visualizzati in grigio perché vengono utilizzati il nome utente e la password per la macchina virtuale di origine.
1. Se si desidera consentire l'accesso remoto alla macchina virtuale, in **Porte in ingresso pubbliche**scegliere Consenti porte **selezionate** e quindi selezionare **RDP (3389)** dall'elenco a discesa. Se non si desidera consentire l'accesso remoto alla macchina virtuale, lasciare selezionata l'opzione **Nessuno** per **Porte in ingresso pubbliche**.
1. Al termine, selezionare il pulsante **Revisione e creazione** nella parte inferiore della pagina.
1. Dopo che la macchina virtuale supera la convalida, selezionare **Crea** nella parte inferiore della pagina per avviare la distribuzione.


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina**, quindi confermare il nome del gruppo di risorse da eliminare.

Se si desidera eliminare singole risorse, è necessario eliminarle in ordine inverso. Ad esempio, per eliminare una definizione di immagine, è necessario eliminare tutte le versioni dell'immagine create da tale immagine.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Per altre informazioni sulle raccolte di immagini condivise, vedere [Panoramica](shared-image-galleries.md). Se si verificano problemi, vedere [Risoluzione dei problemi delle raccolte di immagini condivise](troubleshooting-shared-images.md).

