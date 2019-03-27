---
title: Esercitazione - creare un gateway applicazione con un web application firewall - portale di Azure | Microsoft Docs
description: Informazioni su come creare un gateway applicazione con un web application firewall tramite il portale di Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.openlocfilehash: c5f1cb992f27a8d3f97967ff6b885b3296be8710
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448426"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Creare un gateway applicazione con un web application firewall tramite il portale di Azure

> [!div class="op_single_selector"]
>
> - [Portale di Azure](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Interfaccia della riga di comando di Azure](tutorial-restrict-web-traffic-cli.md)
>
> 

Questa esercitazione illustra come usare il portale di Azure per creare un [gateway applicazione](application-gateway-introduction.md) con un [firewall applicazione web](application-gateway-web-application-firewall-overview.md) (WAF). Il WAF usa regole di [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) per proteggere l'applicazione. Queste regole includono la protezione contro attacchi di tipo SQL injection, attacchi tramite script da altri siti (XSS) e hijack delle sessioni. Dopo aver creato il gateway applicazione, testarlo per verificare che funzioni correttamente. Con il Gateway applicazione di Azure, è indirizzare il traffico delle applicazioni web a risorse specifiche assegnando i listener a porte, la creazione di regole e aggiunta di risorse a un pool back-end. Per ragioni di semplicità, questo articolo usa un semplice programma di installazione con un IP pubblico front-end, un listener di base per ospitare un unico sito per il gateway applicazione, due macchine virtuali usate per il pool back-end e una regola di routing di base richiesta.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un gateway applicazione con WAF abilitato
> * Creare le macchine virtuali usate come server back-end
> * Creare un account di archiviazione e configurare la diagnostica

