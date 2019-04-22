---
title: Configurare il Gateway applicazione di Azure con un indirizzo IP privato front-end
description: Questo articolo fornisce informazioni su come configurare il Gateway applicazione con un indirizzo IP privato front-end
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: cfc63349e20aa6dbef4e0d31e81842d325bd3ec6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905539"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurare un gateway applicazione con un endpoint di servizio di bilanciamento del carico interno

Gateway applicazione di Azure può essere configurato con un indirizzo VIP con connessione Internet o con un endpoint interno non esposto a Internet (utilizzando un indirizzo IP privato per l'indirizzo IP front-end), noto anche come un servizio di bilanciamento del carico interno di endpoint (ILB). Configurare il gateway usando un indirizzo IP privato front-end è utile per applicazioni line-of-business interne non esposte a Internet. È utile anche per servizi e livelli in un'applicazione a più livelli posti entro un limite di sicurezza non esposto a Internet, ma che richiedono la distribuzione del carico round robin, la persistenza delle sessioni o la terminazione Secure Sockets Layer (SSL).

Questo articolo illustra i passaggi per configurare un gateway applicazione con un indirizzo IP privato front-end dal portale di Azure.

In questo articolo verrà spiegato come:

- Creare una configurazione IP front-end privato per un Gateway applicazione
- Creare un gateway applicazione con configurazione IP front-end privato


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale. È possibile creare una nuova rete virtuale oppure usarne una esistente. In questo esempio si creerà una nuova rete virtuale. È possibile creare una rete virtuale durante la creazione del gateway applicazione. Le istanze del gateway applicazione vengono create in subnet separate. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end.

1. Fare clic su **Nuovo** nell'angolo in alto a sinistra nel portale di Azure.
2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco In primo piano.
3. Immettere *myAppGateway* come nome del gateway applicazione e *myResourceGroupAG* come nuovo gruppo di risorse.
4. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
5. Fare clic su **Scegliere una rete virtuale**, **Crea nuova** e quindi immettere i valori seguenti per la rete virtuale:
   - myVNet * - per il nome della rete virtuale.
   - 10.0.0.0/16* - spazio di indirizzi della rete virtuale.
   - *myAGSubnet* come nome della subnet.
   - *10.0.0.0/24* come spazio indirizzi della subnet.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Fare clic su **OK** per creare la rete virtuale e la subnet.
7. Per impostazione predefinita, sia un'assegnazione di indirizzi IP dinamici e scegliere la configurazione Frontend IP come privato. Il primo indirizzo disponibile di scelto Subnet verrà assegnata come indirizzo IP front-end.
8. Se si desidera scegliere un indirizzo IP privato dall'intervallo di indirizzi della subnet (allocazione statica), fare clic nella casella **scegliere un indirizzo IP privato specifico** e specificare l'indirizzo IP.
   > [!NOTE]
   > Una volta allocata, il tipo di indirizzo IP (dinamico o statico) non può essere modificato in un secondo momento.
9. Scegliere la configurazione del listener per il protocollo e la porta, la configurazione di Web Application firewall (se necessario) e fare clic su OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Rivedere le impostazioni nella pagina di riepilogo e quindi fare clic su **OK** per creare le risorse di rete e il gateway applicazione. La creazione del gateway applicazione potrebbe richiedere alcuni minuti. Attendere il completamento della distribuzione prima di passare alla sezione successiva.

## <a name="add-backend-pool"></a>Aggiungere un pool back-end

Il pool back-end viene usato per indirizzare le richieste ai server back-end che gestiranno la richiesta. Il back-end può essere costituito da schede di interfaccia di rete, set di scalabilità di macchine virtuali, IP pubblici, IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come Servizio app di Azure. In questo esempio, come back-end di destinazione si useranno macchine virtuali. È possibile usare macchine virtuali esistenti o crearne di nuove. In questo esempio si creeranno due macchine virtuali usate da Azure come server back-end per il gateway applicazione. A tale scopo:

1. Creare 2 nuove VM, *myVM* e *myVM2*, da usare come server back-end.
2. Installare IIS nelle macchine virtuali per verificare che il gateway applicazione sia stato creato correttamente.
3. Aggiungere i server back-end al pool back-end.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Fare clic su **Nuovo**.
2. Fare clic su **Calcolo** e quindi selezionare **Windows Server 2016 Datacenter** nell'elenco In primo piano.
3. Immettere i valori seguenti per la macchina virtuale:
   - *myVM* come nome della macchina virtuale.
   - *azureuser* come nome utente dell'amministratore.
   - *Azure123456!* come password.
   - Selezionare **Usa esistente** e quindi *myResourceGroupAG*.
4. Fare clic su **OK**.
5. Selezionare **DS1_V2** per le dimensioni della macchina virtuale e fare clic su **seleziona**.
6. Assicurarsi che **myVNet** sia selezionato per la rete virtuale e che la subnet sia **myBackendSubnet**.
7. Fare clic su **Disabilitato** per disabilitare la diagnostica di avvio.
8. Fare clic su **OK**, verificare le impostazioni nella pagina di riepilogo e quindi fare clic su **Crea**.

### <a name="install-iis"></a>Installare IIS

1. Aprire la shell interattiva e assicurarsi che sia impostata su **PowerShell**.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Eseguire questo comando per installare IIS nella macchina virtuale:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
