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
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c28686c3b6494a0cf8938d39ab9b8338de7aa0c1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012581"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Guida introduttiva: Creare una macchina virtuale Windows nel portale di Azure

È possibile creare macchine virtuali di Azure tramite il portale di Azure. Questo metodo fornisce un'interfaccia utente basata su browser per creare le macchine virtuali e le risorse associate. Questa guida introduttiva illustra come usare il portale di Azure per distribuire una macchina virtuale in Azure che esegue Windows Server 2016. Per vedere la macchina virtuale in azione, eseguire RDP nella macchina virtuale e installare il server Web IIS.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Nella casella di ricerca sopra l'elenco delle risorse di Azure Marketplace, cercare e selezionare **Windows Server 2016 Datacenter** e quindi scegliere **Crea**.

3. Specificare un nome di macchina virtuale, ad esempio *macchinavirtuale*, lasciare il tipo di disco *SSD* e quindi specificare un nome utente, ad esempio *utenteazure*. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)

5. Scegliere **Crea nuovo** per creare un nuovo gruppo di risorse e quindi specificare un nome, ad esempio *GruppoRisorse*. Scegliere la **Località** desiderata e quindi selezionare **OK**.

4. Selezionare una dimensione per la VM. È possibile filtrare in base a *Tipo di calcolo* oppure a *Tipo di disco*, ad esempio. Le dimensioni consigliate per le macchine virtuali sono *D2s_v3*.

    ![Screenshot che mostra le dimensioni delle VM](./media/quick-create-portal/create-windows-vm-portal-sizes.png)

5. In **Impostazioni**  accettare tutte le impostazioni predefinite e selezionare **OK**.

6. Nella pagina di riepilogo selezionare **Crea** per avviare la distribuzione della macchina virtuale.

7. La macchina virtuale viene aggiunta al dashboard del portale di Azure. Una volta completata la distribuzione verrà automaticamente aperto il riepilogo della macchina virtuale.

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Creare una connessione Desktop remoto alla macchina virtuale. Queste istruzioni spiegano come connettersi alla macchina virtuale da un computer Windows. In Mac, è necessario un client RDP come questo [client Desktop remoto](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) disponibile nel Mac App Store.

1. Fare clic sul pulsante **Connetti** nella pagina delle proprietà della macchina virtuale. 

    ![Connettersi a una macchina virtuale di Azure dal portale](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png)
    
2. Nella pagina **Connetti a macchina virtuale** mantenere le opzioni predefinite per la connessione con nome DNS sulla porta 3389 e fare clic su **Scarica file RDP**.

2. Aprire il file RDP scaricato e fare clic su **Connetti** quando richiesto. 

3. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Digitare il nome utente come *nomevm*\*nomeutente*, immettere la password creata per la macchina virtuale e quindi fare clic su **OK**.

4. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

## <a name="install-web-server"></a>Installare il server Web

Per visualizzare la macchina virtuale in azione, installare il server Web IIS. Aprire un prompt di PowerShell nella VM ed eseguire questo comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Al termine, chiudere la connessione RDP con la macchina virtuale.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Un gruppo di sicurezza di rete (NSG) protegge il traffico in ingresso e in uscita. Quando si crea una VM nel portale di Azure, viene creata una regola in ingresso sulla porta 3389 per le connessioni RDP. Questa macchina virtuale ospita un server Web, quindi è necessario creare una regola del gruppo di sicurezza di rete per la porta 80.

1. Nella pagina di panoramica per la macchina virtuale selezionare **Rete**.
2. Viene visualizzato l'elenco delle regole ingresso e in uscita esistenti. Scegliere **Aggiungi regola porta in ingresso**.
3. Selezionare l'opzione **Base** nella parte superiore e quindi scegliere *HTTP* nell'elenco dei servizi disponibili. Vengono forniti automaticamente la porta 80, una priorità e un nome.
4. Per creare la regola, selezionare **Aggiungi**.

## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

Dopo l'installazione di IIS e l'apertura della porta 80 nella macchina virtuale da Internet, usare il Web browser preferito per visualizzare la pagina iniziale predefinita di IIS. Usare l'indirizzo IP pubblico della macchina virtuale ottenuto in un passaggio precedente. L'esempio seguente mostra il sito Web predefinito di IIS:

![Sito IIS predefinito](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non serve più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina** e quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, è stata aperta una porta di rete per il traffico Web ed è stato installato un server Web di base. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](./tutorial-manage-vm.md)
