---
title: Avvio rapido in Azure - Creare un portale di VM Windows | Microsoft Docs
description: Avvio rapido in Azure - Creare un portale di VM Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 3cb94144addabf2371c8a058cebff25def4b6338
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Creare una macchina virtuale Windows con il portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Questo metodo fornisce un'interfaccia utente basata sul browser per la creazione e la configurazione delle macchine virtuali e di tutte le risorse correlate. Questa guida introduttiva illustra la creazione di una macchina virtuale con il portale di Azure. Al termine della distribuzione, viene eseguita la connessione al server e viene installato IIS.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

2. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

3. Selezionare **Calcolo** nel pannello **Nuovo**, selezionare **Windows Server 2016 Datacenter** nel pannello **Calcolo** e poi fare clic sul pulsante **Crea**.

4. Compilare il modulo **Generale** della macchina virtuale. Il nome utente e la password immessi in questo modulo verranno usati per accedere alla macchina virtuale. Per **Gruppo di risorse** creare un nuovo gruppo. Un gruppo di risorse è un contenitore logico in cui vengono create e gestite collettivamente le risorse di Azure. Al termine fare clic su **OK**.

    ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. Scegliere una dimensione per la VM e fare clic su **Seleziona**.

6. Nel pannello delle impostazioni selezionare **Sì** in **Usa dischi gestiti**, mantenere i valori predefiniti per le altre impostazioni e fare clic su **OK**.

7. Nella pagina del riepilogo fare clic su **OK** per avviare la distribuzione della macchina virtuale.

8. Per monitorare lo stato di distribuzione fare clic sulla macchina virtuale. La VM è disponibile nel dashboard del portale di Azure o selezionando **Macchine virtuali** nel menu a sinistra. Dopo che la VM è stata creata, lo stato cambia da **Distribuzione in corso** a **In esecuzione**.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web 

Per consentire il traffico per IIS, è necessario aprire la porta 80 al traffico Web. Questo passaggio illustra come creare una regola del gruppo di sicurezza di rete per consentire le connessioni in ingresso sulla porta 80.

1. Nella sezione **Informazioni di base** del pannello della macchina virtuale fare clic sul nome del **gruppo di risorse**.
2. Nel pannello del gruppo di risorse fare clic su di **Gruppo di sicurezza di rete** nell'elenco di risorse. Il gruppo di sicurezza di rete deve avere lo stesso nome della macchina virtuale, con -nsg aggiunto alla fine.
3. Fare clic sull'intestazione **Regola di sicurezza in ingresso** per aprire l'elenco di regole in ingresso. Nell'elenco dovrebbe già essere presente una regola per RDP.
4. Fare clic su **+ Aggiungi** per aprire il pannello **Aggiungi regola di sicurezza in ingresso**.
5. In **Nome** digitare **IIS** e assicurarsi che l'**Intervallo di porte** sia impostato su 80 e l'**Azione** sia impostata su **Consenti**, quindi fare clic su **OK**.


## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Al termine della distribuzione, creare una connessione Desktop remoto alla macchina virtuale.

1. Fare clic sul pulsante **Connetti** nel pannello della macchina virtuale. Verrà creato e scaricato un file Remote Desktop Protocol, con estensione rdp.

    ![Portale 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Per connettersi alla VM, aprire il file RDP scaricato. Se richiesto, fare clic su **Connetti**. In Mac, è necessario un client RDP come questo [client Desktop remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponibile nel Mac App Store.

3. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale e quindi fare clic su **OK**.

4. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.


## <a name="install-iis-using-powershell"></a>Installare IIS tramite PowerShell

Dopo avere eseguito l'accesso alla macchina virtuale di Azure, è possibile usare una singola riga di codice di PowerShell per installare IIS e abilitare la regola del firewall locale per consentire il traffico Web.  Aprire un prompt di PowerShell ed eseguire questo comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

Dopo l'installazione di IIS e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la pagina iniziale predefinita di IIS. Ottenere l'**indirizzo IP pubblico** dal pannello della macchina virtuale e usarlo per aprire la pagina Web predefinita. 

![Sito IIS predefinito](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Eliminare una macchina virtuale

Quando non servono più, il comando seguente consente di rimuovere il gruppo di risorse, la VM e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="delete-virtual-machine"></a>Eliminare una macchina virtuale

Quando non serve più, eliminare il gruppo di risorse e tutte le risorse correlate. A tale scopo selezionare il gruppo di risorse nel pannello della macchina virtuale e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

[Installare un ruolo e configurare l'esercitazione sul firewall](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Esplorare gli esempi dell'interfaccia della riga di comando per la distribuzione della VM](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

