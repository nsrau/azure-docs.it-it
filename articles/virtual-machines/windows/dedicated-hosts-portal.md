---
title: Distribuire host dedicati di Azure usando il portale di Azure
description: Distribuire le macchine virtuali in host dedicati usando il portale di Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/26/2019
ms.author: cynthn
ms.openlocfilehash: 7abfa00b01f2e5e32b2486d69f36ac62f844c5ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033846"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Anteprima: distribuire macchine virtuali in host dedicati usando il portale

Questo articolo illustra come creare un [host dedicato](dedicated-hosts.md) di Azure per ospitare le macchine virtuali (VM). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Nella pagina **Nuovo**, in **Più comuni**, selezionare **Windows Server 2016 Datacenter**.
1. Nella scheda **nozioni di base** , in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta, quindi selezionare *myDedicatedHostsRG* come **gruppo di risorse**. 
1. Sotto **Dettagli dell'istanza**, digitare *myVM* come **nome della macchina virtuale** e scegliere *Stati Uniti orientali* come **Posizione**.
1. In **Opzioni di disponibilità** selezionare **zona di disponibilità**Selezionare *1* nell'elenco a discesa.
1. Per dimensioni selezionare **modifica dimensioni**. Nell'elenco delle dimensioni disponibili scegliere una dalla serie Esv3, ad esempio **E2S standard V3**. Potrebbe essere necessario cancellare il filtro per visualizzare tutte le dimensioni disponibili.
1. Sotto **Account amministratore**, specificare un nome utente, ad esempio *azureuser*, e una password. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. In **Regole porta in ingresso** scegliere **Consenti porte selezionate**, quindi selezionare **RDP (3389)** dall'elenco a discesa.
1. Nella parte superiore della pagina **selezionare la scheda** **Avanzate** e nella sezione **host** Selezionare *myHostGroup* per **gruppo host** *e host host.* 
    ![selezionare il gruppo host e l'host](./media/dedicated-hosts-portal/advanced.png)
1. Lasciare invariate le impostazioni predefinite rimanenti, quindi selezionare il pulsante **Rivedi + Crea** nella parte inferiore della pagina.
1. Quando viene visualizzato il messaggio che la convalida è stata superata, selezionare **Crea**.


## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere Cenni preliminari sugli [host dedicati](dedicated-hosts.md) . 

- È disponibile un [modello di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È inoltre possibile distribuire un host dedicato utilizzando [Azure PowerShell](dedicated-hosts-powershell.md).