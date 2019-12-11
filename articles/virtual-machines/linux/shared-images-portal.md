---
title: Creare immagini di VM di Azure condivise per Linux tramite il portale
description: Informazioni su come usare portale di Azure per creare e condividere immagini di macchine virtuali.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 44df85a94ad26d3abcd48f01c31e7aa093c1123f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978697"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Creare una raccolta di immagini condivise di Azure con il portale

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per l'avvio di attività di distribuzione, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

La raccolta di immagini condivise consente di condividere le immagini personalizzate delle macchine virtuali con altri all'interno dell'organizzazione, della propria area o tra aree e all'interno di un tenant AAD. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. È possibile creare più raccolte così da raggruppare in maniera logica le immagini condivise. 

La raccolta è una risorsa di primo livello che offre un completo controllo degli accessi in base al ruolo (RBAC). Le immagini possono essere con versioni ed è possibile scegliere di eseguire la replica di ogni versione dell'immagine in un diverso set di aree di Azure. La raccolta funziona solo con le immagini gestite.

La funzionalità di raccolta di immagini condivise presenta più tipi di risorse. Verranno usate o compilate le seguenti contenute in questo articolo:

| Gruppi | Description|
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

Per completare l'esempio in questo articolo, è necessario disporre di un'immagine gestita esistente di una macchina virtuale generalizzata o di uno snapshot di una macchina virtuale specializzata. È possibile seguire l' [esercitazione: creare un'immagine personalizzata di una macchina virtuale di Azure con Azure PowerShell](tutorial-custom-images.md) per creare un'immagine gestita o [creare uno snapshot](../windows/snapshot-copy-managed-disk.md) per una macchina virtuale specializzata. Per le immagini e gli snapshot gestiti, le dimensioni del disco dati non possono superare 1 TB.

Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Creare VM 

A questo punto è possibile creare una o più nuove macchine virtuali. Questo esempio crea una VM denominata *myVMfromImage* in *myResourceGroup* nel datacenter *Stati Uniti orientali*.

1. Passare alla definizione dell'immagine. È possibile usare il filtro delle risorse per mostrare tutte le definizioni di immagine disponibili.
1. Nella pagina relativa alla definizione dell'immagine selezionare **Crea macchina virtuale** dal menu nella parte superiore della pagina.
1. Per **gruppo di risorse**selezionare **Crea nuovo** e digitare *myResourceGroup* per nome.
1. In **nome macchina virtuale**digitare *myVM*.
1. In **Area** selezionare *Stati Uniti orientali*.
1. Per le **Opzioni di disponibilità**, lasciare l'impostazione predefinita *nessuna ridondanza dell'infrastruttura richiesta*.
1. Il valore per **Image** viene compilato automaticamente con la versione dell'immagine `latest` se è stato avviato dalla pagina per la definizione dell'immagine.
1. Per **dimensione**scegliere una dimensione di macchina virtuale dall'elenco delle dimensioni disponibili, quindi scegliere **Seleziona**.
1. In **account amministratore**, se la VM di origine è stata generalizzata, immettere il **nome utente** e la **chiave pubblica SSH**. Se la macchina virtuale di origine è specializzata, queste opzioni verranno disattivate perché vengono usate le informazioni della VM di origine.
1. Se si vuole consentire l'accesso remoto alla macchina virtuale, in **porte in ingresso pubbliche**scegliere **Consenti porte selezionate** , quindi selezionare **SSH (22)** nell'elenco a discesa. Se non si vuole consentire l'accesso remoto alla macchina virtuale, lasciare selezionata l'opzione **Nessuna** per le **porte in ingresso pubbliche**.
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
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Per altre informazioni sulle raccolte di immagini condivise, vedere [Panoramica](shared-image-galleries.md). Se si verificano problemi, vedere [Risoluzione dei problemi delle raccolte di immagini condivise](troubleshooting-shared-images.md).

