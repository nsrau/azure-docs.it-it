---
title: Esercitazione - Creare un gateway applicazione con regole di gestione basato su percorsi URL - Portale di Azure
description: Questa esercitazione illustra come creare regole di gestione basato su percorsi URL per un gateway applicazione e un set di scalabilità di macchine virtuali usando il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134798"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Esercitazione: Creare un gateway applicazione con regole di routing basato su percorsi usando il portale di Azure

È possibile usare il portale di Azure per configurare [regole di routing basato su percorsi URL](url-route-overview.md) quando si crea un [gateway applicazione](overview.md). In questa esercitazione si creano pool back-end usando macchine virtuali. Si creano quindi le regole di routing per garantire che il traffico Web raggiunga i server appropriati nei pool.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gateway applicazione
> * Creare macchine virtuali per i server back-end
> * Creare pool back-end con i server back-end
> * Creare un listener back-end
> * Creare una regola di routing basato su percorsi

![Esempio di routing basato su URL](./media/create-url-route-portal/scenario.png)

Se si preferisce, è possibile completare questa esercitazione usando l'[interfaccia della riga di comando di Azure](tutorial-url-route-cli.md) oppure [Azure PowerShell](tutorial-url-route-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create è necessaria una rete virtuale. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end. È possibile creare una rete virtuale durante la creazione del gateway applicazione.

1. Selezionare **Nuovo** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco In primo piano.
3. Immettere i valori seguenti per il gateway applicazione:

   - *myAppGateway* come nome del gateway applicazione.
   - *myResourceGroupAG* come nuovo gruppo di risorse.

     ![Creare il nuovo gateway applicazione](./media/create-url-route-portal/application-gateway-create.png)

4. Accettare i valori predefiniti per le altre impostazioni e quindi selezionare **OK**.
5. Selezionare **Scegliere una rete virtuale**, **Crea nuova** e quindi immettere i valori seguenti per la rete virtuale:

   - *myVNet* come nome della rete virtuale.
   - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
   - *myAGSubnet* come nome della subnet.
   - *10.0.0.0/24* come spazio indirizzi della subnet.

     ![Creare una rete virtuale](./media/create-url-route-portal/application-gateway-vnet.png)

6. Selezionare **OK** per creare la rete virtuale e la subnet.
7. Selezionare **Scegliere un indirizzo IP pubblico**, **Crea nuovo** e quindi immettere il nome dell'indirizzo IP pubblico. In questo esempio il nome dell'indirizzo IP pubblico è *myAGPublicIPAddress*. Accettare i valori predefiniti per le altre impostazioni e quindi selezionare **OK**.
8. Accettare i valori predefiniti per la configurazione del listener, lasciare disabilitato il web application firewall e quindi selezionare **OK**.
9. Rivedere le impostazioni nella pagina di riepilogo e quindi fare clic su **OK** per creare le risorse di rete e il gateway applicazione. La creazione del gateway applicazione potrebbe richiedere alcuni minuti. Attendere il completamento della distribuzione prima di passare alla sezione successiva.

### <a name="add-a-subnet"></a>Aggiungere una subnet

1. Selezionare **Tutte le risorse** nel menu a sinistra e quindi selezionare **myVNet** nell'elenco delle risorse.
2. Selezionare **Subnet** e quindi **Subnet**.

    ![Creare una subnet](./media/create-url-route-portal/application-gateway-subnet.png)

3. Immettere *myBackendSubnet* come nome della subnet e quindi selezionare **OK**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

In questo esempio vengono create tre macchine virtuali da usare come server back-end per il gateway applicazione. Viene anche installato IIS nelle macchine virtuali per verificare che il gateway applicazione sia stato creato correttamente.

1. Selezionare **Nuovo**.
2. Selezionare **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco In primo piano.
3. Immettere i valori seguenti per la macchina virtuale:

    - *myVM1* come nome della macchina virtuale.
    - *azureuser* come nome utente dell'amministratore.
    - *Azure123456!* come password.
    - Selezionare **Usa esistente** e quindi *myResourceGroupAG*.

