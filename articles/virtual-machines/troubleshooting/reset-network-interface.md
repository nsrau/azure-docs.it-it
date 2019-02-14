---
title: Come reimpostare l'interfaccia di rete per la VM Windows Azure | Microsoft Docs
description: Illustra come reimpostare l'interfaccia di rete per la VM Windows Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 3a8e005f8678deef9fc4aebd2d620619fe6074bc
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982880"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Come reimpostare l'interfaccia di rete per la VM Windows di Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Questo articolo illustra come reimpostare l'interfaccia di rete per la macchina virtuale Windows di Azure per risolvere i problemi quando non è possibile connettersi alla macchina virtuale (VM) Windows di Microsoft Azure dopo:

* La disabilitazione dell'interfaccia di rete (NIC) predefinita 
* L'impostazione manuale di un IP statico per la scheda di interfaccia di rete 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Reimpostare l'interfaccia di rete

### <a name="for-vms-deployed-in-resource-group-model"></a>Per le VM distribuite nel modello di gruppo di risorse

1.  Accedere al [portale di Azure](https://ms.portal.azure.com).
2.  Selezionare la macchina virtuale.
3.  Selezionare **Rete** e quindi l'interfaccia di rete della macchina virtuale.

    ![Percorso dell'interfaccia di rete](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Selezionare **Configurazioni IP**.
5.  Selezionare l'indirizzo IP. 
6.  Se l'impostazione **Assegnazione IP privato** non è **Statica**, cambiarla in **Statica**.
7.  Cambiare l'**Indirizzo IP** in un altro indirizzo IP disponibile nella subnet.
8. La macchina virtuale verrà riavviata per inizializzare la nuova scheda NIC al sistema.
9.  Provare a eseguire RDP al computer in uso. Se ha esito positivo, è possibile modificare l'indirizzo IP privato originale se si vuole. In caso contrario è possibile mantenerlo. 

#### <a name="use-azure-powershell"></a>Usare Azure PowerShell

1. Verificare che sia installata [la versione più recente di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Aprire una sessione di Azure PowerShell con privilegi elevati (Esegui come amministratore). Eseguire i comandi seguenti:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Provare a eseguire RDP al computer in uso.  Se ha esito positivo, è possibile modificare l'indirizzo IP privato originale se si vuole. In caso contrario è possibile mantenerlo.

### <a name="for-classic-vms"></a>Per VM classiche

Per reimpostare l'interfaccia di rete, seguire questi passaggi:

#### <a name="use-azure-portal"></a>Usare il portale di Azure

1.  Accedere al [portale di Azure]( https://ms.portal.azure.com).
2.  Selezionare **Macchine virtuali (classiche)**.
3.  Selezionare la macchina virtuale.
4.  Selezionare **Indirizzi IP**.
5.  Se l'impostazione **Assegnazione IP privato** non è **Statica**, cambiarla in **Statica**.
6.  Cambiare l'**Indirizzo IP** in un altro indirizzo IP disponibile nella subnet.
7.  Selezionare **Salva**.
8.  La macchina virtuale verrà riavviata per inizializzare la nuova scheda NIC al sistema.
9.  Provare a eseguire RDP al computer in uso. Se l'operazione ha esito positivo, è possibile scegliere di ripristinare l'indirizzo IP privato originale.  

#### <a name="use-azure-powershell"></a>Usare Azure PowerShell

1. Verificare che sia installata [la versione più recente di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Aprire una sessione di Azure PowerShell con privilegi elevati (Esegui come amministratore). Eseguire i comandi seguenti:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Provare a eseguire RDP al computer in uso. Se ha esito positivo, è possibile modificare l'indirizzo IP privato originale se si vuole. In caso contrario è possibile mantenerlo. 

## <a name="delete-the-unavailable-nics"></a>Eliminare le schede di interfaccia non disponibili
Dopo essere riusciti a eseguire una connessione desktop remoto al computer, è necessario eliminare le schede NIC precedenti per evitare il problema potenziale:

1.  Aprire Gestione dispositivi.
2.  Selezionare **Vista** > **Mostra dispositivi nascosti**.
3.  Selezionare **Schede di rete**. 
4.  Controllare le schede di rete con un nome simile a "Scheda di rete Microsoft Hyper-V".
5.  Si potrebbe vedere una scheda non disponibile che appare in grigio. Fare clic con pulsante destro del mouse sulla scheda e selezionare Disinstalla.

    ![immagine della scheda NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Disinstallare solo le schede non disponibili con il nome "Scheda di rete Microsoft Hyper-V". Se si disinstalla una delle altre schede nascoste, ciò potrebbe provocare altri problemi.
    >
    >

6.  Se l'operazione è riuscita, tutte le schede non disponibili sono state eliminate dal sistema.
