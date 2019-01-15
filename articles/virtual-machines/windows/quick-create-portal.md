---
title: Guida introduttiva - Creare una macchina virtuale Windows nel portale di Azure | Microsoft Docs
description: Questa guida introduttiva illustra come usare il portale di Azure per creare una macchina virtuale Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/14/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6a3a15e8b80983a8df29dd6fe8d13b94c1d3c3a1
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118916"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Avvio rapido: Creare una macchina virtuale di Windows nel portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Questo metodo fornisce un'interfaccia utente basata su browser per creare le macchine virtuali e le risorse associate. Questa guida introduttiva illustra come usare il portale di Azure per distribuire una macchina virtuale in Azure che esegue Windows Server 2016. Per vedere la macchina virtuale in azione, eseguire RDP nella macchina virtuale e installare il server Web IIS.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

1. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace, cercare e selezionare **Windows Server 2016 Datacenter** e quindi scegliere **Crea**.

1. Nella scheda **Nozioni di base**, sotto **Dettagli progetto**, verificare che sia selezionata la sottoscrizione corretta e quindi scegliere **Crea nuovo** gruppo di risorse. Immettere *myResourceGroup* come nome. 

    ![Creare un nuovo gruppo di risorse per la propria macchina virtuale](./media/quick-create-portal/project-details.png)

1. Sotto **Dettagli dell'istanza**, digitare *myVM* come **nome della macchina virtuale** e scegliere *Stati Uniti orientali* come **Posizione**. Lasciare invariate le altre impostazioni predefinite.

    ![Sezione Dettagli dell'istanza](./media/quick-create-portal/instance-details.png)

1. Sotto **Account amministratore**, specificare un nome utente, ad esempio *azureuser*, e una password. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Immettere il proprio nome utente e la password](./media/quick-create-portal/administrator-account.png)

1. Sotto **Regole porta in ingresso**, scegliere **Consentire porte selezionate**, quindi selezionare **RDP (3389)** e **HTTP** dall'elenco a discesa.

    ![Aprire le porte per RDP e HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Lasciare invariate le impostazioni predefinite rimanenti, quindi selezionare il pulsante **Rivedi + Crea** nella parte inferiore della pagina.

    ![Rivedi e crea](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Creare una connessione Desktop remoto alla macchina virtuale. Queste istruzioni spiegano come connettersi alla macchina virtuale da un computer Windows. In Mac, è necessario un client RDP come questo [client Desktop remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponibile nel Mac App Store.

1. Fare clic sul pulsante **Connetti** nella pagina delle proprietà della macchina virtuale. 

    ![Connettersi a una macchina virtuale di Azure dal portale](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Nella pagina **Connetti a macchina virtuale** mantenere le opzioni predefinite per la connessione con nome DNS sulla porta 3389 e fare clic su **Scarica file RDP**.

2. Aprire il file RDP scaricato e fare clic su **Connetti** quando richiesto. 

3. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Digitare il nome utente come **localhost**\\*nomeutente*, immettere la password creata per la macchina virtuale e quindi fare clic su **OK**.

4. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

## <a name="install-web-server"></a>Installare il server Web

Per visualizzare la macchina virtuale in azione, installare il server Web IIS. Aprire un prompt di PowerShell nella VM ed eseguire questo comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Al termine chiudere la connessione RDP con la macchina virtuale.


## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

Nel portale, selezionare la macchina virtuale e nella panoramica della macchina virtuale, usare il pulsante **Fare clic per copiare** a destra dell'indirizzo IP per copiarlo e incollarlo in una scheda del browser. Si aprirà la pagina iniziale di IIS predefinita, che avrà il seguente aspetto:

![Sito IIS predefinito](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina** e quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, è stata aperta una porta di rete per il traffico Web ed è stato installato un server Web di base. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](./tutorial-manage-vm.md)
