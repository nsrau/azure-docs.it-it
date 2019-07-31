---
title: Esercitazione - Creare un gateway applicazione con regole di gestione basato su percorsi URL - Portale di Azure
description: Questa esercitazione illustra come creare regole di gestione basato su percorsi URL per un gateway applicazione e un set di scalabilità di macchine virtuali usando il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: victorh
ms.openlocfilehash: 63a1faa79374e72eabfbee4ece454728c3b4cc05
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597569"
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

1. Selezionare **Crea una risorsa** nel menu a sinistra del portale di Azure. Verrà visualizzata la finestra **Nuovo**.

2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco **In primo piano**.

### <a name="basics-tab"></a>Scheda Informazioni di base

1. Nella scheda **Informazioni di base** immettere questi valori per le impostazioni del gateway applicazione seguenti:

   - **Gruppo di risorse**: selezionare **myResourceGroupAG** come gruppo di risorse. Se non esiste, selezionare **Crea nuovo** per crearlo.
   - **Nome del gateway applicazione**: immettere *myAppGateway* come nome del gateway applicazione.

     ![Creare il nuovo gateway applicazione: Nozioni di base](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale. È possibile creare una nuova rete virtuale oppure usarne una esistente. In questo esempio verrà creata una nuova rete virtuale in concomitanza con la creazione del gateway applicazione. Le istanze del gateway applicazione vengono create in subnet separate. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end.

    In **Configura rete virtuale** selezionare **Crea nuovo** per creare una nuova rete virtuale. Nella finestra **Crea rete virtuale** visualizzata immettere i valori seguenti per creare la rete virtuale e due subnet:

    - **Nome**: immettere *myVnet* come nome della rete virtuale.

    - **Nome subnet** (subnet del gateway applicazione): Nella griglia **Subnet** verrà visualizzata una subnet denominata *Predefinita*. Modificare il nome della subnet in *myAGSubnet*.

      La subnet del gateway applicazione può contenere solo i gateway applicazione. Non sono consentite altre risorse.

    - **Nome subnet** (subnet del server back-end): Nella seconda riga della griglia **Subnet** immettere *myBackendSubnet* nella colonna **Nome subnet**.

    - **Intervallo di indirizzi** (subnet del server back-end): Nella seconda riga della griglia **Subnet** immettere un intervallo di indirizzi che non si sovrappone all'intervallo di indirizzi di *myAGSubnet*. Ad esempio, se l'intervallo di indirizzi di *myAGSubnet* è 10.0.0.0/24, immettere *10.0.1.0/24* per l'intervallo di indirizzi di *myBackendSubnet*.

    Selezionare **OK** per chiudere la finestra **Crea rete virtuale** e salvare le impostazioni della rete virtuale.

     ![Creare un nuovo gateway applicazione: rete virtuale](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Nella scheda **Informazioni di base** accettare i valori predefiniti per le altre impostazioni e quindi selezionare **Successivo: Front-end**.

### <a name="frontends-tab"></a>Scheda Front-end

1. Nella scheda **Front- end** verificare che **Tipo di indirizzo IP front-end** sia impostato su **Pubblico**. <br>È possibile configurare l'indirizzo IP front-end come pubblico o privato in base al caso d'uso. In questo esempio si sceglierà un indirizzo IP front-end pubblico.
   > [!NOTE]
   > Per lo SKU v2 del gateway applicazione, è possibile scegliere solo la configurazione **pubblica** dell'IP front-end. La configurazione di indirizzi IP front-end privati non è attualmente abilitata per questo SKU v2.

2. Scegliere **Crea nuovo** per **Indirizzo IP pubblico** e immettere *myAGPublicIPAddress* per il nome dell'indirizzo IP pubblico, quindi selezionare **OK**. 

     ![Creare il nuovo gateway applicazione: front-end](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selezionare **Avanti: Back-end**.

### <a name="backends-tab"></a>Scheda Back-end

Il pool back-end viene usato per instradare le richieste ai server back-end che gestiscono la richiesta. I pool back-end possono essere costituiti da schede di interfaccia di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come Servizio app di Azure. In questo esempio verrà creato un pool back-end vuoto con il gateway applicazione a cui verranno aggiunte le destinazioni back-end.

1. Nella scheda **Back- end** selezionare **Aggiungi un pool back-end**.

2. Nella finestra **Aggiungi un pool back-end** visualizzata immettere i valori seguenti per creare un pool back-end vuoto:

    - **Nome**: immettere *appGatewayBackendPool* come nome del pool back-end.
    - **Aggiungi pool back-end senza destinazioni**: selezionare **Sì** per creare un pool back-end senza destinazioni. Le destinazioni back-end verranno aggiunte dopo la creazione del gateway applicazione.

3. Nella finestra **Aggiungi un pool back-end** selezionare **Aggiungi** per salvare la configurazione del pool back-end e tornare alla scheda **Back-end**.
4. Aggiungere ora altri due pool back-end *imagesBackendPool* e *videoBackendPool*.

     ![Creare il nuovo gateway applicazione: back-end](./media/create-url-route-portal/backends.png)

4. Nella scheda **Back-end** selezionare **Passaggio successivo: Configurazione**.

### <a name="configuration-tab"></a>Scheda Configurazione

Nella scheda **Configurazione** verranno connessi i pool front-end e back-end creati tramite una regola di routing.

1. Selezionare **Aggiungi una regola** nella colonna **Regole di routing**.
2. Nella finestra **Aggiungi una regola di routing** visualizzata immettere *Rule1* per **Nome regola**.
3. Una regola di routing richiede un listener. Nella scheda **Listener** nella finestra **Aggiungi una regola di routing** immettere i valori seguenti per il listener:

    - **Nome listener**: immettere *DefaultListener* come nome del listener.
    - **IP front-end**: selezionare **Pubblico** per scegliere l'indirizzo IP pubblico creato per il front-end.

   Accettare i valori predefiniti per le altre impostazioni nella scheda **Listener**, quindi selezionare la scheda **Destinazioni back-end** per configurare il resto della regola di routing.
4. Nella scheda **Destinazioni back-end** selezionare **appGatewayBackendPool** per **Destinazione back-end**.

5. Per **Impostazione HTTP** selezionare **Crea nuovo** per creare una nuova impostazione HTTP. L'impostazione HTTP determinerà il comportamento della regola di routing. Nella finestra **Aggiungi un'impostazione HTTP** visualizzata immettere *myHTTPSetting* per **Nome impostazione HTTP**. Accettare i valori predefiniti per le altre impostazioni nella finestra **Aggiungi un'impostazione HTTP** e quindi selezionare **Aggiungi** per tornare alla finestra **Aggiungi una regola di routing**. 

6. Nella finestra **Aggiungi una regola di routing** selezionare **Aggiungi** per salvare la regola di routing e tornare alla scheda **Configurazione**.



1. Selezionare **Aggiungi una regola** nella colonna **Regole di routing**.

2. Nella finestra **Aggiungi una regola di routing** visualizzata immettere *Rule2* per **Nome regola**.

3. Una regola di routing richiede un listener. Nella scheda **Listener** nella finestra **Aggiungi una regola di routing** immettere i valori seguenti per il listener:

    - **Nome listener**: immettere *myBackendListener* per il nome del listener.
    - **IP front-end**: selezionare **Pubblico** per scegliere l'indirizzo IP pubblico creato per il front-end.
    - **Porta**: 8080

   In **Impostazioni aggiuntive**:
   - **Tipo di listener**: Basic

   Accettare i valori predefiniti per le altre impostazioni nella scheda **Listener**, quindi selezionare la scheda **Destinazioni back-end** per configurare il resto della regola di routing.

4. Nella scheda **Destinazioni back-end** selezionare **appGatewayBackendPool** per **Destinazione back-end**.

5. Per **Impostazione HTTP**, selezionare *myHTTPSetting*. Accettare i valori predefiniti per le altre impostazioni nella finestra **Aggiungi un'impostazione HTTP** e quindi selezionare **Aggiungi** per tornare alla finestra **Aggiungi una regola di routing**. 

1. In **Routing basato su percorso** selezionare **Aggiungere più destinazioni per creare una regola basata sul percorso**.
2. Nella finestra **Aggiungi una regola del percorso** immettere i valori seguenti per la regola del percorso:

   - **Percorso**: */images/\**
   - **Nome regola del percorso**: *Immagini*
   - **Impostazione HTTP**: selezionare *myHTTPSetting*
   - **Destinazione back-end**: *imagesBackendPool*
9. Selezionare **Aggiungi**.
10. Aggiungere un'altra regola del percorso denominata *Video*, con il percorso impostato su */video/\** e il nome *videoBackendPool*.
11. Selezionare **Salva le modifiche e torna alle regole di routing**.

    ![Aggiungere una regola di routing](media/create-url-route-portal/add-routing-rule.png)

12. Selezionare **Aggiungi**.

7. Selezionare **Avanti: Tag** e quindi **Passaggio successivo: Rivedi e crea**.

### <a name="review--create-tab"></a>Scheda Rivedi e crea

Rivedere le impostazioni nella scheda **Rivedi e crea** e quindi selezionare **Crea** per creare la rete virtuale, l'indirizzo IP pubblico e il gateway applicazione. La creazione del gateway applicazione in Azure può richiedere diversi minuti.

Attendere fino al termine della distribuzione prima di passare alla sezione successiva.


## <a name="create-virtual-machines"></a>Creare macchine virtuali

In questo esempio vengono create tre macchine virtuali da usare come server back-end per il gateway applicazione. Viene anche installato IIS nelle macchine virtuali per verificare che il gateway applicazione sia stato creato correttamente.

1. Nel portale di Azure fare clic su **Crea una risorsa**. Verrà visualizzata la finestra **Nuovo**.
2. Selezionare **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco **Più comuni**. Viene visualizzata la pagina **Creare una macchina virtuale**.

   Il gateway applicazione può indirizzare il traffico a qualsiasi tipo di macchina virtuale usato nel pool back-end. In questo esempio si usa Windows Server 2016 Datacenter.
1. Immettere questi valori nella scheda **Informazioni di base** per le seguenti impostazioni della macchina virtuale:

    - **Gruppo di risorse**: selezionare **myResourceGroupAG** come nome del gruppo di risorse.
    - **Nome macchina virtuale**: immettere *myVM1* come nome della macchina virtuale.
    - **Nome utente**: immettere *azureuser* come nome utente dell'amministratore.
    - **Password**: immettere *Azure123456!* come password amministratore.
4. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Avanti: Dischi**.  
5. Accettare le impostazioni predefinite della scheda **Dischi** e quindi selezionare **Avanti: Rete**.
6. Nella scheda **Rete** verificare che **myVNet** sia selezionato per la **Rete virtuale** e che la **Subnet** sia **myBackendSubnet**. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Avanti: Gestione**.

   Il gateway applicazione può comunicare con le istanze all'esterno della rete virtuale in cui si trova, ma è necessario verificare che ci sia la connettività IP.
1. Nella scheda **Gestione** impostare **Diagnostica di avvio** su **Off**. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Rivedi e crea**.
2. Nella scheda **Rivedi e crea** rivedere le impostazioni, correggere eventuali errori di convalida e quindi selezionare **Crea**.
3. Attendere il termine della creazione della macchina virtuale prima di continuare.

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

3. Creare altre due macchine virtuali e installare IIS seguendo la procedura appena completata. Usare *myVM2* e *myVM3* come nomi delle macchine virtuali e per i valori di **VMName** in Set-AzVMExtension.

## <a name="add-backend-servers-to-backend-pools"></a>Aggiungere i server back-end ai pool back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **Pool back-end** dal menu a sinistra.

3. Selezionare **appGatewayBackendPool**.

4. In **Destinazioni** selezionare **Macchina virtuale** dall'elenco a discesa.

5. In **MACCHINA VIRTUALE** e **INTERFACCE DI RETE** selezionare la macchina virtuale **myVM1** e le relative interfacce di rete associate negli elenchi a discesa.

    ![Aggiungere i server back-end](./media/create-url-route-portal/backend-pool.png)

6. Selezionare **Salva**.
7. Ripetere la procedura per aggiungere *myVM2* e l'interfaccia in *imagesBackendPool* e quindi *myVM3* e l'interfaccia in *videoBackendPool*.

Attendere il completamento della distribuzione prima di procedere al passaggio successivo.

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
