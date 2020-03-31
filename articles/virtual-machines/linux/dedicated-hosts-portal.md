---
title: Distribuire host dedicati di Azure tramite il portale di AzureDeploy Azure dedicated hosts using the Azure portal
description: Distribuire macchine virtuali in host dedicati usando il portale di Azure.Deploy VMs to dedicated hosts using the Azure portal.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086743"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Distribuire macchine virtuali in host dedicati tramite il portaleDeploy VMs to dedicated host using the portal

Questo articolo illustra come creare un [host dedicato di](dedicated-hosts.md) Azure per ospitare le macchine virtuali (VM). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
1. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace, cercare e selezionare **Ubuntu Server 16.04 LTS** di Canonical e quindi scegliere **Crea**.
1. Nella scheda **Nozioni di base,** in **Dettagli progetto,** verificare che sia selezionata la sottoscrizione corretta e quindi selezionare *myDedicatedHostsRG* come **gruppo di risorse**. 
1. Sotto **Dettagli dell'istanza**, digitare *myVM* come **nome della macchina virtuale** e scegliere *Stati Uniti orientali* come **Posizione**.
1. In **Opzioni disponibilità** selezionare Area di **disponibilità**, selezionare *1* dall'elenco a discesa.
1. Per le dimensioni, selezionare **Cambia dimensione**. Nell'elenco delle taglie disponibili, scegline una della serie Esv3, come **Standard E2s v3.** Potrebbe essere necessario cancellare il filtro per visualizzare tutte le dimensioni disponibili.
1. Sotto **Account amministratore**, selezionare **Chiave pubblica SSH**, digitare il nome utente, quindi incollare la chiave pubblica nella casella di testo. Rimuovere gli eventuali spazi iniziali o finali nella chiave pubblica.

    ![Account amministratore](./media/quick-create-portal/administrator-account.png)

1. In **Regole** > delle porte in ingresso**Porte pubbliche in ingresso**scegliere Consenti porte **selezionate** e quindi selezionare **SSH (22)** dall'elenco a discesa. 
1. Nella parte superiore della pagina selezionare la scheda **Avanzate** e nella sezione **Host** selezionare *myHostGroup* per **gruppo Host** e *myHost* per **host**. 
    ![Selezionare il gruppo host e l'host](./media/dedicated-hosts-portal/advanced.png)
1. Lasciare invariate le impostazioni predefinite rimanenti, quindi selezionare il pulsante **Rivedi + Crea** nella parte inferiore della pagina.
1. Quando viene visualizzato il messaggio che indica che la convalida è stata superata, selezionare **Crea**.

La distribuzione della macchina virtuale richiederà alcuni minuti.

## <a name="add-an-existing-vm"></a>Aggiungere una macchina virtuale esistenteAdd an existing VM 

È possibile aggiungere una macchina virtuale in uscita a un host dedicato, ma prima la macchina virtuale deve essere Stop-Deallocato. Prima di spostare una macchina virtuale in un host dedicato, assicurarsi che la configurazione della macchina virtuale sia supportata:Before you move a VM to a dedicated host, make sure that the VM configuration is supported:

- La dimensione della macchina virtuale deve essere nella stessa famiglia di dimensioni dell'host dedicato. Ad esempio, se l'host dedicato è DSv3, la dimensione della macchina virtuale potrebbe essere Standard_D4s_v3, ma non potrebbe essere un Standard_A4_v2. 
- La macchina virtuale deve trovarsi nella stessa area dell'host dedicato.
- La macchina virtuale non può far parte di un gruppo di posizionamento di prossimità. Rimuovere la macchina virtuale dal gruppo di posizionamento di prossimità prima di spostarla in un host dedicato. Per altre informazioni, vedere [Spostare una macchina virtuale da un gruppo di posizionamento di prossimitàFor](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group) more information, see Move a VM out of a proximity placement group
- La macchina virtuale non può essere in un set di disponibilità.
- Se la macchina virtuale si trova in una zona di disponibilità, deve essere la stessa zona di disponibilità del gruppo host. Le impostazioni della zona di disponibilità per la macchina virtuale e il gruppo host devono corrispondere.

Spostare la macchina virtuale in un host dedicato usando il [portale](https://portal.azure.com).

1. Aprire la pagina per la macchina virtuale.
1. Selezionare **Interrompi** per arrestare e deallocare la macchina virtuale.
1. Selezionare **Configurazione** dal menu a sinistra.
1. Selezionare un gruppo host e un host dai menu a discesa.
1. Al termine, selezionare **Salva** nella parte superiore della pagina.
1. Dopo aver aggiunto la macchina virtuale all'host, selezionare **Panoramica** dal menu a sinistra.
1. Nella parte superiore della pagina selezionare **Avvia** per riavviare la macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere la panoramica degli [host dedicati.](dedicated-hosts.md)

- È disponibile un modello di esempio, disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), che utilizza sia zone che domini di errore per la massima resilienza in un'area.

- È anche possibile distribuire un host dedicato usando l'interfaccia della riga di comando di [Azure.](dedicated-hosts-cli.md)



