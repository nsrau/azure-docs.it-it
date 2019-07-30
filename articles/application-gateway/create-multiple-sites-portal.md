---
title: 'Esercitazione: Creare un gateway applicazione che ospita più siti Web usando il portale di Azure'
description: Questa esercitazione descrive come creare un gateway applicazione che ospita più siti Web usando il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: 73a313a6244971b65ba89fb7b676610d88acabfa
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498460"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Esercitazione: Creare e configurare un gateway applicazione per ospitare più siti Web usando il portale di Azure

È possibile usare il portale di Azure per configurare l'[hosting di più siti Web](multiple-site-overview.md) quando si crea un [gateway applicazione](overview.md). In questa esercitazione si definiranno pool di indirizzi back-end usando macchine virtuali. e quindi si configurano i listener e le regole in base ai domini di cui si è proprietari per assicurarsi che il traffico Web raggiunga i server appropriati nei pool. Questa esercitazione presuppone che l'utente sia proprietario di più domini e che usi gli esempi di *www.contoso.com* e *www.fabrikam.com*.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gateway applicazione
> * Creare macchine virtuali per i server back-end
> * Creare pool back-end con i server back-end
> * Creare i listener back-end
> * Creare le regole di routing
> * Creare un record CNAME nel dominio

![Esempio di routing multisito](./media/create-multiple-sites-portal/scenario.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com)

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

    - **Nome subnet** (subnet del gateway applicazione): Nella griglia **Subnet** verrà visualizzata una subnet denominata *Predefinita*. Modificare il nome della subnet in *myAGSubnet*.<br>La subnet del gateway applicazione può contenere solo i gateway applicazione. Non sono consentite altre risorse.

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

    - **Nome**: immettere *contosoPool* come nome del pool back-end.
    - **Aggiungi pool back-end senza destinazioni**: selezionare **Sì** per creare un pool back-end senza destinazioni. Le destinazioni back-end verranno aggiunte dopo la creazione del gateway applicazione.

3. Nella finestra **Aggiungi un pool back-end** selezionare **Aggiungi** per salvare la configurazione del pool back-end e tornare alla scheda **Back-end**.
4. Aggiungere ora un altro pool back-end denominato *fabrikamPool*.

     ![Creare il nuovo gateway applicazione: back-end](./media/create-multiple-sites-portal/backend-pools.png)

4. Nella scheda **Back-end** selezionare **Passaggio successivo: Configurazione**.

### <a name="configuration-tab"></a>Scheda Configurazione

Nella scheda **Configurazione** verranno connessi i pool front-end e back-end creati tramite una regola di routing.

1. Selezionare **Aggiungi una regola** nella colonna **Regole di routing**.

2. Nella finestra **Aggiungi una regola di routing** visualizzata immettere *contosoRule* per **Nome regola**.

3. Una regola di routing richiede un listener. Nella scheda **Listener** nella finestra **Aggiungi una regola di routing** immettere i valori seguenti per il listener:

    - **Nome listener**: immettere *contosoListener* come nome del listener.
    - **IP front-end**: selezionare **Pubblico** per scegliere l'indirizzo IP pubblico creato per il front-end.

   In **Impostazioni aggiuntive**:
   - **Tipo di listener**: Più siti
   - **Nome host**: **www.contoso.com**

   Accettare i valori predefiniti per le altre impostazioni nella scheda **Listener**, quindi selezionare la scheda **Destinazioni back-end** per configurare il resto della regola di routing.

   ![Creare il nuovo gateway applicazione: listener](./media/create-multiple-sites-portal/routing-rule.png)

4. Nella scheda **Destinazioni back-end** selezionare **contosoPool** per **Destinazione back-end**.

5. Per **Impostazione HTTP** selezionare **Crea nuovo** per creare una nuova impostazione HTTP. L'impostazione HTTP determinerà il comportamento della regola di routing. Nella finestra **Aggiungi un'impostazione HTTP** visualizzata immettere *contosoHTTPSetting* per **Nome impostazione HTTP**. Accettare i valori predefiniti per le altre impostazioni nella finestra **Aggiungi un'impostazione HTTP** e quindi selezionare **Aggiungi** per tornare alla finestra **Aggiungi una regola di routing**. 