4. Selezionare **OK**.
5. Selezionare **DS1_V2** come dimensioni per la macchina virtuale e quindi **Seleziona**.
6. Assicurarsi che **myVNet** sia selezionato per la rete virtuale e che la subnet sia **myBackendSubnet**. 
7. Selezionare **Disabilitato** per disabilitare la diagnostica di avvio.
8. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.

### <a name="install-iis"></a>Installare IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Aprire la shell interattiva e assicurarsi che sia impostata su **PowerShell**.

    ![Installare l'estensione personalizzata](./media/create-url-route-portal/application-gateway-extension.png)

2. Eseguire questo comando per installare IIS nella macchina virtuale: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Creare altre due macchine virtuali e installare IIS seguendo la procedura appena completata. Immettere *myVM2* e *myVM3* come nomi e valori di VMName in Set-AzVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Creare pool back-end con le macchine virtuali

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.
2. Selezionare **Pool back-end**. È stato creato automaticamente un pool predefinito con il gateway applicazione. Selezionare **appGatewayBackendPool**.
3. Selezionare **Aggiungi destinazione** per aggiungere *myVM1* a appGatewayBackendPool.

    ![Aggiungere i server back-end](./media/create-url-route-portal/application-gateway-backend.png)

4. Selezionare **Salva**.
5. Selezionare **Pool di back-end** e quindi **Aggiungi**.
6. Immettere il nome *imagesBackendPool* e aggiungere *myVM2* usando **Aggiungi destinazione**.
7. Selezionare **OK**.
8. Selezionare **Aggiungi** per aggiungere un altro pool di back-end denominato *videoBackendPool* e aggiungervi *myVM3*.

## <a name="create-a-backend-listener"></a>Creare un listener back-end

1. Selezionare **Listener** e quindi **Base**.
2. Immettere *myBackendListener* come nome, *myFrontendPort* come nome della porta front-end e quindi *8080* come porta del listener.
3. Selezionare **OK**.

## <a name="create-a-path-based-routing-rule"></a>Creare una regola di routing basato su percorsi

1. Selezionare **Regole** e quindi selezionare **Basata sul percorso**.
2. Immettere *rule2* come nome.
3. Immettere *Images* come nome del primo percorso. Immettere */images/*\* come percorso. Selezionare **imagesBackendPool** come pool back-end.
4. Immettere *Video* come nome del secondo percorso. Immettere */video/*\* come percorso. Selezionare **videoBackendPool** come pool back-end.

    ![Creare una regola basata sul percorso](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Selezionare **OK**.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Selezionare **Tutte le risorse** e quindi **myAGPublicIPAddress**.

    ![Registrare l'indirizzo IP pubblico del gateway applicazione](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Ad esempio, http://40.121.222.19.

    ![Testare l'URL di base nel gateway applicazione](./media/create-url-route-portal/application-gateway-iistest.png)

3. Modificare l'URL in http://&lt;ip-address&gt;:8080/images/test.htm sostituendo &lt;ip-address&gt; con l'indirizzo IP usato come nell'esempio seguente:

    ![Testare l'URL delle immagini nel gateway applicazione](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Modificare l'URL in http://&lt;ip-address&gt;:8080/video/test.htm sostituendo &lt;ip-address&gt; con l'indirizzo IP usato come nell'esempio seguente:

    ![Testare l'URL video nel gateway applicazione](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, rimuovere il gruppo di risorse. La rimozione del gruppo di risorse comporta anche la rimozione del gateway applicazione e di tutte le risorse correlate. 

Per rimuovere il gruppo di risorse:

1. Nel menu a sinistra del portale di Azure, selezionare **Gruppi di risorse**.
2. Nella pagina **Gruppo di risorse** cercare **myResourceGroupAG** nell'elenco e selezionarlo.
3. Nella pagina **Gruppo di risorse** selezionare **Elimina gruppo di risorse**.
4. Immettere *myResourceGroupAG* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulle operazioni che è possibile eseguire con il gateway applicazione di Azure](application-gateway-introduction.md)
