---
title: Configurare un endpoint del servizio di bilanciamento del carico interno
titleSuffix: Azure Application Gateway
description: In questo articolo vengono fornite informazioni su come configurare il gateway applicazione con un indirizzo IP front-end privato
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 809274aba35e9607aeacf7c6483ec3d10f899667
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312360"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurare un gateway applicazione con un endpoint di bilanciamento del carico internoConfigure an application gateway with an internal load balancer (ILB) endpoint

Azure Application Gateway can be configured with an Internet-facing VIP or with an internal endpoint that isn't exposed to the Internet. Un endpoint interno usa un indirizzo IP privato per il front-end, noto anche come endpoint del servizio di *bilanciamento del carico interno.*

La configurazione del gateway tramite un indirizzo IP privato front-end è utile per le applicazioni line-of-business interne non esposte a Internet. È utile anche per i servizi e i livelli all'interno di un'applicazione multilivello che si trovano in un limite di sicurezza che non è esposto a Internet ma che richiede comunque la distribuzione del carico round robin, la persistenza della sessione o la protezione TLS (Transport Layer Security), precedentemente nota come Secure Sockets Layer (SSL), terminazione.

Questo articolo illustra i passaggi per configurare un gateway applicazione con un indirizzo IP privato front-end tramite il portale di Azure.This article guides you through the steps to configure an application gateway with a frontend private IP address using the Azure portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com>.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale. È possibile creare una nuova rete virtuale oppure usarne una esistente. In questo esempio si creerà una nuova rete virtuale. È possibile creare una rete virtuale durante la creazione del gateway applicazione. Le istanze del gateway applicazione vengono create in subnet separate. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end.

