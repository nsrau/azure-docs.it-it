---
title: Creare un gateway applicazione con regole di routing basato su percorsi URL - Portale di Azure | Microsoft Docs
description: "Informazioni su come creare regole di routing basato su percorsi URL per un gateway applicazione e un set di scalabilità di macchine virtuali usando il portale di Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: eb07b1811b017f71a003be26522e6b213a300321
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Creare un gateway applicazione con regole di routing basato su percorsi usando il portale di Azure

È possibile usare il portale di Azure per configurare [regole di routing basato su percorsi URL](application-gateway-url-route-overview.md) quando si crea un [gateway applicazione](application-gateway-introduction.md). In questa esercitazione si creano pool back-end usando macchine virtuali. Si creano quindi le regole di routing per garantire che il traffico Web raggiunga i server appropriati nei pool.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un gateway applicazione
> * Creare macchine virtuali per i server back-end
> * Creare pool back-end con i server back-end
> * Creare un listener back-end
> * Creare una regola di routing basato su percorsi

![Esempio di routing basato su URL](./media/application-gateway-create-url-route-portal/scenario.png)

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

    ![Creare il nuovo gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
5. Fare clic su **Scegliere una rete virtuale**, **Crea nuova** e quindi immettere i valori seguenti per la rete virtuale:

    - *myVNet* come nome della rete virtuale.
    - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
    - *myAGSubnet* come nome della subnet.
    - *10.0.0.0/24* come spazio indirizzi della subnet.

    ![Creare una rete virtuale](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. Fare clic su **OK** per creare la rete virtuale e la subnet.
7. Fare clic su **Scegliere un indirizzo IP pubblico**, **Crea nuovo** e quindi immettere il nome dell'indirizzo IP pubblico. In questo esempio il nome dell'indirizzo IP pubblico è *myAGPublicIPAddress*. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
8. Accettare i valori predefiniti per la configurazione del listener, lasciare disabilitato il web application firewall e quindi fare clic su **OK**.
9. Rivedere le impostazioni nella pagina di riepilogo e quindi fare clic su **OK** per creare le risorse di rete e il gateway applicazione. La creazione del gateway applicazione potrebbe richiedere alcuni minuti. Attendere il completamento della distribuzione prima di passare alla sezione successiva.

### <a name="add-a-subnet"></a>Aggiungere una subnet

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi su **myVNet** nell'elenco delle risorse.
2. Fare clic su **Subnet** e quindi su **Subnet**.

    ![Creare una subnet](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Immettere *myBackendSubnet* come nome della subnet e quindi fare clic su **OK**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

In questo esempio vengono create tre macchine virtuali da usare come server back-end per il gateway applicazione. È anche possibile installare IIS nelle macchine virtuali per verificare l'avvenuta creazione del gateway applicazione.

1. Fare clic su **Nuovo**.
2. Fare clic su **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco in primo piano.
3. Immettere i valori seguenti per la macchina virtuale:

    - *myVM1* come nome della macchina virtuale.
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

    ![Installare l'estensione personalizzata](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Eseguire questo comando per installare IIS nella macchina virtuale: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Creare altre due macchine virtuali e installare IIS seguendo la procedura appena completata. Immettere *myVM2* e *myVM3* come nomi e valori di VMName in Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Creare pool back-end con le macchine virtuali

1. Fare clic su **Tutte le risorse** e quindi su **myAppGateway**.
2. Fare clic su **Pool back-end**. È stato creato automaticamente un pool predefinito con il gateway applicazione. Fare clic su **appGatewayBackendPool**.
3. Fare clic su **Aggiungi destinazione** per aggiungere *myVM1* a appGatewayBackendPool.

    ![Aggiungere i server back-end](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Fare clic su **Save**.
5. Fare clic su **Pool back-end** e quindi su **Aggiungi**.
6. Immettere il nome *imagesBackendPool* e aggiungere *myVM2* usando **Aggiungi destinazione**.
7. Fare clic su **OK**.
8. Fare nuovamente clic su **Aggiungi** per aggiungere un altro pool back-end denominato *videoBackendPool* e aggiungervi *myVM3*.

## <a name="create-a-backend-listener"></a>Creare un listener back-end

1. Fare clic su **Listener** e quindi su **Basic**.
2. Immettere *myBackendListener* come nome, *myFrontendPort* come nome della porta front-end e quindi *8080* come porta del listener.
3. Fare clic su **OK**.

## <a name="create-a-path-based-routing-rule"></a>Creare una regola di routing basato su percorsi

1. Fare clic su **Regole** e quindi su **Basata sul percorso**.
2. Immettere *rule2* come nome.
3. Immettere *Images* come nome del primo percorso. Immettere */images/** come percorso. Selezionare **imagesBackendPool** come pool back-end.
4. Immettere *Video* come nome del secondo percorso. Immettere */video/** come percorso. Selezionare **videoBackendPool** come pool back-end.

    ![Creare una regola basata sul percorso](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Fare clic su **OK**.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Fare clic su **Tutte le risorse** e quindi su **myAGPublicIPAddress**.

    ![Registrare l'indirizzo IP pubblico del gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Ad esempio, http://http://40.121.222.19.

    ![Testare l'URL di base nel gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Modificare l'URL in http://&lt;ip-address&gt;:8080/video/test.htm sostituendo &lt;ip-address&gt; con l'indirizzo IP usato come nell'esempio seguente:

    ![Testare l'URL delle immagini nel gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Modificare l'URL in http://&lt;ip-address&gt;:8080/video/test.htm sostituendo &lt;ip-address&gt; con l'indirizzo IP usato come nell'esempio seguente:

    ![Testare l'URL video nel gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un gateway applicazione
> * Creare macchine virtuali per i server back-end
> * Creare pool back-end con i server back-end
> * Creare un listener back-end
> * Creare una regola di routing basato su percorsi

Per altre informazioni sui gateway applicazione e sulle risorse associate, continuare con le procedure dettagliate.