---
title: Esercitazione - Creare un gateway applicazione con regole di gestione basato su percorsi URL - Portale di Azure
description: Questa esercitazione illustra come creare regole di gestione basato su percorsi URL per un gateway applicazione e un set di scalabilità di macchine virtuali usando il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: victorh
ms.openlocfilehash: 2cb21eb98e698ab44d73ada195fdcb7d7aac8839
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844644"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Esercitazione: Creare un gateway applicazione con regole di routing basato su percorsi usando il portale di Azure

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Creare macchine virtuali

In questo esempio vengono create tre macchine virtuali da usare come server back-end per il gateway applicazione. È anche possibile installare IIS nelle macchine virtuali per verificare il corretto funzionamento del gateway applicazione.

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Selezionare **Windows Server 2016 Datacenter** nell'elenco Più comuni.
3. Immettere i valori seguenti per la macchina virtuale:

    - **Gruppo di risorse**, selezionare **Crea nuovo** e quindi digitare *myResourceGroupAG*.
    - **Nome della macchina virtuale**: *myVM1*
    - **Area**: *(Stati Uniti) Stati Uniti orientali*
    - **Nome utente**: *azureuser*
    - **Password**: *Azure123456!*


4. Selezionare **Next:Disks**.
5. Selezionare **Passaggio successivo: Rete**
6. Per **Rete virtuale** selezionare **Crea nuova** e quindi immettere i valori seguenti per la rete virtuale:

   - *myVNet* come nome della rete virtuale.
   - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
   - *myBackendSubnet* come nome della prima subnet
   - *10.0.1.0/24* come spazio indirizzi della subnet.
   - *myAGSubnet* come nome della seconda subnet.
   - *10.0.0.0/24* come spazio indirizzi della subnet.
7. Selezionare **OK**.

8. Assicurarsi che in **Interfaccia di rete** sia selezionato il valore **myBackendSubnet** per la subnet e quindi selezionare **Passaggio successivo: Gestione**.
9. Selezionare **Disattivato** per disabilitare la diagnostica di avvio.
10. Fare clic su **Rivedi e crea**, esaminare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.
11. Creare altre due macchine virtuali, *myVM2* e *myVM3*, e inserirle nella rete virtuale *MyVNet* e nella subnet *myBackendSubnet*.

### <a name="install-iis"></a>Installare IIS