1. Espandere il menu del portale e selezionare **Crea una risorsa**.
2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco In primo piano.
3. Immettere *myAppGateway* come nome del gateway applicazione e *myResourceGroupAG* come nuovo gruppo di risorse.
4. Per **Regione**, selezionare **(Stati Uniti) Stati Uniti centrali**.
5. Per **Livello**, selezionare **Standard**.
6. In **Configura rete virtuale** selezionare Crea **nuovo**e quindi immettere i valori seguenti per la rete virtuale:
   - *myVNet* - come nome della rete virtuale.
   - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
   - *myAGSubnet* come nome della subnet.
   - *10.0.0.0/24* come spazio indirizzi della subnet.
   - *myBackendSubnet* - come nome della subnet back-end.
   - *10.0.1.0/24* - per lo spazio di indirizzi della subnet back-end.

    ![Creare una rete virtuale](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selezionare **OK** per creare la rete virtuale e la subnet.
7. Selezionare **Avanti:Frontends**.
8. Per **Tipo di indirizzo IP front-end**, selezionare **Privato**.

   Per impostazione predefinita, si tratta di un'assegnazione di indirizzo IP dinamico. Il primo indirizzo disponibile della subnet configurata viene assegnato come indirizzo IP front-end.
   > [!NOTE]
   > Una volta allocato, il tipo di indirizzo IP (statico o dinamico) non può essere modificato in un secondo momento.
9. Selezionare **Next:Backends**.
10. Selezionare **Aggiungi pool back-end**.
11. In **Nome**digitare *appGatewayBackendPool*.
12. Per **Aggiungi pool back-end senza destinazioni**, selezionare **Sì**. Le destinazioni verranno aggiunte in un secondo momento.
13. Selezionare **Aggiungi**.
14. Selezionare **Next:Configuration**.
15. In **Regole di routing**selezionare Aggiungi una **regola**.
16. In **Nome regola**digitare *Rrule-01*.
17. Per **Nome listener**digitare *Listener-01*.
18. Per **IP front-end**, selezionare **Privato**.
19. Accettare le impostazioni predefinite rimanenti e selezionare la scheda **Destinazioni back-end.**
20. Per **Tipo di destinazione**, selezionare Pool **back-end**, quindi selezionare **appGatewayBackendPool**.
21. Per **l'impostazione HTTP**, selezionare **Crea nuovo**.
22. Per **Nome impostazione HTTP**, *digitare http-setting-01*.
23. Per **Protocollo back-end**, selezionare **HTTP**.
24. Per **Porta back-end**digitare *80*.
25. Accettare le impostazioni predefinite rimanenti e selezionare **Aggiungi**.
26. Nella pagina **Aggiungi regola** di routing selezionare **Aggiungi**.
27. Selezionare **Successivo: Tag**.
28. Selezionare **Successivo: Revisione e creazione**.
29. Esaminare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea** per creare le risorse di rete e il gateway applicazione. La creazione del gateway applicazione può richiedere diversi minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

## <a name="add-backend-pool"></a>Aggiungere un pool back-end

Il pool back-end viene usato per instradare le richieste ai server back-end che gestiscono la richiesta. Il back-end può essere composto da schede di interfaccia di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come il servizio app di Azure.The backend can be composed of NIC, virtual machine scale sets, public IP addresses, internal IP addresses, fully qualified domain names (FQDN) and multi-tenant back-ends like Azure App Service. In questo esempio, come back-end di destinazione si usano macchine virtuali. È possibile usare macchine virtuali esistenti o crearne di nuove. In questo esempio vengono create due macchine virtuali che Azure usa come server back-end per il gateway applicazione.

A tale scopo, eseguire l'operazione seguente:

1. Creare due nuove macchine virtuali, *myVM* e *myVM2*, utilizzate come server back-end.
2. Installare IIS nelle macchine virtuali per verificare che il gateway applicazione sia stato creato correttamente.
3. Aggiungere i server back-end al pool back-end.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Selezionare **Crea una risorsa**.
2. Selezionare **Calcolo** , quindi **Macchina virtuale**.
4. Immettere i valori seguenti per la macchina virtuale:
   - Selezionare *myResourceGroupAG* per **Gruppo di risorse**.
   - *myVM* - per **Nome macchina virtuale**.
   - Selezionare **Windows Server 2019 Datacenter** per **Immagine**.
   - *azureadmin* - per il **nome utente**.
   - *Azure123456!* per la **Password**.
5. Accettare le impostazioni predefinite rimanenti e selezionare **Avanti : Dischi**.
6. Accettare le impostazioni predefinite e selezionare **Avanti : Rete**.
7. Assicurarsi che **myVNet** sia selezionato per la rete virtuale e che la subnet sia **myBackendSubnet**.
8. Accettare le impostazioni predefinite rimanenti e selezionare **Avanti : Gestione**.
9. Selezionare **Disattivato** per disabilitare la diagnostica di avvio.
10. Accettare le impostazioni predefinite rimanenti e selezionare **Avanti : Avanzate**.
11. Selezionare **Avanti : Tag**.
12. Selezionare **Avanti : Rivedere e creare**.
13. Verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**. La creazione della macchina virtuale potrebbe richiedere alcuni minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

### <a name="install-iis"></a>Installare IIS

1. Aprire Cloud Shell e assicurarsi che sia impostato su **PowerShell**.
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

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. Creare una seconda macchina virtuale e installare IIS seguendo la procedura appena completata. Immettere myVM2 per il nome e per VMName in Set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Aggiungere i server back-end al pool back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.
2. Selezionare **Pool back-end**. Selezionare **appGatewayBackendPool**.
3. In **Tipo di destinazione** selezionare Macchina **virtuale** e in **Destinazione**selezionare la versione vNIC associata a myVM.
4. Ripetere l'operazione per aggiungere MyVM2.
   ![privato-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. selezionare **Salva.**

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Controllare l'IP front-end assegnato facendo clic sulla pagina **Configurazioni IP front-end** nel portale.
    ![privato-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copiare l'indirizzo IP privato e quindi incollarlo nella barra degli indirizzi del browser in una macchina virtuale nella stessa rete virtuale o locale che dispone di connettività a questa rete virtuale e provare ad accedere al gateway applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se si desidera monitorare l'integrità del back-end, vedere Registri di [diagnostica e integrità back-end per](application-gateway-diagnostics.md)il gateway applicazione .
