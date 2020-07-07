---
title: Distribuire host dedicati di Azure tramite il portale
description: Distribuire le macchine virtuali in host dedicati usando il portale.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 53a9cd33f7bd2223be1c9f199b7a6d10d876c322
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82082935"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Distribuire macchine virtuali in host dedicati tramite il portale

Questo articolo illustra come creare un [host dedicato](dedicated-hosts.md) di Azure per ospitare le macchine virtuali (VM). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Nella pagina **nuovo** , in **Popular**, selezionare **Windows Server 2016 datacenter**.
1. Nella scheda **nozioni di base** , in **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta, quindi selezionare *myDedicatedHostsRG* come **gruppo di risorse**. 
1. Sotto **Dettagli dell'istanza**, digitare *myVM* come **nome della macchina virtuale** e scegliere *Stati Uniti orientali* come **Posizione**.
1. In **Opzioni di disponibilità** selezionare **zona di disponibilità**Selezionare *1* nell'elenco a discesa.
1. Per dimensioni selezionare **modifica dimensioni**. Nell'elenco delle dimensioni disponibili scegliere una dalla serie Esv3, ad esempio **E2S standard V3**. Potrebbe essere necessario cancellare il filtro per visualizzare tutte le dimensioni disponibili.
1. In **Account amministratore**, specificare un nome utente, ad esempio *azureuser*, e una password. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. In **Regole porta in ingresso** scegliere **Consenti porte selezionate**, quindi selezionare **RDP (3389)** dall'elenco a discesa.
1. Nella parte superiore della pagina **selezionare la scheda** **Avanzate** e nella sezione **host** Selezionare *myHostGroup* per **gruppo host** *e host host.* 
    ![Seleziona gruppo host e host](./media/dedicated-hosts-portal/advanced.png)
1. Lasciare invariate le impostazioni predefinite rimanenti, quindi selezionare il pulsante **Rivedi e crea** nella parte inferiore della pagina.
1. Quando viene visualizzato il messaggio che la convalida è stata superata, selezionare **Crea**.

## <a name="add-an-existing-vm"></a>Aggiungere una macchina virtuale esistente 

È possibile aggiungere una macchina virtuale esistente a un host dedicato, ma la VM deve essere prima di tutto Stop\Deallocated. Prima di spostare una macchina virtuale in un host dedicato, verificare che la configurazione della macchina virtuale sia supportata:

- Le dimensioni della macchina virtuale devono essere nella stessa famiglia di dimensioni dell'host dedicato. Se, ad esempio, l'host dedicato è DSv3, è possibile che le dimensioni della macchina virtuale siano Standard_D4s_v3, ma non una Standard_A4_v2. 
- La macchina virtuale deve trovarsi nella stessa area dell'host dedicato.
- La macchina virtuale non può far parte di un gruppo di posizionamento di prossimità. Rimuovere la macchina virtuale dal gruppo di posizionamento vicino prima di trasferirla in un host dedicato. Per altre informazioni, vedere [spostare una macchina virtuale da un gruppo di posizionamento vicino](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- La macchina virtuale non può trovarsi in un set di disponibilità.
- Se la macchina virtuale si trova in una zona di disponibilità, deve corrispondere alla stessa zona di disponibilità del gruppo host. Le impostazioni della zona di disponibilità per la macchina virtuale e il gruppo host devono corrispondere.

Spostare la macchina virtuale in un host dedicato usando il [portale](https://portal.azure.com).

1. Aprire la pagina per la macchina virtuale.
1. Selezionare **Arresta** per stop\deallocate la macchina virtuale.
1. Scegliere **configurazione** dal menu a sinistra.
1. Selezionare un gruppo host e un host nei menu a discesa.
1. Al termine, selezionare **Salva** nella parte superiore della pagina.
1. Dopo che la macchina virtuale è stata aggiunta all'host, scegliere **Panoramica** dal menu a sinistra.
1. Nella parte superiore della pagina selezionare **Avvia** per riavviare la macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere la panoramica [Host dedicati](dedicated-hosts.md). 

- È disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) un modello di esempio che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È inoltre possibile distribuire un host dedicato utilizzando [Azure PowerShell](dedicated-hosts-powershell.md).
