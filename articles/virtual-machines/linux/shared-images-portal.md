---
title: Creare immagini di macchine virtuali di Azure condivise per Linux usando il portale | Microsoft Docs
description: Informazioni su come usare portale di Azure per creare e condividere immagini di macchine virtuali.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 8be4890f01ae2c0d893bb7c45f29c6f8178844f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082121"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Creare una raccolta di immagini condivise usando il portale di Azure

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per l'avvio di attività di distribuzione, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

La raccolta di immagini condivise consente di condividere le immagini personalizzate delle macchine virtuali con altri all'interno dell'organizzazione, della propria area o tra aree e all'interno di un tenant AAD. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. È possibile creare più raccolte così da raggruppare in maniera logica le immagini condivise. 

La raccolta è una risorsa di primo livello che offre un completo controllo degli accessi in base al ruolo (RBAC). Le immagini possono essere con versioni ed è possibile scegliere di eseguire la replica di ogni versione dell'immagine in un diverso set di aree di Azure. La raccolta funziona solo con le immagini gestite.

La funzionalità di raccolta di immagini condivise presenta più tipi di risorse. Verranno usate o compilate le seguenti contenute in questo articolo:

| Risorsa | Descrizione|
|----------|------------|
| **Immagine gestita** | Si tratta di un'immagine di base che può essere usata da sola o per creare una **versione dell'immagine** in una raccolta di immagini. Le immagini gestite vengono create da macchine virtuali generalizzate. Un'immagine gestita è un tipo speciale di disco rigido virtuale che può essere usato per creare più macchine virtuali e può ora essere sfruttato per creare versioni di immagini condivise. |
| **Raccolta di immagini** | Come in Azure Marketplace, una **raccolta di immagini** è un repository per la gestione e la condivisione delle immagini, ma è possibile controllare chi ha accesso. |
| **Definizione delle immagini** | Le immagini vengono definite all'interno di una raccolta e forniscono informazioni sull'immagine e sui requisiti per il suo uso interno. Questa include il fatto che l'immagine è per Windows o Linux, le note sulla versione e i requisiti minimi e massimi di memoria. Si tratta della definizione di un tipo di immagine. |
| **Versione dell'immagine** | La **versione dell'immagine** è ciò che si usa per creare una macchina virtuale quando si usa una raccolta. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Come un'immagine gestita, quando si usa una **versione dell'immagine** per creare una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte. |


## <a name="before-you-begin"></a>Prima di iniziare

Per completare l'esempio in questo articolo, è necessario disporre di un'immagine gestita esistente. Per crearne una, se necessario, è possibile seguire questa [Esercitazione: Creare un'immagine personalizzata di una macchina virtuale di Azure con Azure PowerShell](tutorial-custom-images.md). Se l'immagine gestita contiene un disco dati, le dimensioni del disco dati non possono superare 1 TB.

Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>Creare macchine virtuali da un'immagine

Dopo aver completato la versione dell'immagine, è possibile creare una o più nuove macchine virtuali. 

> [!IMPORTANT]
> Non è possibile usare il portale per distribuire una macchina virtuale da un'immagine in un altro tenant di Azure. Per creare una macchina virtuale da un'immagine condivisa tra i tenant, è necessario usare l'interfaccia della riga di comando di [Azure](shared-images.md#create-a-vm) o [PowerShell](../windows/shared-images.md#create-vms-from-an-image).


Questo esempio crea una VM denominata *myVMfromImage* in *myResourceGroup* nel datacenter *Stati Uniti orientali*.

1. Nella pagina per la versione dell'immagine selezionare **Crea macchina virtuale** dal menu nella parte superiore della pagina.
1. Per **gruppo di risorse**selezionare **Crea nuovo** e digitare *myResourceGroup* per nome.
1. In **nome macchina virtuale**digitare *myVM*.
1. Per **area**selezionare *Stati Uniti orientali*.
1. Per le **Opzioni di disponibilità**, lasciare l'impostazione predefinita nessuna ridondanza dell' *infrastruttura richiesta*.
1. Il valore per l' **immagine** deve essere compilato automaticamente se è stato avviato dalla pagina per la versione dell'immagine.
1. Per **dimensione**scegliere una dimensione di macchina virtuale dall'elenco delle dimensioni disponibili, quindi fare clic su "Seleziona".
1. In **account amministratore**selezionare **password** o **chiave pubblica SSH**, quindi immettere le informazioni.
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
- [Creare una macchina virtuale da una versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Per altre informazioni sulle raccolte di immagini condivise, vedere [Panoramica](shared-image-galleries.md). Se si verificano problemi, vedere [Risoluzione dei problemi delle raccolte di immagini condivise](troubleshooting-shared-images.md).