![Esempio di web application firewall](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale. È possibile creare una nuova rete virtuale o usarne uno esistente. In questo esempio, si creerà una nuova rete virtuale. È possibile creare una rete virtuale durante la creazione del gateway applicazione. Le istanze del Gateway applicazione vengono create in subnet separate. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end.

Selezionare **Crea una risorsa** nel menu a sinistra del portale di Azure. Verrà visualizzata la finestra **Nuovo**.

Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco **In primo piano**.

### <a name="basics-page"></a>Pagina Informazioni di base

1. Nella pagina **Informazioni di base** immettere questi valori per le impostazioni del gateway applicazione seguenti:
   - **Nome**: immettere *myAppGateway* come nome del gateway applicazione.
   - **Gruppo di risorse**: selezionare **myResourceGroupAG** come gruppo di risorse. Se non esiste, selezionare **Crea nuovo** per crearlo.
   - Selezionare *WAF* per il livello del gateway applicazione.![ Creare nuovo gateway applicazione](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.

### <a name="settings-page"></a>Pagina Impostazioni

1. Nella pagina **Impostazioni** sotto **Configurazione subnet**, selezionare **Scegliere una rete virtuale**. <br>
2. Nella pagina **Scegliere una rete virtuale** selezionare **Crea nuova** e quindi immettere i valori per le seguenti impostazioni della rete virtuale:
   - **Nome**: immettere *myVnet* come nome della rete virtuale.
   - **Spazio degli indirizzi**: immettere *10.0.0.0/16* come spazio indirizzi della rete virtuale.
   - **Nome della subnet**: immettere *myAGSubnet* come nome della subnet.<br>La subnet del gateway applicazione può contenere solo i gateway applicazione. Non sono consentite altre risorse.
   - **Intervallo di indirizzi subnet**: Immettere *10.0.0.0/24* per l'intervallo di indirizzi subnet.![ Creare una rete virtuale](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)
3. Fare clic su **OK** per creare la rete virtuale e la subnet.
4. Scegliere il **configurazione Frontend IP**. In **Configurazione dell'indirizzo IP front-end** verificare che **Tipo di indirizzo IP** sia impostato su **pubblico**. In **Indirizzo IP pubblico**, verificare che **Crea nuovo** sia selezionato. <br>È possibile configurare Frontend IP per essere pubblico o privato in base al caso d'uso. In questo esempio, si sceglierà un indirizzo IP pubblico front-end. 
5. Immettere *myAGPublicIPAddress* come nome dell'indirizzo IP pubblico. 
6. Accettare i valori predefiniti per le altre impostazioni e quindi selezionare **OK**.<br>I valori predefiniti verrà scelto in questo articolo per motivi di semplicità, ma è possibile configurare i valori personalizzati per le altre impostazioni in base al caso d'uso 

### <a name="summary-page"></a>Pagina Riepilogo

Rivedere le impostazioni nella pagina di **riepilogo** e quindi selezionare **OK** per creare la rete virtuale, l'indirizzo IP pubblico e il gateway applicazione. La creazione del gateway applicazione in Azure può richiedere diversi minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

## <a name="add-backend-pool"></a>Aggiungere un pool back-end

Il pool back-end viene utilizzato per indirizzare le richieste ai server back-end che renderà la richiesta. I pool back-end possono essere costituiti da schede di interfaccia di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come Servizio app di Azure. È necessario aggiungere il back-end è destinato a un pool di back-end.

In questo esempio, si userà le macchine virtuali come back-end di destinazione. È possibile utilizzare le macchine virtuali esistenti o crearne uno nuovo. In questo esempio, si creerà due macchine virtuali che usa Azure come server back-end per il gateway applicazione. A tale scopo, si apprenderà come:

1. Creare una nuova subnet *myBackendSubnet*, in cui verranno create le nuove macchine virtuali. 
2. Creare 2 macchine Virtuali nuove, *myVM* e *myVM2*, da utilizzare come server back-end.
3. Installare IIS nelle macchine virtuali per verificare che il gateway applicazione è stato creato correttamente.
4. Aggiungere i server back-end al pool back-end.

### <a name="add-a-subnet"></a>Aggiungere una subnet

Aggiungere una subnet alla rete virtuale creata seguendo questa procedura:

1. Selezionare **Tutte le risorse** nel menu a sinistra del portale di Azure, immettere *myVNet* nella casella di ricerca e quindi selezionare **myVNet** dai risultati della ricerca.

2. Selezionare **Subnet** dal menu a sinistra e quindi **+ Subnet**. 

   ![Creare una subnet](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Dalla pagina **Aggiungi subnet** immettere *myBackendSubnet* come **nome** della subnet e quindi selezionare **OK**.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Nel portale di Azure fare clic su **Crea una risorsa**. Verrà visualizzata la finestra **Nuovo**.
2. Selezionare **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco **In primo piano**. Viene visualizzata la pagina **Creare una macchina virtuale**.<br>Il Gateway applicazione può indirizzare il traffico a qualsiasi tipo di macchina virtuale usata nel pool back-end. In questo esempio, si usa un Data Center di Windows Server 2016.
3. Immettere questi valori nella scheda **Informazioni di base** per le seguenti impostazioni della macchina virtuale:
   - **Gruppo di risorse**: selezionare **myResourceGroupAG** come nome del gruppo di risorse.
   - **Nome macchina virtuale**: immettere *myVM* come nome della macchina virtuale.
   - **Nome utente**: immettere *azureuser* come nome utente dell'amministratore.
   - **Password**: immettere *Azure123456!* come password amministratore.
4. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Avanti: Dischi**.  
5. Accettare le impostazioni predefinite della scheda **Dischi** e quindi selezionare **Avanti: Rete**.
6. Nella scheda **Rete** verificare che **myVNet** sia selezionato per la **Rete virtuale** e che la **Subnet** sia **myBackendSubnet**. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Avanti: Gestione**.<br>Il Gateway applicazione può comunicare con istanze all'esterno della rete virtuale a cui si trova, ma è necessario assicurarsi che sia presente la connettività IP. 
7. Nella scheda **Gestione** impostare **Diagnostica di avvio** su **Off**. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Rivedi e crea**.
8. Nella scheda **Rivedi e crea** rivedere le impostazioni, correggere eventuali errori di convalida e quindi selezionare **Crea**.
9. Attendere il termine della creazione della macchina virtuale prima di continuare.

### <a name="install-iis-for-testing"></a>Installare IIS per i test

In questo esempio viene eseguita l'installazione IIS nelle macchine virtuali solo allo scopo di verifica di che Azure creato correttamente il gateway applicazione. 

1. Aprire [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). A tale scopo, selezionare **Cloud Shell** dalla barra di spostamento superiore del portale di Azure e quindi selezionare **PowerShell** nell'elenco a discesa. 

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

3. Creare una seconda macchina virtuale e installare IIS seguendo la procedura precedentemente completata. Uso *myVM2* per il nome della macchina virtuale e per l'impostazione **VMName** del cmdlet **Set-AzureRmVMExtension**.

### <a name="add-backend-servers-to-backend-pool"></a>Aggiungere i server back-end al pool back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.

2. Selezionare **Pool back-end** dal menu a sinistra. Azure ha creato automaticamente un pool predefinito, **appGatewayBackendPool**, quando è stato creato il gateway applicazione. 

3. Selezionare **appGatewayBackendPool**.

4. In **Destinazioni** selezionare **Macchina virtuale** dall'elenco a discesa.

5. In **MACCHINA VIRTUALE** e **INTERFACCE DI RETE** selezionare le macchine virtuali **myVM** e **myVM2** e le relative interfacce di rete associate dagli elenchi a discesa.

   ![Aggiungere i server back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selezionare **Salva**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Creare un account di archiviazione e configurare la diagnostica

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

In questa esercitazione il gateway applicazione usa un account di archiviazione per archiviare i dati con finalità di rilevamento e prevenzione. È anche possibile usare i log di Monitoraggio di Azure o Hub eventi per registrare i dati.

1. Fare clic su **Nuovo** nell'angolo in alto a sinistra nel portale di Azure.
2. Selezionare **Archiviazione** e quindi **Account di archiviazione: BLOB, File, Tabelle, Code**.
3. Immettere il nome dell'account di archiviazione, selezionare **Usa esistente** come gruppo di risorse e quindi selezionare **myResourceGroupAG**. In questo esempio il nome dell'account di archiviazione è *myagstore1*. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **Crea**.

### <a name="configure-diagnostics"></a>Configurare la diagnostica

Configurare la diagnostica per registrare i dati nei log ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog e ApplicationGatewayFirewallLog.

1. Nel menu di sinistra fare clic su **Tutte le risorse** e quindi selezionare *myAppGateway*.
2. In Monitoraggio fare clic su **Log di diagnostica**.
3. Fare clic su **Aggiungi impostazioni di diagnostica**.
4. Immettere *myDiagnosticsSettings* come nome per le impostazioni di diagnostica.
5. Selezionare **Archivia in un account di archiviazione** e quindi fare clic su **Configura** per selezionare l'account di archiviazione *myagstore1* creato in precedenza.
6. Selezionare i log del gateway applicazione da raccogliere e mantenere.
7. Fare clic su **Save**.

    ![Configurare la diagnostica](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Nonostante l'installazione di IIS non sia necessaria per creare il gateway applicazione, è stata eseguita in questa guida introduttiva per verificare se il gateway applicazione è stato creato correttamente in Azure. Usare IIS per testare il gateway applicazione:

1. Trovare l'indirizzo IP pubblico del gateway applicazione nel relativo **Overview** pagina.![ Registrare l'indirizzo IP pubblico del gateway applicazione](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)in alternativa, è possibile selezionare **tutte le risorse**, immettere *myAGPublicIPAddress* nella ricerca casella e quindi selezionarlo nella ricerca risultati. Azure mostra l'indirizzo IP pubblico nella pagina **Panoramica**.
2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.
3. Controllare la risposta. Una risposta valida verifica che il gateway applicazione è stato completato ed è in grado di connettersi correttamente con il back-end.![Testare il gateway applicazione](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il gateway applicazione non sono più necessarie, rimuovere il gruppo di risorse. La rimozione del gruppo di risorse comporta anche la rimozione del gateway applicazione e di tutte le risorse correlate. 

Per rimuovere il gruppo di risorse:

1. Nel menu a sinistra del portale di Azure, selezionare **Gruppi di risorse**.
2. Nella pagina **Gruppo di risorse** cercare **myResourceGroupAG** nell'elenco e selezionarlo.
3. Nella pagina **Gruppo di risorse** selezionare **Elimina gruppo di risorse**.
4. Immettere *myResourceGroupAG* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e quindi selezionare **Elimina**

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un gateway applicazione con WAF abilitato
> * Creare le macchine virtuali usate come server back-end
> * Creare un account di archiviazione e configurare la diagnostica

Per altre informazioni sui gateway applicazione e sulle risorse associate, continuare con le procedure dettagliate.