1. Aprire la shell interattiva e assicurarsi che sia impostata su **PowerShell**.

    ![Installare l'estensione personalizzata](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Eseguire questo comando per installare IIS nella macchina virtuale: 

    ```azurepowershell
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

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

1. Selezionare **Crea una risorsa** nel menu a sinistra del portale di Azure. Verrà visualizzata la finestra **Nuovo**.

2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco **In primo piano**.

### <a name="basics-tab"></a>Scheda Informazioni di base

1. Nella scheda **Informazioni di base** immettere questi valori per le impostazioni del gateway applicazione seguenti:

   - **Gruppo di risorse**: selezionare **myResourceGroupAG** come gruppo di risorse.
   - **Nome del gateway applicazione**: immettere *myAppGateway* come nome del gateway applicazione.
   - **Area**: selezionare **(Stati Uniti) Stati Uniti orientali**.

        ![Creare il nuovo gateway applicazione: Nozioni di base](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  In **Configura la rete virtuale** selezionare **myVNet** come nome della rete virtuale.
3. Selezionare **myAGSubnet** come subnet.
3. Accettare i valori predefiniti per le altre impostazioni e quindi selezionare **Passaggio successivo: Front-end**.

### <a name="frontends-tab"></a>Scheda Front-end

1. Nella scheda **Front- end** verificare che **Tipo di indirizzo IP front-end** sia impostato su **Pubblico**.

   > [!NOTE]
   > Per lo SKU v2 del gateway applicazione, è possibile scegliere solo la configurazione **pubblica** dell'IP front-end. La configurazione di indirizzi IP front-end privati non è attualmente abilitata per questo SKU v2.

2. Scegliere **Crea nuovo** per **Indirizzo IP pubblico** e immettere *myAGPublicIPAddress* per il nome dell'indirizzo IP pubblico, quindi selezionare **OK**. 
3. Selezionare **Avanti: Back-end**.

### <a name="backends-tab"></a>Scheda Back-end

Il pool back-end viene usato per instradare le richieste ai server back-end che gestiscono la richiesta. I pool back-end possono essere costituiti da schede di interfaccia di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come Servizio app di Azure.

1. Nella scheda **Back- end** selezionare **Aggiungi un pool back-end**.

2. Nella finestra **Aggiungi un pool back-end** visualizzata immettere i valori seguenti per creare un pool back-end vuoto:

    - **Nome**: immettere *myBackendPool* come nome del pool back-end.
3. In **Destinazioni back-end**, **Tipo di destinazione** selezionare **Macchina virtuale** dall'elenco a discesa.

5. In **Destinazione** selezionare l'interfaccia di rete per **myVM1**.
6. Selezionare **Aggiungi**.
7. Ripetere l'operazione per aggiungere un pool back-end *Images* con *myVM2* come destinazione e un pool back-end *Video* con *myVM3* come destinazione.
8. Selezionare **Aggiungi** per salvare la configurazione del pool back-end e tornare alla scheda **Back-end**.

4. Nella scheda **Back-end** selezionare **Passaggio successivo: Configurazione**.

### <a name="configuration-tab"></a>Scheda Configurazione

Nella scheda **Configurazione** verranno connessi il front-end e il pool back-end creati tramite una regola di routing.

1. Selezionare **Aggiungi una regola** nella colonna **Regole di routing**.

2. Nella finestra **Aggiungi una regola di routing** visualizzata immettere *myRoutingRule* per **Nome regola**.

3. Una regola di routing richiede un listener. Nella scheda **Listener** nella finestra **Aggiungi una regola di routing** immettere i valori seguenti per il listener:

    - **Nome listener**: immettere *myListener* per il nome del listener.
    - **IP front-end**: selezionare **Pubblico** per scegliere l'indirizzo IP pubblico creato per il front-end.
    - **Porta**: Tipo *8080*
  
        Accettare i valori predefiniti per le altre impostazioni nella scheda **Listener**, quindi selezionare la scheda **Destinazioni back-end** per configurare il resto della regola di routing.

4. Nella scheda **Destinazioni back-end** selezionare **myBackendPool** per **Destinazione back-end**.

5. Per **Impostazione HTTP** selezionare **Crea nuovo** per creare una nuova impostazione HTTP. L'impostazione HTTP determinerà il comportamento della regola di routing. 

6. Nella finestra **Aggiungi un'impostazione HTTP** visualizzata immettere *myHTTPSetting* per **Nome impostazione HTTP**. Accettare i valori predefiniti per le altre impostazioni nella finestra **Aggiungi un'impostazione HTTP** e quindi selezionare **Aggiungi** per tornare alla finestra **Aggiungi una regola di routing**.
7. In **Routing basato su percorso** selezionare **Aggiungere più destinazioni per creare una regola basata sul percorso**.
8. Come **Percorso** digitare */images/* \*.
9. Come **Nome regola del percorso** digitare *Images*.
10. Come **Impostazione HTTP** selezionare **myHTTPSetting**
11. Come **Destinazione back-end** selezionare **Images**.
12. Selezionare **Aggiungi** per salvare la regola di percorso e tornare alla scheda **Aggiungi una regola di routing**.
13. Ripetere l'operazione per aggiungere un'altra regola per Video.
14. Selezionare **Aggiungi** per aggiungere una regola di gestione e tornare alla scheda **Configurazione**.
15. Selezionare **Avanti: Tag** e quindi **Passaggio successivo: Rivedi e crea**.

> [!NOTE]
> Non è necessario aggiungere una regola di percorso */* * personalizzata per gestire i casi predefiniti. Questa operazione viene gestita automaticamente dal pool back-end predefinito.

### <a name="review--create-tab"></a>Scheda Rivedi e crea

Rivedere le impostazioni nella scheda **Rivedi e crea** e quindi selezionare **Crea** per creare la rete virtuale, l'indirizzo IP pubblico e il gateway applicazione. La creazione del gateway applicazione in Azure può richiedere diversi minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.


## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

    ![Registrare l'indirizzo IP pubblico del gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Ad esempio, http:\//52.188.72.175:8080.

    ![Testare l'URL di base nel gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Il listener sulla porta 8080 instrada questa richiesta al pool back-end predefinito.

3. Cambiare l'URL in *http://&lt;ip-address&gt;:8080/images/test.htm*, sostituendo &lt;ip-address&gt; con il proprio indirizzo IP. Dovrebbe visualizzarsi una schermata simile alla seguente:

    ![Testare l'URL delle immagini nel gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Il listener sulla porta 8080 instrada questa richiesta al pool back-end *Images*.

4. Cambiare l'URL in *http://&lt;ip-address&gt;:8080/video/test.htm*, sostituendo &lt;ip-address&gt; con il proprio indirizzo IP. Dovrebbe visualizzarsi una schermata simile alla seguente:

    ![Testare l'URL video nel gateway applicazione](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Il listener sulla porta 8080 instrada questa richiesta al pool back-end *Video*.


## <a name="next-steps"></a>Passaggi successivi

- [Abilitazione di SSL end-to-end nel gateway applicazione di Azure](application-gateway-backend-ssl.md)
