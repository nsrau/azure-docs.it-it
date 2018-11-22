---
title: 'Guida introduttiva: Indirizzare il traffico Web con un gateway applicazione di Azure - Portale di Azure | Microsoft Docs'
description: Informazioni su come usare il portale di Azure per creare un gateway applicazione di Azure che indirizza il traffico Web alle macchine virtuali in un pool back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6ad839b9cf1179e282b9163df5a38e13417408e2
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706229"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Guida introduttiva: Indirizzare il traffico Web con un gateway applicazione di Azure - Portale di Azure

Con il gateway applicazione di Azure è possibile indirizzare il traffico Web dell'applicazione a risorse specifiche assegnando listener alle porte, creando regole e aggiungendo risorse a un pool back-end.

Questa guida introduttiva illustra come usare il portale di Azure per creare rapidamente il gateway applicazione con due macchine virtuali nel relativo pool back-end. Il gateway verrà quindi testato per assicurarsi che funzioni correttamente.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create è necessaria una rete virtuale. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end. È possibile creare una rete virtuale durante la creazione del gateway applicazione.

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Fare clic su **Rete** e quindi su **Gateway applicazione** nell'elenco In primo piano.

### <a name="basics"></a>Nozioni di base

1. Immettere i valori seguenti per il gateway applicazione:

    - *myAppGateway* come nome del gateway applicazione.
    - *myResourceGroupAG* come nuovo gruppo di risorse.

    ![Creare il nuovo gateway applicazione](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.

### <a name="settings"></a>Impostazioni

1. Fare clic su **Scegliere una rete virtuale**, **Crea nuova** e quindi immettere i valori seguenti per la rete virtuale:

    - *myVNet* come nome della rete virtuale.
    - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
    - *myAGSubnet* come nome della subnet.
    - *10.0.0.0/24* come intervallo di indirizzi della subnet.

    ![Creare una rete virtuale](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Fare clic su **OK** per tornare alla pagina Impostazioni.
7. In **Configurazione dell'indirizzo IP front-end** verificare che **Tipo di indirizzo IP** sia impostato su **pubblico**, e in **Indirizzo IP pubblico** verificare che **Crea nuovo** sia selezionato. Digitare *myAGPublicIPAddress* come nome dell'indirizzo IP pubblico. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.

### <a name="summary"></a>Summary

Rivedere le impostazioni nella pagina di riepilogo e quindi fare clic su **OK** per creare la rete virtuale, l'indirizzo IP pubblico e il gateway applicazione. Il processo di creazione del gateway applicazione può richiedere alcuni minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

## <a name="add-a-subnet"></a>Aggiungere una subnet

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi su **myVNet** nell'elenco delle risorse.
2. Fare clic su **Subnet** e quindi su **+Subnet**.

    ![Creare una subnet](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Immettere *myBackendSubnet* come nome della subnet e quindi fare clic su **OK**.

## <a name="create-backend-servers"></a>Creare i server back-end

In questo esempio vengono create due macchine virtuali usate come server back-end per il gateway applicazione. È anche possibile installare IIS nelle macchine virtuali per verificare l'avvenuta creazione del gateway applicazione.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Fare clic su **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco In primo piano.
3. Immettere i valori seguenti per la macchina virtuale:

    - *myResourceGroupAG* come gruppo di risorse.
    - *myVM* come nome della macchina virtuale.
    - *azureuser* come nome utente dell'amministratore.
    - *Azure123456!* come password.

   Accettare tutte le altre impostazioni predefinite e fare clic su **Avanti: Dischi**.
4. Accettare le impostazioni predefinite del disco e fare clic su **Avanti: Rete**.
5. Assicurarsi che **myVNet** sia selezionato per la rete virtuale e che la subnet sia **myBackendSubnet**.
6. Accettare tutte le altre impostazioni predefinite e fare clic su **Avanti: Gestione**.
7. Fare clic su **Disattivato** per disabilitare la diagnostica di avvio. Accettare tutte le altre impostazioni predefinite e fare clic su **Rivedi e crea**.
8. Verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Create** (Crea).
9. Attendere il termine della creazione della macchina virtuale prima di continuare.

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

1. Fare clic su **Tutte le risorse** e quindi su **myAppGateway**.
4. Fare clic su **Pool back-end**. È stato creato automaticamente un pool predefinito con il gateway applicazione. Fare clic su **appGatewayBackendPool**.
5. In **Destinazioni** fare clic su **Indirizzo IP o nome di dominio completo** e selezionare **Macchina virtuale**.
6. In **Macchina virtuale** aggiungere le macchine virtuali myVM e myVM2 e le relative interfacce di rete associate.

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

> [!div class="nextstepaction"]
> [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)
