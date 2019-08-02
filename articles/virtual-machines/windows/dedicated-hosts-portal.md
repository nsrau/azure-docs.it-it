---
title: Distribuire host dedicati di Azure usando il portale di Azure | Microsoft Docs
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
ms.openlocfilehash: 0a3cb76d1e28559727957c1007b0992fb8e90f2b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700561"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Anteprima: Distribuire macchine virtuali in host dedicati tramite il portale

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
1. Nella parte superiore della pagina selezionare la scheda **Avanzate** e nella sezione **host** Selezionare *myHostGroup* per **gruppo host** *e host* **host.** 
    ![Seleziona gruppo host e host](./media/dedicated-hosts-portal/advanced.png)
1. Lasciare invariate le impostazioni predefinite rimanenti, quindi selezionare il pulsante **Rivedi + Crea** nella parte inferiore della pagina.
1. Quando viene visualizzato il messaggio che la convalida è stata superata, selezionare **Crea**.


## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere Cenni preliminari sugli [host dedicati](dedicated-hosts.md) . 

- È [disponibile un](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)modello di esempio, che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È inoltre possibile distribuire un host dedicato utilizzando [Azure PowerShell](dedicated-hosts-powershell.md).