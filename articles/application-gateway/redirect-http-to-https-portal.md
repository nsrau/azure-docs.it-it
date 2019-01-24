---
title: Creare un gateway applicazione con reindirizzamento da HTTP a HTTPS tramite il portale di Azure
description: Informazioni su come creare un gateway applicazione con traffico reindirizzato da HTTP a HTTPS usando il portale di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 12/7/2018
ms.author: victorh
ms.openlocfilehash: c27c31bc2f21cfae9036849973301a66a437de42
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435244"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Creare un gateway applicazione con reindirizzamento da HTTP a HTTPS tramite il portale di Azure

È possibile usare il portale di Azure per creare un [gateway applicazione](overview.md) con un certificato per la terminazione SSL. Viene usata una regola di routing per reindirizzare il traffico HTTP verso la porta HTTPS nel gateway applicazione. In questo esempio viene creato anche un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per il pool back-end del gateway applicazione che contiene due istanze di macchine virtuali.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un certificato autofirmato
> * Configurare una rete
> * Creare un gateway applicazione con il certificato
> * Aggiungere un listener e una regola di reindirizzamento
> * Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per questa esercitazione è necessario il modulo Azure PowerShell 3.6 o versione successiva per creare un certificato e installare IIS. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Per eseguire i comandi in questa esercitazione, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per l'uso in ambiente di produzione è necessario importare un certificato valido firmato da un provider attendibile. Per questa esercitazione viene creato un certificato autofirmato usando il comando [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). È possibile usare [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) con l'identificazione personale restituita per esportare un file pfx dal certificato.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

L'output sarà simile al risultato seguente:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Usare l'identificazione personale per creare il file pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create è necessaria una rete virtuale. In questo esempio vengono create due subnet: una per il gateway applicazione e l'altra per i server back-end. È possibile creare una rete virtuale durante la creazione del gateway applicazione.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
3. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco In primo piano.
4. Immettere i valori seguenti per il gateway applicazione:

    - *myAppGateway* come nome del gateway applicazione.
    - *myResourceGroupAG* come nuovo gruppo di risorse.

    ![Creare il nuovo gateway applicazione](./media/create-url-route-portal/application-gateway-create.png)

5. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
6. Fare clic su **Scegliere una rete virtuale**, **Crea nuova** e quindi immettere i valori seguenti per la rete virtuale:

    - *myVNet* come nome della rete virtuale.
    - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
    - *myAGSubnet* come nome della subnet.
    - *10.0.1.0/24* come spazio indirizzi della subnet.

    ![Creare una rete virtuale](./media/create-url-route-portal/application-gateway-vnet.png)

7. Fare clic su **OK** per creare la rete virtuale e la subnet.
8. In **Configurazione dell'indirizzo IP front-end** verificare che **Tipo di indirizzo IP** sia impostato su **pubblico** e che **Crea nuovo** sia selezionato. Immettere *myAGPublicIPAddress* per il nome. Accettare i valori predefiniti per le altre impostazioni e quindi fare clic su **OK**.
9. In **Configurazione listener** selezionare **HTTPS**, quindi **Selezionare un file**, passare al file *c:\appgwcert.pfx* e scegliere **Apri**.
10. Digitare *appgwcert* come nome del certificato e *Azure123456!* come password.
11. Lasciare disabilitato il web application firewall e quindi selezionare **OK**.
12. Rivedere le impostazioni nella pagina di riepilogo e quindi fare clic su **OK** per creare le risorse di rete e il gateway applicazione. La creazione del gateway applicazione potrebbe richiedere alcuni minuti. Attendere il completamento della distribuzione prima di passare alla sezione successiva.

### <a name="add-a-subnet"></a>Aggiungere una subnet

1. Selezionare **Tutte le risorse** nel menu a sinistra e quindi selezionare **myVNet** nell'elenco delle risorse.
2. Selezionare **Subnet** e quindi fare clic su **Subnet**.

    ![Creare una subnet](./media/create-url-route-portal/application-gateway-subnet.png)

3. Digitare *myBackendSubnet* come nome della subnet.
4. Digitare *10.0.2.0/24* come intervallo di indirizzi e quindi selezionare **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Aggiungere un listener e una regola di reindirizzamento

### <a name="add-the-listener"></a>Aggiungere il listener

In primo luogo, aggiungere il listener denominato *myListener* per la porta 80.

1. Aprire il gruppo di risorse **myResourceGroupAG** e selezionare **myAppGateway**.
2. Selezionare **Listener** e quindi **+ Base**.
3. Digitare *MyListener* per il nome.
4. Digitare *httpPort* come nome della nuova porta front-end e *80* per la porta.
5. Verificare che il protocollo sia impostato su **HTTP**, quindi selezionare **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Aggiungere una regola di routing con una configurazione di reindirizzamento

