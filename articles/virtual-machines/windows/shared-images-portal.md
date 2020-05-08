---
title: Creare una raccolta di immagini condivise di Azure con il portale
description: Informazioni su come usare portale di Azure per creare e condividere immagini di macchine virtuali.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: e484cccb2dc15266fb7889c335a0acc981053e5c
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792140"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Creare una raccolta di immagini condivise di Azure con il portale

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per l'avvio di attività di distribuzione, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

La raccolta di immagini condivise consente di condividere le immagini personalizzate delle macchine virtuali con altri all'interno dell'organizzazione, della propria area o tra aree e all'interno di un tenant AAD. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. È possibile creare più raccolte così da raggruppare in maniera logica le immagini condivise. 

La raccolta è una risorsa di primo livello che offre un completo controllo degli accessi in base al ruolo (RBAC). Le immagini possono essere con versioni ed è possibile scegliere di eseguire la replica di ogni versione dell'immagine in un diverso set di aree di Azure. La raccolta funziona solo con le immagini gestite.

La funzionalità di raccolta di immagini condivise presenta più tipi di risorse. Verranno usate o compilate le seguenti contenute in questo articolo:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


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
1. Il valore per **Image** viene compilato automaticamente con la `latest` versione dell'immagine se è stato avviato dalla pagina per la definizione dell'immagine.
1. Per **dimensione**scegliere una dimensione di macchina virtuale dall'elenco delle dimensioni disponibili, quindi scegliere **Seleziona**.
1. In **account amministratore**, se l'immagine è stata generalizzata, è necessario specificare un nome utente, ad esempio *azureuser* e una password. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm). Se l'immagine è stata specializzata, i campi nome utente e password verranno disattivati perché vengono usati il nome utente e la password per la macchina virtuale di origine.
1. Se si vuole consentire l'accesso remoto alla macchina virtuale, in **porte in ingresso pubbliche**scegliere **Consenti porte selezionate** , quindi selezionare **RDP (3389)** nell'elenco a discesa. Se non si vuole consentire l'accesso remoto alla macchina virtuale, lasciare selezionata l'opzione **Nessuna** per le **porte in ingresso pubbliche**.
1. Al termine, selezionare il pulsante **Verifica e crea** nella parte inferiore della pagina.
1. Dopo che la macchina virtuale ha superato la convalida, selezionare **Crea** nella parte inferiore della pagina per avviare la distribuzione.


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina** e quindi confermare il nome del gruppo di risorse da eliminare.

Se si desidera eliminare singole risorse, è necessario eliminarle in ordine inverso. Per eliminare la definizione di un'immagine, ad esempio, è necessario eliminare tutte le versioni di immagine create da tale immagine.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Per altre informazioni sulle raccolte di immagini condivise, vedere [Panoramica](shared-image-galleries.md). Se si verificano problemi, vedere [Risoluzione dei problemi delle raccolte di immagini condivise](troubleshooting-shared-images.md).

