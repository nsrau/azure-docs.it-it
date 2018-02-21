---
title: Configurare indirizzi IP privati per le VM - Portale di Azure | Microsoft Docs
description: Informazioni su come configurare indirizzi IP privati per le VM mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 672462fad715758e50680fa5bade4b1f9d50e6e5
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Configurare indirizzi IP privati per una VM mediante il portale di Azure

> [!div class="op_single_selector"]
> * [Azure portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-networks-static-private-ip-arm-cli.md)
> * [Portale di Azure (classico)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (Classic)](virtual-networks-static-private-ip-classic-ps.md) (PowerShell (classico))
> * [Interfaccia della riga di comando di Azure (versione classica)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Questo articolo illustra il modello di distribuzione Gestione risorse. È anche possibile [gestire un indirizzo IP statico privato nel modello di distribuzione classico](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

La procedura di esempio seguente prevede che un ambiente semplice sia già creato. Se si desidera eseguire la procedura illustrata in questo documento, creare innanzitutto l'ambiente di prova descritto in [creare una rete virtuale](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Come creare una VM per il test degli indirizzi IP statici privati
Non è possibile impostare un indirizzo IP statico privato durante la creazione di una VM in modalità di distribuzione di Gestione risorse tramite il portale di Azure. È necessario creare innanzitutto la VM, quindi impostare il relativo IP privato come statico.

Per creare una VM denominata *DNS01* nel subnet *FrontEnd* di una VNet denominata *TestVNet*, seguire questa procedura.

1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **NUOVO** > **Calcolo** > **Windows Server 2012 R2 Datacenter** (notare che l’elenco **Selezionare un modello di distribuzione** mostra già **Gestione risorse**) e quindi fare clic su **Crea**, come mostrato nella figura seguente.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Nel pannello **Nozioni di base** , immettere il nome della VM da creare (*DNS01* nello scenario), l'account amministratore locale e la password, come illustrato nella figura che segue.
   
    ![Pannello Nozioni di base](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Assicurarsi che la **Località** selezionata sia *Stati Uniti centrali*, fare clic su **Seleziona esistente** in **Gruppo di risorse**, fare clic nuovamente su **Gruppo di risorse**, fare clic su *TestRG* e infine fare clic su **OK**.
   
    ![Pannello Nozioni di base](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Nel pannello **Scegli una dimensione** selezionare **Standard A1** e quindi fare clic su **Seleziona**.
   
    ![Pannello Scegliere una dimensione](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. Nel pannello **Impostazioni** assicurarsi che siano impostate le proprietà seguenti con i valori riportati di seguito, quindi fare clic su **OK**.
   
    -**Account di archiviazione**: *vnetstorage*
   
   * **Rete**: *TestVNet*
   * **Subnet**: *FrontEnd*
     
     ![Pannello Scegliere una dimensione](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Nel pannello **Riepilogo** fare clic su **OK**. Si noti il riquadro visualizzato di seguito nel dashboard.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare le informazioni relative all'indirizzo IP privato statico per una VM
Per visualizzare le informazioni dell’indirizzo IP privato statico per la macchina virtuale creata con la procedura descritta sopra, eseguire la procedura seguente.

1. Dal portale di Azure fare clic su **Esplora tutto** > **Macchine virtuali** > **machinesDNS01All** > **Tutte le impostazioni** > **Interfacce di rete** e quindi fare clic sull'unica interfaccia di rete elencata.
   
    ![Distribuzione di un riquadro VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. Nel pannello **Interfaccia di rete** fare clic su **Tutte le impostazioni** > **Indirizzi IP** e notare i valori di **Assegnazione** e **Indirizzo IP**.
   
    ![Distribuzione di un riquadro VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP statico privato per la macchina virtuale creata in precedenza, attenersi alla procedura seguente:

1. Dal pannello **Indirizzi IP** mostrato in precedenza, fare clic su **Statico** in **Assegnazione**.
2. Digitare *192.168.1.101* per **Indirizzo IP** e quindi fare clic su **Salva**.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Se dopo aver fatto clic su **Salva** si nota che l'assegnazione è ancora impostata su **Dinamico**, significa che l'indirizzo IP immesso è già in uso. Provare un indirizzo IP diverso.
> 
> 

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP privato statico dalla VM creata in precedenza, attenersi alla procedura seguente:

Dal pannello **Indirizzi IP** mostrato in precedenza, fare clic su **Dinamico** in **Assegnazione** e quindi fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [indirizzi IP pubblici riservati](virtual-networks-reserved-public-ip.md) .
* Informazioni su [indirizzi IP pubblici a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Consultare le [API REST dell'indirizzo IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).