6. Nella finestra **Aggiungi una regola di routing** selezionare **Aggiungi** per salvare la regola di routing e tornare alla scheda **Configurazione**.
7. Selezionare **Aggiungi una regola** e aggiungere una regola, un listener, una destinazione back-end e un'impostazione HTTP simili per Fabrikam.

     ![Creare il nuovo gateway applicazione: regola di routing](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Selezionare **Avanti: Tag** e quindi **Passaggio successivo: Rivedi e crea**.

### <a name="review--create-tab"></a>Scheda Rivedi e crea

Rivedere le impostazioni nella scheda **Rivedi e crea** e quindi selezionare **Crea** per creare la rete virtuale, l'indirizzo IP pubblico e il gateway applicazione. La creazione del gateway applicazione in Azure può richiedere diversi minuti.

Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

## <a name="add-backend-targets"></a>Aggiungere destinazioni back-end

In questo esempio vengono usate macchine virtuali come back-end di destinazione. È possibile usare macchine virtuali esistenti o crearne di nuove. Vengono create due macchine virtuali, usate da Azure come server back-end per il gateway applicazione.

Per aggiungere destinazioni back-end:

1. Creare due nuove macchine virtuali, *contosoVM* e *fabrikamVM*, da usare come server back-end.
2. Installare IIS nelle macchine virtuali per verificare che il gateway applicazione sia stato creato correttamente.
3. Aggiungere i server back-end ai pool back-end.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Nel portale di Azure fare clic su **Crea una risorsa**. Verrà visualizzata la finestra **Nuovo**.
2. Selezionare **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco **Più comuni**. Viene visualizzata la pagina **Creare una macchina virtuale**.<br>Il gateway applicazione può indirizzare il traffico a qualsiasi tipo di macchina virtuale usato nel pool back-end. In questo esempio si usa Windows Server 2016 Datacenter.
3. Immettere questi valori nella scheda **Informazioni di base** per le seguenti impostazioni della macchina virtuale:

    - **Gruppo di risorse**: selezionare **myResourceGroupAG** come nome del gruppo di risorse.
    - **Nome macchina virtuale**: immettere *contosoVM* come nome della macchina virtuale.
    - **Nome utente**: immettere *azureuser* come nome utente dell'amministratore.
    - **Password**: immettere *Azure123456!* come password amministratore.
4. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Avanti: Dischi**.  
5. Accettare le impostazioni predefinite della scheda **Dischi** e quindi selezionare **Avanti: Rete**.
6. Nella scheda **Rete** verificare che **myVNet** sia selezionato per la **Rete virtuale** e che la **Subnet** sia **myBackendSubnet**. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Avanti: Gestione**.<br>Il gateway applicazione può comunicare con le istanze all'esterno della rete virtuale in cui si trova, ma è necessario verificare che ci sia la connettività IP.
7. Nella scheda **Gestione** impostare **Diagnostica di avvio** su **Off**. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Rivedi e crea**.
8. Nella scheda **Rivedi e crea** rivedere le impostazioni, correggere eventuali errori di convalida e quindi selezionare **Crea**.
9. Attendere il termine della creazione della macchina virtuale prima di continuare.

### <a name="install-iis-for-testing"></a>Installare IIS a scopo di test

In questo esempio viene installato IIS nelle macchine virtuali solo per verificare che il gateway applicazione sia stato creato correttamente da Azure.

1. Aprire [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). A tale scopo, selezionare **Cloud Shell** dalla barra di spostamento superiore del portale di Azure e quindi selezionare **PowerShell** nell'elenco a discesa. 

    ![Installare l'estensione personalizzata](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Eseguire questo comando per installare IIS nella macchina virtuale: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Creare una seconda macchina virtuale e installare IIS seguendo la procedura precedentemente completata. Usare *fabrikamVM* come nome della macchina virtuale e per l'impostazione **VMName** del cmdlet **Set-AzVMExtension**.

### <a name="add-backend-servers-to-backend-pools"></a>Aggiungere i server back-end ai pool back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **Pool back-end** dal menu a sinistra.

3. Selezionare **contosoPool**.

4. In **Destinazioni** selezionare **Macchina virtuale** dall'elenco a discesa.

5. In **MACCHINA VIRTUALE** e **INTERFACCE DI RETE** selezionare la macchina virtuale **contosoVM** e le relative interfacce di rete associate negli elenchi a discesa.

    ![Aggiungere i server back-end](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Selezionare **Salva**.
7. Ripetere la procedura per aggiungere *fabrikamVM* e l'interfaccia a *fabrikamPool*.

Attendere il completamento della distribuzione prima di procedere al passaggio successivo.

## <a name="create-a-www-a-record-in-your-domains"></a>Creare un record A www nei domini

Dopo aver creato il gateway applicazione con l'indirizzo IP pubblico, è possibile ottenere l'indirizzo IP e usarlo per creare un record A nei domini. 

1. Fare clic su **Tutte le risorse** e quindi su **myAGPublicIPAddress**.

    ![Registrare l'indirizzo DNS del gateway applicazione](./media/create-multiple-sites-portal/public-ip.png)

2. Copiare l'indirizzo IP e usarlo come valore per un nuovo record A *www* nei domini.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Immettere il nome di dominio nella barra degli indirizzi del browser. Ad esempio, http://www.contoso.com.

    ![Testare il sito contoso nel gateway applicazione](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Sostituire l'indirizzo con l'altro dominio come nell'esempio seguente:

    ![Testare il sito fabrikam nel gateway applicazione](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, rimuovere il gruppo di risorse. La rimozione del gruppo di risorse comporta anche la rimozione del gateway applicazione e di tutte le risorse correlate.

Per rimuovere il gruppo di risorse:

1. Nel menu a sinistra del portale di Azure, selezionare **Gruppi di risorse**.
2. Nella pagina **Gruppo di risorse** cercare **myResourceGroupAG** nell'elenco e selezionarlo.
3. Nella pagina **Gruppo di risorse** selezionare **Elimina gruppo di risorse**.
4. Immettere *myResourceGroupAG* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e quindi selezionare **Elimina**

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulle operazioni che è possibile eseguire con il gateway applicazione di Azure](application-gateway-introduction.md)