---
title: Configurare una rete virtuale per Azure DevTest Labs | Documentazione Microsoft
description: Informazioni su come configurare una rete virtuale esistente e una subnet e utilizzarle in una VM con Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 8c5ce9570541def245ca33f5841a8392e73569b4
ms.lasthandoff: 03/10/2017


---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configurare una rete virtuale per Azure DevTest Labs
Come illustrato nell'articolo [Aggiungere una VM con elementi a un lab](devtest-lab-add-vm-with-artifacts.md), quando si crea una VM in un lab, è possibile specificare una rete virtuale configurata. Questo potrebbe essere utile, ad esempio, per accedere alle risorse di rete aziendali dalle VM usando la rete virtuale configurata con ExpressRoute o VPN da sito a sito. Le sezioni seguenti illustrano come aggiungere la rete virtuale esistente alle impostazioni della rete virtuale di un lab in modo che sia disponibile al momento della creazione delle VM.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurare una rete virtuale per un lab con il portale di Azure
I passaggi seguenti descrivono la procedura per aggiungere una rete virtuale esistente (e una subnet) a un lab in modo che sia disponibile al momento della creazione di una VM nel lab. 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato. 
4. Nel pannello del lab selezionare **Configurazione**.
5. Nel pannello **Configurazione** del lab selezionare **Reti virtuali**.
6. Nel pannello **Reti virtuali** viene visualizzato l'elenco delle reti virtuali configurate per il lab corrente, come pure la rete virtuale predefinita creata per il lab. 
7. Selezionare **+ Aggiungi**.
   
    ![Aggiungere una rete virtuale esistente al lab](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. Nel pannello **Rete virtuale** selezionare **[Select virtual network]** (Seleziona rete virtuale).
   
    ![Selezionare una rete virtuale esistente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. Nel pannello **Scegli rete virtuale** selezionare la rete virtuale desiderata. Il pannello mostra tutte le reti virtuali presenti nella stessa area nella sottoscrizione del lab.  
10. Dopo aver selezionato una rete virtuale, viene visualizzato nuovamente il pannello **Rete virtuale**. Fare clic sulla subnet nell'elenco nella parte inferiore del pannello.

    ![Elenco di subnet](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Viene visualizzato il pannello Lab subnet (Subnet lab).

    ![Pannello Lab Subnet (Subnet lab)](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. Specificare un **nome per la subnet del lab**.
12. Per consentire l'uso di una subnet nella creazione della macchina virtuale del lab, selezionare **Use in virtual machine creation** (Usa nella creazione di macchine virtuali).
13. Per abilitare un [indirizzo IP pubblico condiviso](devtest-lab-shared-ip.md), selezionare **Enable shared public IP** (Abilita indirizzo IP pubblico condiviso).
14. Per consentire gli indirizzi IP pubblici in una subnet, selezionare **Allow public IP creation** (Consenti la creazione di un indirizzo IP pubblico).
15. Nel campo **Maximum virtual machines per user** (Numero massimo di macchine virtuali per utente) specificare per ogni utente il numero massimo di macchine virtuali per ogni subnet. Se si desidera un numero illimitato di VM, lasciare vuoto questo campo.
16. Selezionare **OK** per chiudere il pannello Lab Subnet (Subnet lab).
17. Selezionare **Salva** per chiudere il pannello Rete virtuale.
18. Dopo aver configurato la rete virtuale, è possibile selezionarla quando si crea una VM. 
    Per informazioni su come creare una VM e specificare una rete virtuale, vedere l'articolo [Aggiungere una VM con elementi a un lab](devtest-lab-add-vm-with-artifacts.md). 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere aggiunto la rete virtuale desiderata al lab, il passaggio successivo consiste nell' [aggiungere una VM a un lab](devtest-lab-add-vm-with-artifacts.md).


