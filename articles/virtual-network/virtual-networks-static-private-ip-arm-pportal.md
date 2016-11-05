---
title: Come impostare un indirizzo IP statico privato in modalità ARM usando il portale di Azure | Microsoft Docs
description: Informazioni sugli IP privati (DIP) e istruzioni per gestirli in modalità ARM tramite il portale di Azure
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial

---
# Come impostare un indirizzo IP statico privato nel portale di Azure
[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Questo articolo illustra il modello di distribuzione Gestione risorse. È anche possibile [gestire un indirizzo IP statico privato nel modello di distribuzione classico](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

La procedura di esempio seguente prevede che un ambiente semplice sia già creato. Se si desidera eseguire la procedura illustrata in questo documento, creare innanzitutto l'ambiente di prova descritto in [creare una rete virtuale](virtual-networks-create-vnet-arm-pportal.md).

## Come creare una VM per il test degli indirizzi IP statici privati
Non è possibile impostare un indirizzo IP statico privato durante la creazione di una VM in modalità di distribuzione di Gestione risorse tramite il portale di Azure. È necessario creare innanzitutto la VM, quindi impostare il relativo IP privato come statico.

Per creare una VM denominata *DNS01* nel subnet *FrontEnd* di una VNet denominata *TestVNet*, attenersi alla procedura riportata di seguito.

1. Da un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **NUOVO** > **Calcolo** > **Windows Server 2012 R2 Datacenter**, si noti che l’elenco **Selezionare un modello di distribuzione** mostra già **Gestione risorse**, quindi fare clic su **Crea**, come illustrato nella figura che segue.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Nel pannello **Nozioni di base**, immettere il nome della VM da creare (*DNS01* nello scenario), l'account amministratore locale e la password, come illustrato nella figura che segue.
   
    ![Pannello Nozioni di base](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Assicurarsi che la **Località** selezionata sia *USA centrale*, quindi fare clic su **Seleziona esistente** in **Gruppo di risorse**, quindi fare clic nuovamente su **Gruppo di risorse**, quindi fare clic su *TestRG* e infine su **OK**.
   
    ![Pannello Nozioni di base](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Nel pannello **Scegli una dimensione** selezionare **Standard A1** e quindi fare clic su **Seleziona**.
   
    ![Pannello Scegliere una dimensione](./media/virtual-networks-static-ip-arm-pportal/figure04.png)
6. Nel pannello **Impostazioni**, assicurarsi che siano impostate le seguenti proprietà con i valori riportati di seguito, quindi fare clic su **OK**.
   
    -**Account di archiviazione**: *vnetstorage*
   
   * **Rete**: *TestVNet*
   * **Subnet**: *FrontEnd*
     
     ![Pannello Scegliere una dimensione](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  
7. Nel pannello **Riepilogo**, fare clic su **OK**. Si noti il riquadro visualizzato di seguito nel dashboard.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## Come recuperare le informazioni relative all'indirizzo IP privato statico per una VM
Per visualizzare le informazioni dell’indirizzo IP privato statico per la macchina virtuale creata con la procedura descritta sopra, eseguire la procedura seguente.

1. Dal portale di Azure fare clic su **Esplora tutto** > **Macchine virtuali** > **DNS01** > **Tutte le impostazioni** > **Interfacce di rete** e quindi fare clic sull'unica interfaccia di rete elencata.
   
    ![Distribuzione di un riquadro VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)  
2. Nel pannello **Interfaccia di rete**, fare clic su **Tutte le impostazioni** > **Indirizzi IP** e notare i valori di **Assegnazione** e **Indirizzo IP**.
   
    ![Distribuzione di un riquadro VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)  

## Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP statico privato per la VM creata in precedenza, attenersi alla procedura seguente:

1. Dal pannello **Indirizzi IP** mostrato in precedenza, fare clic su **Statico**in **Assegnazione**.
2. Digitare *192.168.1.101* per **Indirizzo IP** e quindi fare clic su **Salva**.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Se dopo aver fatto clic su **Salva ** si nota che l'assegnazione è ancora impostato su **Dinamico**, significa che l'indirizzo IP immesso è già in uso. Provare un indirizzo IP diverso.
> 
> 

## Come rimuovere un indirizzo IP statico privato da una VM
Per rimuovere l'indirizzo IP statico privato dalla VM creata in precedenza, attenersi alla procedura seguente.

1. Dal pannello **Indirizzi IP** mostrato in precedenza, fare clic su **Dinamico** in **Assegnazione** e quindi fare clic su **Salva**.

## Passaggi successivi
* Informazioni su [indirizzi IP pubblici riservati](virtual-networks-reserved-public-ip.md).
* Informazioni su [indirizzi IP pubblici a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md).
* Consultare le [API REST dell'indirizzo IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0810_2016-->