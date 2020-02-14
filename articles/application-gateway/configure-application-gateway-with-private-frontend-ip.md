---
title: Configurare un endpoint del servizio di bilanciamento del carico interno (ILB)
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce informazioni su come configurare un gateway applicazione con un indirizzo IP front-end privato
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: f56929e14aef34f675139782328ed5c559df12c7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198599"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurare un gateway applicazione con un endpoint del servizio di bilanciamento del carico interno (ILB)

Applicazione Azure gateway può essere configurato con un indirizzo VIP con connessione Internet o con un endpoint interno non esposto a Internet. Un endpoint interno usa un indirizzo IP privato per il front-end, noto anche come endpoint del servizio di *bilanciamento del carico interno (ILB)* .

La configurazione del gateway tramite un indirizzo IP privato front-end è utile per le applicazioni line-of-business interne che non sono esposte a Internet. È utile anche per i servizi e i livelli all'interno di un'applicazione multilivello che si trovano in un limite di sicurezza non esposto a Internet, ma che richiedono comunque la distribuzione del carico Round Robin, la viscosità della sessione o la terminazione di Secure Sockets Layer (SSL).

Questo articolo illustra i passaggi per configurare un gateway applicazione con un indirizzo IP privato front-end usando il portale di Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com>.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale. È possibile creare una nuova rete virtuale oppure usarne una esistente. In questo esempio si creerà una nuova rete virtuale. È possibile creare una rete virtuale durante la creazione del gateway applicazione. Le istanze del gateway applicazione vengono create in subnet separate. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end.

1. Espandere il menu del portale e selezionare **Crea una risorsa**.
2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco In primo piano.
3. Immettere *myAppGateway* come nome del gateway applicazione e *myResourceGroupAG* come nuovo gruppo di risorse.
4. Per **area**selezionare **(Stati Uniti) Stati Uniti centrali**.
5. Per **livello**selezionare **standard**.
6. In **Configura rete virtuale** selezionare **Crea nuovo**e quindi immettere i valori seguenti per la rete virtuale:
   - *myVNet* come nome della rete virtuale.
   - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
   - *myAGSubnet* come nome della subnet.
   - *10.0.0.0/24* come spazio indirizzi della subnet.
   - *myBackendSubnet* : per il nome della subnet back-end.
   - *10.0.1.0/24* : per lo spazio di indirizzi della subnet back-end.

    ![Creare una rete virtuale](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selezionare **OK** per creare la rete virtuale e la subnet.
7. Selezionare **Avanti: front-end**.
8. In **tipo di indirizzo IP**front-end selezionare **privato**.

   Per impostazione predefinita, si tratta di un'assegnazione di indirizzi IP dinamici. Il primo indirizzo disponibile della subnet configurata viene assegnato come indirizzo IP front-end.
   > [!NOTE]
   > Una volta allocato, il tipo di indirizzo IP (statico o dinamico) non può essere modificato in un secondo momento.
9. Selezionare **Avanti: backend**.
10. Selezionare **Aggiungi un pool back-end**.
11. Per **nome**digitare *appGatewayBackendPool*.
12. Per **Aggiungi pool back-end senza destinazioni**, selezionare **Sì**. Le destinazioni verranno aggiunte in un secondo momento.
13. Selezionare **Aggiungi**.
14. Selezionare **Avanti: configurazione**.
15. In **regole di routing**selezionare **Aggiungi una regola**.
16. Per **Nome regola**digitare *RRULE-01*.
17. Per **nome listener**digitare *listener-01*.
18. Per **IP**front-end selezionare **privato**.
19. Accettare i valori predefiniti rimanenti e selezionare la scheda **destinazioni back-end** .
20. In **tipo di destinazione**selezionare **pool back-end**e quindi selezionare **appGatewayBackendPool**.
21. Per **impostazione http**selezionare **Crea nuovo**.
22. Per **Nome impostazione http**Digitare *http-setting-01*.
23. Per **protocollo backend**selezionare **http**.
24. Per la **porta back-end**Digitare *80*.
25. Accettare le impostazioni predefinite rimanenti e selezionare **Aggiungi**.
26. Nella pagina **Aggiungi una regola di routing** selezionare **Aggiungi**.
27. Selezionare **Avanti: Tag**.
28. Selezionare **Avanti: verifica + crea**.
29. Rivedere le impostazioni nella pagina Riepilogo e quindi selezionare **Crea** per creare le risorse di rete e il gateway applicazione. La creazione del gateway applicazione può richiedere diversi minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

## <a name="add-backend-pool"></a>Aggiungere un pool back-end

Il pool back-end viene usato per instradare le richieste ai server back-end che gestiscono la richiesta. Il back-end può essere costituito da schede di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come app Azure Service. In questo esempio, come back-end di destinazione si usano macchine virtuali. È possibile usare macchine virtuali esistenti o crearne di nuove. In questo esempio vengono create due macchine virtuali che Azure usa come server back-end per il gateway applicazione.

A tale scopo, eseguire l'operazione seguente:

1. Creare due nuove macchine virtuali, *myVM* e *myVM2*, usate come server back-end.
2. Installare IIS nelle macchine virtuali per verificare che il gateway applicazione sia stato creato correttamente.
3. Aggiungere i server back-end al pool back-end.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Selezionare **Crea una risorsa**.
2. Selezionare **calcolo** , quindi selezionare **macchina virtuale**.
4. Immettere i valori seguenti per la macchina virtuale:
   - Selezionare *myResourceGroupAG* per **gruppo di risorse**.
   - *myVM* -per **nome macchina virtuale**.
   - Selezionare **Windows Server 2019 datacenter** per l' **immagine**.
   - *azureadmin* : per il **nome utente**.
   - *Azure123456!* per la **password**.
5. Accettare i valori predefiniti rimanenti e fare clic su **Avanti: dischi**.
6. Accettare le impostazioni predefinite e selezionare **Avanti: rete**.
7. Assicurarsi che **myVNet** sia selezionato per la rete virtuale e che la subnet sia **myBackendSubnet**.
8. Accettare i valori predefiniti rimanenti e fare clic su **Avanti: gestione**.
9. Selezionare **Disattivato** per disabilitare la diagnostica di avvio.
10. Accettare le impostazioni predefinite rimanenti e selezionare **Avanti: avanzate**.
11. Selezionare **Avanti: Tag**.
12. Selezionare **Avanti: verifica + crea**.
13. Verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**. La creazione della macchina virtuale può richiedere diversi minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

### <a name="install-iis"></a>Installare IIS

1. Aprire il Cloud Shell e verificare che sia impostato su **PowerShell**.
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



3. Creare una seconda macchina virtuale e installare IIS seguendo la procedura appena completata. Immettere myVM2 per il nome e per VMName in set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Aggiungere i server back-end al pool back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.
2. Selezionare **Pool back-end**. Selezionare **appGatewayBackendPool**.
3. In **tipo di destinazione** selezionare **macchina virtuale** e in **destinazione**selezionare il vNIC associato a myVM.
4. Ripetere l'aggiunta per aggiungere MyVM2.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Selezionare **Salva.**

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Controllare l'IP front-end assegnato facendo clic sulla pagina **configurazioni IP** front-end nel portale.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copiare l'indirizzo IP privato e quindi incollarlo nella barra degli indirizzi del browser in una macchina virtuale nella stessa VNet o in locale che disponga della connettività a questo VNet e provare ad accedere al gateway applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se si vuole monitorare l'integrità del back-end, vedere [log di diagnostica e integrità back-end per il gateway applicazione](application-gateway-diagnostics.md).
