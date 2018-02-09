---
title: Creare un gateway applicazione - Portale di Azure| Microsoft Docs
description: Informazioni su come creare un gateway applicazione usando il portale di Azure.
services: application-gateway
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: df9235bc7ff61943de52a0bcc4064bf9fab6636a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Creare un gateway applicazione con il portale di Azure

È possibile usare il portale di Azure per creare o gestire gateway applicazione. In questa guida introduttiva viene illustrato come creare risorse di rete, server back-end e un gateway applicazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create è necessaria una rete virtuale. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end. È possibile creare una rete virtuale durante la creazione del gateway applicazione.

1. Fare clic su **Nuovo** nell'angolo in alto a sinistra nel portale di Azure.
2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco In primo piano.
3. Immettere i valori seguenti per il gateway applicazione:

    - *myAppGateway* come nome del gateway applicazione.
    - *myResourceGroupAG* come nuovo gruppo di risorse.

    ![Creare il nuovo gateway applicazione](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
5. Fare clic su **Scegliere una rete virtuale**, **Crea nuova** e quindi immettere i valori seguenti per la rete virtuale:

    - *myVNet* come nome della rete virtuale.
    - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
    - *myAGSubnet* come nome della subnet.
    - *10.0.0.0/24* come spazio indirizzi della subnet.

    ![Creare una rete virtuale](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Fare clic su **OK** per creare la rete virtuale e la subnet.
6. Fare clic su **Scegliere un indirizzo IP pubblico**, **Crea nuovo** e quindi immettere il nome dell'indirizzo IP pubblico. In questo esempio il nome dell'indirizzo IP pubblico è *myAGPublicIPAddress*. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
8. Accettare i valori predefiniti per la configurazione del listener, lasciare disabilitato il web application firewall e quindi fare clic su **OK**.
9. Rivedere le impostazioni nella pagina di riepilogo e quindi fare clic su **OK** per creare la rete virtuale, l'indirizzo IP pubblico e il gateway applicazione. La creazione del gateway applicazione potrebbe richiedere alcuni minuti. Attendere il completamento della distribuzione prima di passare alla sezione successiva.

### <a name="add-a-subnet"></a>Aggiungere una subnet

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi su **myVNet** nell'elenco delle risorse.
2. Fare clic su **Subnet** e quindi su **Subnet**.

    ![Creare una subnet](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Immettere *myBackendSubnet* come nome della subnet e quindi fare clic su **OK**.

## <a name="create-backend-servers"></a>Creare i server back-end

In questo esempio vengono create due macchine virtuali da usare come server back-end per il gateway applicazione. È anche possibile installare IIS nelle macchine virtuali per verificare l'avvenuta creazione del gateway applicazione.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Fare clic su **Nuovo**.
2. Fare clic su **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco In primo piano.
3. Immettere i valori seguenti per la macchina virtuale:

    - *myVM* come nome della macchina virtuale.
    - *azureuser* come nome utente dell'amministratore.
    - *Azure123456!* come password.
    - Selezionare **Usa esistente** e quindi *myResourceGroupAG*.

4. Fare clic su **OK**.
5. Selezionare **DS1_V2** come dimensioni per la macchina virtuale e fare clic su **Seleziona**.
6. Assicurarsi che **myVNet** sia selezionato per la rete virtuale e che la subnet sia **myBackendSubnet**. 
7. Fare clic su **Disabilitato** per disabilitare la diagnostica di avvio.
8. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Crea**.

### <a name="install-iis"></a>Installare IIS

1. Aprire la shell interattiva e assicurarsi che sia impostata su **PowerShell**.

    ![Installare l'estensione personalizzata](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Eseguire questo comando per installare IIS nella macchina virtuale: 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Creare una seconda macchina virtuale e installare IIS seguendo la procedura appena completata. Immettere *myVM2* per il nome e per VMName in Set-AzureRmVMExtension.

### <a name="add-backend-servers"></a>Aggiungere i server back-end

3. Fare clic su **Tutte le risorse** e quindi su **myAppGateway**.
4. Fare clic su **Pool back-end**. È stato creato automaticamente un pool predefinito con il gateway applicazione. Fare clic su **appGatewayBackendPool**.
5. Fare clic su **Aggiungi destinazione** per aggiungere ogni macchina virtuale creata al pool back-end.

    ![Aggiungere i server back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Fare clic su **Save**.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Individuare l'indirizzo IP pubblico del gateway applicazione nella schermata Panoramica. Fare clic su **Tutte le risorse** e quindi su **myAGPublicIPAddress**.

    ![Registrare l'indirizzo IP pubblico del gateway applicazione](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

    ![Testare il gateway applicazione](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, eliminare il gruppo di risorse, il gateway applicazione e tutte le risorse correlate. A questo scopo, selezionare il gruppo di risorse che contiene il gateway applicazione e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati creati un gruppo di risorse, le risorse di rete e i server back-end. Tali risorse sono state quindi usate per creare un gateway applicazione. Per altre informazioni sui gateway applicazione e sulle risorse associate, continuare con le procedure dettagliate.
