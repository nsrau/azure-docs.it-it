---
title: Creare un gateway applicazione con un web application firewall - Portale di Azure | Microsoft Docs
description: Informazioni su come creare un gateway applicazione con un web application firewall tramite il portale di Azure.
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
ms.openlocfilehash: d2b8fc65e6cd03f61151dbae66bb89821cdab13b
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Creare un gateway applicazione con un web application firewall tramite il portale di Azure

È possibile usare il portale di Azure per creare un [gateway applicazione](application-gateway-introduction.md) con un [web application firewall](application-gateway-web-application-firewall-overview.md) (WAF). Il WAF usa regole di [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) per proteggere l'applicazione. Queste regole includono la protezione contro attacchi di tipo SQL injection, attacchi tramite script da altri siti (XSS) e hijack delle sessioni.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un gateway applicazione con WAF abilitato
> * Creare le macchine virtuali usate come server back-end
> * Creare un account di archiviazione e configurare la diagnostica

![Esempio di web application firewall](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create è necessaria una rete virtuale. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end. È possibile creare una rete virtuale durante la creazione del gateway applicazione.

1. Fare clic su **Nuovo** nell'angolo in alto a sinistra nel portale di Azure.
2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco In primo piano.
3. Immettere i valori seguenti per il gateway applicazione:

    - *myAppGateway* come nome del gateway applicazione.
    - *myResourceGroupAG* come nuovo gruppo di risorse.
    - Selezionare *WAF* come livello del gateway applicazione.

    ![Creare il nuovo gateway applicazione](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
5. Fare clic su **Scegliere una rete virtuale**, **Crea nuova** e quindi immettere i valori seguenti per la rete virtuale:

    - *myVNet* come nome della rete virtuale.
    - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
    - *myAGSubnet* come nome della subnet.
    - *10.0.0.0/24* come spazio indirizzi della subnet.

    ![Creare una rete virtuale](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Fare clic su **OK** per creare la rete virtuale e la subnet.
7. Fare clic su **Scegliere un indirizzo IP pubblico**, **Crea nuovo** e quindi immettere il nome dell'indirizzo IP pubblico. In questo esempio il nome dell'indirizzo IP pubblico è *myAGPublicIPAddress*. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
8. Accettare i valori predefiniti per la configurazione del listener, lasciare disabilitato il web application firewall e quindi fare clic su **OK**.
9. Rivedere le impostazioni nella pagina di riepilogo e quindi fare clic su **OK** per creare le risorse di rete e il gateway applicazione. La creazione del gateway applicazione potrebbe richiedere alcuni minuti. Attendere il completamento della distribuzione prima di passare alla sezione successiva.

### <a name="add-a-subnet"></a>Aggiungere una subnet

1. Fare clic su **Tutte le risorse** nel menu a sinistra e quindi su **myVNet** nell'elenco delle risorse.
2. Fare clic su **Subnet** e quindi su **Subnet**.

    ![Creare una subnet](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

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

    ![Installare l'estensione personalizzata](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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
2. Fare clic su **Pool back-end**. È stato creato automaticamente un pool predefinito con il gateway applicazione. Fare clic su **appGatewayBackendPool**.
3. Fare clic su **Aggiungi destinazione** per aggiungere ogni macchina virtuale creata al pool back-end.

    ![Aggiungere i server back-end](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Fare clic su **Save**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Creare un account di archiviazione e configurare la diagnostica

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

In questa esercitazione il gateway applicazione usa un account di archiviazione per archiviare i dati con finalità di rilevamento e prevenzione. Per registrare i dati, è anche possibile usare Log Analytics o Hub eventi.

1. Fare clic su **Nuovo** nell'angolo in alto a sinistra nel portale di Azure.
2. Selezionare **Archiviazione** e quindi **Account di archiviazione: BLOB, File, Tabelle, Code**.
3. Immettere il nome dell'account di archiviazione, selezionare **Usa esistente** come gruppo di risorse e quindi selezionare **myResourceGroupAG**. In questo esempio il nome dell'account di archiviazione è *myagstore1*. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **Crea**.

## <a name="configure-diagnostics"></a>Configurare la diagnostica

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

1. Individuare l'indirizzo IP pubblico del gateway applicazione nella schermata Panoramica. Fare clic su **Tutte le risorse** e quindi su **myAGPublicIPAddress**.

    ![Registrare l'indirizzo IP pubblico del gateway applicazione](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

    ![Testare il gateway applicazione](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un gateway applicazione con WAF abilitato
> * Creare le macchine virtuali usate come server back-end
> * Creare un account di archiviazione e configurare la diagnostica

Per altre informazioni sui gateway applicazione e sulle risorse associate, continuare con le procedure dettagliate.