1. In **myAppGateway** selezionare **Regole** e quindi selezionare **+Base**.
2. Per **Nome**, digitare *Rule2*.
3. Assicurarsi che come listener sia selezionato **MyListener**.
4. Selezionare la casella di controllo **Configura reindirizzamento**.
5. Per **Tipo di reindirizzamento**, selezionare **Permanente**.
6. Per **Destinazione di reindirizzamento**, selezionare **Listener**.
7. Verificare che il **Listener di destinazione**sia impostato su **appGatewayHttpListener**.
8. Selezionare le caselle di controllo **Includi la stringa di query** e **Includi percorso**.
9. Selezionare **OK**.

## <a name="create-a-virtual-machine-scale-set"></a>Creare un set di scalabilità di macchine virtuali

In questo esempio viene creato un set di scalabilità di macchine virtuali per fornire i server per il pool back-end nel gateway applicazione.

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale.
2. Selezionare **Calcolo**.
3. Nella casella di ricerca digitare *set di scalabilità* e premere INVIO.
4. Selezionare **Set di scalabilità di macchine virtuali**, quindi selezionare **Crea**.
5. Per **Nome del set di scalabilità di macchine virtuali** digitare *myvmss*.
6. Per Immagine del disco del sistema operativo,** assicurarsi che sia selezionato **Windows Server 2016 Datacenter**.
7. Per **Gruppo di risorse**  selezionare **myResourceGroupAG**.
8. Per **Nome utente** digitare *azureuser*.
9. Per **Password** digitare *Azure123456!* e confermare la password.
10. Per **Numero di istanze**, verificare che il valore sia **2**.
11. Per la **Dimensioni istanze** selezionare **D2s_v3**.
12. In **Rete** assicurarsi che **Scegliere le opzioni di bilanciamento del carico** sia impostato su **Gateway applicazione**.
13. Assicurarsi che **Gateway applicazione** sia impostato su **myAppGateway**.
14. Assicurarsi che **Subnet** sia impostato su **myBackendSubnet**.
15. Selezionare **Create**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Associare il set di scalabilità al pool back-end appropriato

L'interfaccia utente del portale del set di scalabilità di macchine virtuali crea un nuovo pool back-end per il set di scalabilità, ma lo si vuole associare all'appGatewayBackendPool esistente.

1. Aprire il gruppo di risorse **myResourceGroupAG**.
2. Selezionare **myAppGateway**.
3. Selezionare **Pool back-end**.
4. Selezionare **myAppGatewaymyvmss**.
5. Selezionare **Rimuovi tutte le destinazioni dal pool back-end**.
6. Selezionare **Salva**.
7. Al termine del processo, selezionare il pool back-end **myAppGatewaymyvmss**, selezionare **Elimina** e quindi **OK** per confermare.
8. Selezionare **appGatewayBackendPool**.
9. In **Destinazioni** selezionare **VMSS**.
10. In **VMSS** selezionare **myvmss**.
11. In **Configurazioni dell'interfaccia di rete** selezionare **myvmssNic**.
12. Selezionare **Salva**.

### <a name="upgrade-the-scale-set"></a>Aggiornare il set di scalabilità

Infine, è necessario aggiornare il set di scalabilità con queste modifiche.

1. Selezionare il set di scalabilità **myvmss**.
2. In **Impostazioni** selezionare **Istanze**.
3. Selezionare entrambe le istanze e quindi **Aggiorna**.
4. Selezionare **Sì** per confermare.
5. Dopo il completamento, tornare a **myAppGateway** e selezionare **Pool back-end**. A questo punto **appGatewayBackendPool** dovrebbe avere due destinazioni e **myAppGatewaymyvmss** zero destinazioni.
6. Selezionare **myAppGatewaymyvmss**, quindi selezionare **Elimina**.
7. Selezionare **OK** per confermare.

### <a name="install-iis"></a>Installare IIS

Un modo semplice per installare IIS nel set di scalabilità è usare PowerShell. Dal portale, fare clic sull'icona Cloud Shell e assicurarsi che **PowerShell** sia selezionato.

Incollare il codice seguente nella finestra di PowerShell e premere INVIO.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Aggiornare il set di scalabilità

Dopo aver modificato le istanze con IIS, è necessario aggiornare di nuovo il set di scalabilità con questa modifica.

1. Selezionare il set di scalabilità **myvmss**.
2. In **Impostazioni** selezionare **Istanze**.
3. Selezionare entrambe le istanze e quindi **Aggiorna**.
4. Selezionare **Sì** per confermare.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

È possibile ottenere l'indirizzo IP pubblico dell'applicazione dalla pagina di panoramica del gateway applicazione.

1. Selezionare **myAppGateway**.
2. Nella pagina **Panoramica** prendere nota dell'indirizzo IP in **Indirizzo IP pubblico front-end**.

3. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Ad esempio: http://52.170.203.149

   ![Avviso di sicurezza](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Per accettare l'avviso di sicurezza se si è usato un certificato autofirmato, selezionare **Dettagli** e quindi **Continua per la pagina Web**. Il sito Web IIS protetto viene quindi visualizzato come illustrato nell'esempio seguente:

   ![Testare l'URL di base nel gateway applicazione](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [Creare un gateway applicazione con reindirizzamento interno](redirect-internal-site-powershell.md).