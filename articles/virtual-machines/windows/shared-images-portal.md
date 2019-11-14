---
title: Creare immagini di macchine virtuali di Azure condivise per Windows tramite il portale
description: Informazioni su come usare portale di Azure per creare e condividere immagini di macchine virtuali.
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
ms.openlocfilehash: 0823942964f2bdb9f943d6eb778a3132574af0f7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065540"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Creare una raccolta di immagini condivise usando il portale di Azure

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per l'avvio di attività di distribuzione, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

La raccolta di immagini condivise consente di condividere le immagini personalizzate delle macchine virtuali con altri all'interno dell'organizzazione, della propria area o tra aree e all'interno di un tenant AAD. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. È possibile creare più raccolte così da raggruppare in maniera logica le immagini condivise. 

La raccolta è una risorsa di primo livello che offre un completo controllo degli accessi in base al ruolo (RBAC). Le immagini possono essere con versioni ed è possibile scegliere di eseguire la replica di ogni versione dell'immagine in un diverso set di aree di Azure. La raccolta funziona solo con le immagini gestite.

La funzionalità di raccolta di immagini condivise presenta più tipi di risorse. Verranno usate o compilate le seguenti contenute in questo articolo:

| Risorsa | DESCRIZIONE|
|----------|------------|
| **Immagine gestita** | Un'immagine di base che può essere usata da sola o usata per creare una **versione dell'immagine** in una raccolta di immagini. Le immagini gestite vengono create da VM [generalizzate](shared-image-galleries.md#generalized-and-specialized-images) . Un'immagine gestita è un tipo speciale di disco rigido virtuale che può essere usato per creare più macchine virtuali e può ora essere sfruttato per creare versioni di immagini condivise. |
| **Snapshot** | Copia di un disco rigido virtuale che può essere utilizzato per creare una **versione dell'immagine**. Gli snapshot possono essere ricavati da una VM [specializzata](shared-image-galleries.md#generalized-and-specialized-images) (uno che non è stato generalizzato), quindi usati singolarmente o con snapshot di dischi dati, per creare una versione di immagine specializzata.
| **Raccolta di immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le immagini vengono definite all'interno di una raccolta e contengono informazioni sull'immagine e sui requisiti per l'uso all'interno dell'organizzazione. È possibile includere informazioni come se l'immagine sia generalizzata o specializzata, il sistema operativo, i requisiti di memoria minimi e massimi e le note sulla versione. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |

<br>


> [!IMPORTANT]
> Le immagini specializzate sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitazioni di anteprima note** Le macchine virtuali possono essere create solo da immagini specializzate usando il portale o l'API. Non è disponibile l'interfaccia della riga di comando o il supporto PowerShell per l'anteprima.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare l'esempio in questo articolo, è necessario disporre di un'immagine gestita esistente di una macchina virtuale generalizzata o di uno snapshot di una macchina virtuale specializzata. È possibile seguire l' [esercitazione: creare un'immagine personalizzata di una macchina virtuale di Azure con Azure PowerShell](tutorial-custom-images.md) per creare un'immagine gestita o [creare uno snapshot](snapshot-copy-managed-disk.md) per una macchina virtuale specializzata. Per le immagini e gli snapshot gestiti, le dimensioni del disco dati non possono superare 1 TB.

Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Creare VM

A questo punto è possibile creare una o più nuove macchine virtuali. Questo esempio crea una macchina virtuale denominata *myVM*, in *myResourceGroup*, nel Data Center *degli Stati Uniti orientali* .

1. Passare alla definizione dell'immagine. È possibile usare il filtro delle risorse per mostrare tutte le definizioni di immagine disponibili.
1. Nella pagina relativa alla definizione dell'immagine selezionare **Crea macchina virtuale** dal menu nella parte superiore della pagina.
1. Per **gruppo di risorse**selezionare **Crea nuovo** e digitare *myResourceGroup* per nome.
1. In **nome macchina virtuale**digitare *myVM*.
1. In **Area** selezionare *Stati Uniti orientali*.
1. Per le **Opzioni di disponibilità**, lasciare l'impostazione predefinita *nessuna ridondanza dell'infrastruttura richiesta*.
1. Il valore per **Image** viene compilato automaticamente con la versione dell'immagine `latest` se è stato avviato dalla pagina per la definizione dell'immagine.
1. Per **dimensione**scegliere una dimensione di macchina virtuale dall'elenco delle dimensioni disponibili, quindi scegliere **Seleziona**.
1. In **account amministratore**, se l'immagine è stata generalizzata, è necessario specificare un nome utente, ad esempio *azureuser* e una password. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm). Se l'immagine è stata specializzata, i campi nome utente e password verranno disattivati perché vengono usati il nome utente e la password per la macchina virtuale di origine.
1. Se si vuole consentire l'accesso remoto alla macchina virtuale, in **porte in ingresso pubbliche**scegliere **Consenti porte selezionate** , quindi selezionare **RDP (3389)** nell'elenco a discesa. Se non si vuole consentire l'accesso remoto alla macchina virtuale, lasciare selezionata l'opzione **Nessuna** per le **porte in ingresso pubbliche**.
1. Al termine, selezionare il pulsante **Verifica e crea** nella parte inferiore della pagina.
1. Dopo che la macchina virtuale ha superato la convalida, selezionare **Crea** nella parte inferiore della pagina per avviare la distribuzione.


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina** e quindi confermare il nome del gruppo di risorse da eliminare.

Se si desidera eliminare singole risorse, è necessario eliminarle in ordine inverso. Per eliminare la definizione di un'immagine, ad esempio, è necessario eliminare tutte le versioni di immagine create da tale immagine.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale da una versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Per altre informazioni sulle raccolte di immagini condivise, vedere [Panoramica](shared-image-galleries.md). Se si verificano problemi, vedere [Risoluzione dei problemi delle raccolte di immagini condivise](troubleshooting-shared-images.md).

