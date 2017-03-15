---
title: Configurare indirizzi IP privati per le VM (classiche) - Portale di Azure | Documentazione Microsoft
description: Informazioni su come configurare indirizzi IP privati per macchine virtuali (classiche) mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: bde6de3495c2909b63b1f85e420a4ff5e7ac2c1a
ms.lasthandoff: 02/28/2017


---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configurare indirizzi IP privati per una macchina virtuale (classica) mediante il portale di Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

In questo articolo viene illustrato il modello di distribuzione classica. È inoltre possibile [gestire un indirizzo IP statico privato nel modello di distribuzione di gestione delle risorse](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

La procedura di esempio seguente prevede che un ambiente semplice sia già creato. Se si desidera eseguire la procedura illustrata in questo documento, creare innanzitutto l'ambiente di prova descritto in [creare una rete virtuale](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale.
Per creare una VM denominata *DNS01* nella subnet *FrontEnd* di una rete virtuale denominata *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire la procedura seguente:

1. In un browser passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **NUOVO** > **Calcolo** > **Windows Server 2012 R2 Datacenter**; si noti che l'elenco **Selezionare un modello di distribuzione** mostra già **Classico**, quindi fare clic su **Crea**.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Nel pannello **Creare VM** , immettere il nome della macchina virtuale da creare (*DNS01* nello scenario), l'account amministratore locale e la password.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Fare clic su **Configurazione facoltativa** > **Rete** > **Rete virtuale**, poi fare clic su **TestVNet**. Se **TestVNet** non è disponibile, verificare che si utilizzi la posizione *Stati Uniti centrali* e di aver creato l'ambiente di prova descritto all'inizio di questo articolo.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Nel pannello **Rete** assicurarsi che la subnet attualmente selezionata sia *FrontEnd*, poi fare clic su **Indirizzi IP**, in **Assegnazione indirizzi IP** fare clic su **Statico** e immettere *192.168.1.101* per **Indirizzo IP** come indicato di seguito.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Fare clic su **OK** nel pannello **Indirizzi IP**, poi fare clic su **OK** nel pannello **Rete**, e fare clic su **OK** nel pannello **Configurazione facoltativa**.
7. Nel pannello **Creare VM** fare clic su **Crea**. Si noti il riquadro visualizzato di seguito nel dashboard.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare le informazioni relative all'indirizzo IP privato statico per una VM
Per visualizzare le informazioni dell’indirizzo IP privato statico per la macchina virtuale creata con la procedura descritta sopra, eseguire la procedura seguente.

1. Nel portale di Azure fare clic su **ESPLORA TUTTO** > **Macchine virtuali (classico)** > **DNS01** > **Tutte le impostazioni** > **Indirizzi IP** e notare l'assegnazione di indirizzi IP e l'indirizzo IP come indicato di seguito.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP statico privato dalla macchina virtuale creata in precedenza, attenersi alla procedura seguente.

1. Dal pannello **Indirizzi IP** mostrato in precedenza fare clic su **Dinamica** a destra di **Assegnazione indirizzi IP**, poi fare clic su **Salva** e su **Sì**.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP statico privato per la macchina virtuale creata in precedenza, attenersi alla procedura seguente:

1. Dal pannello **Indirizzi IP** mostrato in precedenza, fare clic su **Statica** a destra di **Assegnazione indirizzi IP**.
2. Digitare *192.168.1.101* per **indirizzo IP**, fare clic su **Salva**, quindi fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [indirizzi IP pubblici riservati](virtual-networks-reserved-public-ip.md) .
* Informazioni su [indirizzi IP pubblici a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Consultare le [API REST dell'indirizzo IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).


