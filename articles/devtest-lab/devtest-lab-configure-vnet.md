---
title: Configurare una rete virtuale per Azure DevTest Labs | Documentazione Microsoft
description: Informazioni su come configurare una rete virtuale esistente e una subnet e utilizzarle in una VM con Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: v-craic
ms.openlocfilehash: 21daa8ff756ee30c6d454d49af7db20afe488c47
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurare una rete virtuale per Azure DevTest Labs
Come illustrato nell'articolo [Aggiungere una macchina virtuale a un lab](devtest-lab-add-vm.md), quando si crea una macchina virtuale in un lab è possibile specificare una rete virtuale configurata. Potrebbe essere necessario, ad esempio, accedere alle risorse di rete aziendali dalle macchine virtuali usando la rete virtuale configurata con ExpressRoute o VPN da sito a sito.

Questo articolo illustra come aggiungere la rete virtuale esistente alle impostazioni della rete virtuale di un lab in modo che sia disponibile al momento della creazione delle macchine virtuali.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurare una rete virtuale per un lab con il portale di Azure
I passaggi seguenti descrivono la procedura per aggiungere una rete virtuale esistente (e una subnet) a un lab in modo che sia disponibile al momento della creazione di una VM nel lab. 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
1. Nell'elenco dei lab selezionare il lab desiderato. 
1. Nel riquadro principale del lab selezionare **Configurazione e criteri**.

    ![Accedere alla configurazione e ai criteri del lab](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Nella sezione **RISORSE ESTERNE** selezionare **Reti virtuali**. Viene visualizzato un elenco delle reti virtuali configurate per il lab corrente, come pure la rete virtuale predefinita creata per il lab. 
1. Selezionare **+ Aggiungi**.
   
    ![Aggiungere una rete virtuale esistente al lab](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Nel riquadro **Rete virtuale** selezionare **[Seleziona una rete virtuale]**.
   
    ![Selezionare una rete virtuale esistente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Nel riquadro **Scegli rete virtuale** selezionare la rete virtuale desiderata. Viene visualizzato un elenco di tutte le reti virtuali presenti nella stessa area nella sottoscrizione del lab.
1. Dopo aver selezionato una rete virtuale, viene visualizzato nuovamente il riquadro **Rete virtuale**. Selezionare la subnet nell'elenco riportato in basso.

    ![Elenco di subnet](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Viene visualizzato il riquadro Subnet del lab.

    ![Riquadro Subnet del lab](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Specificare un **nome per la subnet del lab**.
   - Per consentire l'uso di una subnet nella creazione della macchina virtuale del lab, selezionare **Use in virtual machine creation** (Usa nella creazione di macchine virtuali).
   - Per abilitare un [indirizzo IP pubblico condiviso](devtest-lab-shared-ip.md), selezionare **Enable shared public IP** (Abilita indirizzo IP pubblico condiviso).
   - Per consentire gli indirizzi IP pubblici in una subnet, selezionare **Allow public IP creation** (Consenti la creazione di un indirizzo IP pubblico).
   - Nel campo **Maximum virtual machines per user** (Numero massimo di macchine virtuali per utente) specificare per ogni utente il numero massimo di macchine virtuali per ogni subnet. Se si desidera un numero illimitato di VM, lasciare vuoto questo campo.
1. Selezionare **OK** per chiudere il riquadro Subnet del lab.
1. Selezionare **Salva** per chiudere il riquadro Rete virtuale.

Dopo aver configurato la rete virtuale, è possibile selezionarla quando si crea una VM. Per informazioni su come creare una VM e specificare una rete virtuale, vedere l'articolo [Aggiungere una VM a un lab](devtest-lab-add-vm.md). 

La [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network) di Azure fornisce altre informazioni sull'uso delle reti virtuali, tra cui come impostare e gestire una rete virtuale e connetterla alla rete locale.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere aggiunto la rete virtuale desiderata al lab, il passaggio successivo consiste nell' [aggiungere una VM a un lab](devtest-lab-add-vm.md).

