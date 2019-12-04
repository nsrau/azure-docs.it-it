---
title: 'Esercitazione: Diagnosticare un problema di comunicazione tra reti tramite il portale di Azure'
titleSuffix: Azure Network Watcher
description: Questa esercitazione illustra come diagnosticare un problema di comunicazione tra una rete virtuale di Azure connessa a una rete locale o a un'altra rete virtuale, tramite un gateway di rete virtuale di Azure, tramite la funzionalità di diagnostica VPN di Network Watcher.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: e2ec59cf609fcde79d289e321331ca5018401a5e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419726"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Esercitazione: Diagnosticare un problema di comunicazione tra reti tramite il portale di Azure

Un gateway di rete virtuale connette una rete virtuale di Azure a una rete locale o a un'altra rete virtuale. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Diagnosticare un problema con un gateway di rete virtuale con funzionalità di diagnostica VPN di Network Watcher
> * Diagnosticare un problema con una connessione gateway
> * Risolvere un problema con un gateway

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per usare la diagnostica VPN, è necessario un gateway VPN esistente, in esecuzione. Se non si dispone di un gateway VPN esistente per la diagnosi, è possibile distribuire uno usando uno [script di PowerShell](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). È possibile eseguire lo script di PowerShell da:
- **Un'installazione locale di PowerShell**: Per questo script è necessario il modulo `Az` di Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.
- **Azure Cloud Shell**: [Azure Cloud Shell](https://shell.azure.com/powershell) contiene la versione più recente di PowerShell installata e configurata e connette l'utente ad Azure.

Lo script richiede circa un'ora per creare un gateway VPN. Nei passaggi rimanenti si presuppone che il gateway che si sta diagnosticando è quello distribuito da questo script. Se invece si diagnostica il gateway esistente, i risultati variano.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-network-watcher"></a>Abilitare Network Watcher

Se si dispone già di un Network Watcher abilitato nell'area Stati Uniti orientali, passare al paragrafo [Diagnosticare un gateway](#diagnose-a-gateway).

1. Nel portale selezionare **Tutti i servizi**. Nella **casella del filtro** immettere *Network Watcher*. Selezionare **Network Watcher** quando viene visualizzato tra i risultati.
2. Selezionare **Area** per espandere e quindi selezionare **...** a destra di **Stati Uniti orientali**, come mostrato nell'immagine seguente:

    ![Abilitare Network Watcher](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Selezionare **Abilita Network Watcher**.

## <a name="diagnose-a-gateway"></a>Diagnosticare un gateway

1. Sul lato sinistro del portale selezionare **Altri servizi**.
2. Iniziare a digitare *network watcher* nella casella **Filtro**. Selezionare **Network Watcher** quando viene visualizzato tra i risultati della ricerca.
3. In **STRUMENTI DI DIAGNOSTICA DI RETE** selezionare **Diagnostica VPN**.
4. Selezionare **Account di archiviazione**, quindi selezionare l'account di archiviazione in cui si intende scrivere le informazioni diagnostiche.
5. Nell'elenco **Account di archiviazione** selezionare l'account di archiviazione da usare. Se non è presente un account di archiviazione, selezionare **+ Account di archiviazione**, immettere o selezionare le informazioni necessarie e quindi selezionare **Crea** per crearne uno. Se è stato creato un gateway VPN tramite lo script in [prerequisiti](#prerequisites), è possibile creare l'account di archiviazione nello stesso gruppo di risorse *TestRG1* del gateway.
6. Nell'elenco dei **contenitori** selezionare il contenitore che si vuole usare e quindi selezionare **Seleziona**. Se non si dispone di alcun contenitore, selezionare **+ Contenitore**, immettere un nome per il contenitore, quindi selezionare **OK**.
7. Selezionare un gateway, quindi **Avvia risoluzione dei problemi**. Come illustrato nell'immagine seguente, il test viene eseguito in un gateway denominato **Vnet1GW**:

    ![Diagnostica VPN](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Durante l'esecuzione del test, viene visualizzato **In esecuzione** nella colonna **STATO RISOLUZIONE DEI PROBLEMI** dove viene visualizzato **Non avviato** nell'immagine precedente. L'esecuzione del test può richiedere alcuni minuti.
9. Visualizzare lo stato di un test completato. L'immagine seguente mostra i risultati di stato di un test di diagnostica completato:

    ![Stato](./media/diagnose-communication-problem-between-networks/status.png)

    È possibile visualizzare che lo **STATO RISOLUZIONE DEI PROBLEMI** è **Non integro**, nonché il **Riepilogo** e i **Dettagli** del problema nella scheda **Stato**.
10. Quando si seleziona la scheda **Azione** diagnostica VPN offre informazioni aggiuntive. Nell'esempio illustrato nella figura seguente diagnostica VPN informa l'utente che è necessario controllare l'integrità di ogni connessione:

    ![Azione](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Diagnosticare una connessione gateway

Un gateway è connesso ad altre reti tramite una connessione gateway. Sia il gateway che le connessioni gateway devono essere integri per una corretta comunicazione tra una rete virtuale e una rete connessa.

1. Completare di nuovo il passaggio 7 di [Diagnosticare un gateway](#diagnose-a-gateway), questa volta, selezionando una connessione. Nell'esempio seguente viene testata una connessione denominata **VNet1toSite1**:

    ![Connessione](./media/diagnose-communication-problem-between-networks/connection.png)

    Il test viene eseguito per alcuni minuti.
2. Dopo aver completato il test della connessione, si ricevono risultati simili ai risultati mostrati nelle immagini seguenti nelle schede **Stato** e **Azione**:

    ![Stato della connessione](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Azione di connessione](./media/diagnose-communication-problem-between-networks/connection-action.png)

    Diagnostica VPN comunica il problema nella scheda **Stato** scheda e offre alcuni suggerimenti di possibili cause del problema nella scheda **Azione**.

    Se il gateway testato è quello distribuito dallo [script](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) in [Prerequisiti](#prerequisites), il problema nella scheda **Stato** e i primi due elementi nella scheda  **Azioni** rappresentano esattamente il problema. Lo script configura un indirizzo IP segnaposto, 23.99.221.164, per il dispositivo gateway VPN locale.

    Per risolvere il problema, è necessario assicurarsi che il gateway VPN locale sia [configurato correttamente](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e modificare l'indirizzo IP configurato tramite lo script per il gateway di rete locale con l'indirizzo pubblico effettivo del gateway VPN locale.

## <a name="clean-up-resources"></a>Pulire le risorse

Se è stato creato un gateway VPN tramite lo script nei [prerequisiti](#prerequisites) esclusivamente per completare questa esercitazione, non è più necessario, eliminare il gruppo di risorse e tutte le risorse che contiene:

1. Immettere *TestRG1* nella casella di **ricerca** nella parte superiore del portale. Dopo aver individuato **TestRG1** nei risultati della ricerca, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *TestRG1* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come diagnosticare un problema con un gateway di rete virtuale. È possibile registrare le comunicazioni di rete da e verso una VM in modo da poter esaminare il log per verificare la presenza di anomalie. Per informazioni su questa operazione, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Registrare il traffico di rete da e verso una macchina virtuale](network-watcher-nsg-flow-logging-portal.md)
