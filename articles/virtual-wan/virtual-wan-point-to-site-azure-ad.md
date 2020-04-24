---
title: 'Configurare Azure AD autenticazione per la connessione VPN utente: rete WAN virtuale'
description: Informazioni su come configurare l'autenticazione Azure Active Directory per la VPN utente.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 703b832d58f2374eac131cfd380ba27f2c890618
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059496"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Configurare l'autenticazione Azure Active Directory per la VPN utente

Questo articolo illustra come configurare l'autenticazione Azure AD per la VPN utente in una rete WAN virtuale per la connessione alle risorse in Azure tramite una connessione VPN OpenVPN. L'autenticazione di Azure Active Directory è disponibile solo per i gateway che usano il protocollo OpenVPN e i client che eseguono Windows.

Questo tipo di connessione richiede la configurazione di un client nel computer client. Per ulteriori informazioni sulla rete WAN virtuale, vedere [Panoramica di WAN virtuale](virtual-wan-about.md).

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare una rete WAN
> * Creare un hub
> * Creare una configurazione da punto a sito
> * Scaricare un profilo client VPN
> * Applicare la configurazione da punto a sito a un hub
> * Connettere una rete virtuale a un hub
> * Scaricare e applicare la configurazione del client VPN
> * Visualizzare la rete WAN virtuale

![Diagramma della rete WAN virtuale](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Si ha una rete virtuale a cui ci si vuole connettere. Verificare che nessuna delle subnet della rete locale possa sovrapporsi alle reti virtuali a cui ci si vuole connettere. Per creare una rete virtuale nel portale di Azure, vedere l'[Avvio rapido](../virtual-network/quick-create-portal.md).

* La rete virtuale non presenta alcun gateway di rete virtuale. Se la rete virtuale presenta un gateway (VPN o ExpressRoute) è necessario rimuovere tutti i gateway. Questa configurazione richiede che le reti virtuali siano invece connesse al gateway dell'hub della rete WAN virtuale.

* Ottenere un intervallo di indirizzi IP per l'area dell'hub. L'hub è una rete virtuale che viene creata e usata dalla rete WAN virtuale. L'intervallo di indirizzi specificati per l'hub non può sovrapporsi ad alcuna delle reti virtuali esistenti a cui ci si connette. Inoltre non può sovrapporsi agli intervalli di indirizzi a cui ci si connette in locale. Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, coordinarsi con qualcuno che possa fornire tali dettagli.

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Creare una rete WAN virtuale

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

1. Passare alla pagina WAN virtuale. Nel portale fare clic su **+Crea una risorsa**. Digitare **Rete WAN virtuale** nella casella di ricerca e premere INVIO.
2. Selezionare **Rete WAN virtuale** nei risultati. Nella pagina della rete WAN virtuale fare clic su **Crea** per aprire la pagina Crea rete WAN.
3. Nella pagina **Crea rete WAN**, nella scheda **Nozioni di base**, compilare i campi seguenti:

   ![Rete WAN virtuale](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Sottoscrizione** - Selezionare la sottoscrizione che si vuole usare.
   * **Gruppo di risorse** : Crea nuovo o Usa esistente.
   * **Posizione gruppo di risorse** - Scegliere una posizione per le risorse nell'elenco a discesa. Una rete WAN è una risorsa globale e non si trova in un'area specifica. Tuttavia, è necessario selezionare un'area per poter gestire e individuare più facilmente la risorsa WAN creata.
   * **Nome** - Digitare il nome che si vuole usare per la rete WAN.
   * **Tipo:** Standard. Con la creazione di una rete WAN di base è possibile creare solo un hub di base. Gli hub di base possono supportare solo la connettività VPN da sito a sito.
4. Dopo aver completato i campi, selezionare **Rivedi e crea**.
5. Al termine della convalida selezionare **Crea** per creare la rete WAN virtuale.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Creare un hub virtuale vuoto

1. Nella rete WAN virtuale selezionare Hub e fare clic su **+Nuovo hub**.

   ![nuovo sito](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Nella pagina Crea hub virtuale completare i campi seguenti.

   **Area**: selezionare l'area in cui si vuole distribuire l'hub virtuale.

   **Nome**: immettere il nome con cui denominare l'hub virtuale.

   **Spazio di indirizzi privato dell'hub**: intervallo di indirizzi dell'hub nella notazione CIDR.

   ![nuovo sito](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Fare clic su **Rivedi e crea**.
4. Nella pagina **Convalida superata** fare clic su **Crea**.

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Creare una nuova configurazione da punto a sito

Una configurazione da punto a sito definisce i parametri per la connessione di client remoti.

1. Nella sezione della rete WAN virtuale selezionare **Configurazioni VPN utente**.

   ![nuova configurazione](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. Fare clic su **+Crea configurazione VPN utente**.

   ![nuova configurazione](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Immettere le informazioni e fare clic su **Crea**

   ![nuova configurazione](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Modificare l'assegnazione dell'hub

1. Passare al pannello **Hub** nella rete WAN virtuale.
2. Selezionare l'hub a cui si vuole associare la configurazione del server VPN e fare clic sui puntini di sospensione (...).

   ![nuovo sito](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Fare clic su **Modifica hub virtuale**.
4. Selezionare la casella di controllo **Includi gateway da punto a sito** e selezionare l'**unità di scala gateway** che si vuole usare.

   ![nuovo sito](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Immettere il **Pool di indirizzi** da cui gli indirizzi IP verranno assegnati ai client VPN.
6. Fare clic su **conferma**.
7. Il completamento dell'operazione potrà richiedere fino a 30 minuti.

## <a name="download-vpn-profile"></a><a name="device"></a>Scaricare il profilo VPN

Usare il profilo VPN per configurare i client.

1. Nella pagina della rete WAN virtuale fare clic su **Configurazioni VPN utente**.
2. Nella parte superiore della pagina fare clic su **Download user VPN config** (Scarica configurazione VPN utente).
3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.
4. Usare il file del profilo per configurare i client VPN.

## <a name="configure-user-vpn-clients"></a>Configurare i client VPN utente

Per connettersi, è necessario scaricare il client VPN di Azure e importare il profilo client VPN scaricato nei passaggi precedenti in ogni computer che dovrà connettersi alla rete virtuale.

> [!NOTE]
> Azure AD autenticazione è supportata solo per le&reg; connessioni di protocollo OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Per scaricare il client VPN di Azure

Usare questo [collegamento](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) per scaricare il client VPN di Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Per importare un profilo client

1. Nella pagina selezionare **Importa**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Individuare il file XML del profilo e selezionarlo. Con il file selezionato, selezionare **Apri**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specificare il nome del profilo e selezionare **Salva**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Selezionare **Connetti** per connettersi alla VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde mostrerà lo stato **Connesso**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Per eliminare un profilo client

1. Selezionare i puntini di sospensione (...) accanto al profilo client da eliminare. Selezionare quindi **Rimuovi**.

    ![Elimina](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Per procedere all'eliminazione, selezionare **Rimuovi**.

    ![Elimina](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnosticare i problemi di connessione

1. Per diagnosticare i problemi di connessione, è possibile usare lo strumento **Diagnosi**. Selezionare i puntini di sospensione (...) accanto alla connessione VPN che si vuole diagnosticare per visualizzare il menu. Selezionare quindi **Diagnosi**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Nella pagina **Proprietà connessione** selezionare **Esegui diagnosi**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Accedere con le credenziali personali.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Visualizzare i risultati della diagnosi.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
2. Nella pagina Panoramica ogni punto sulla mappa rappresenta un hub.
3. Nella sezione degli hub e delle connessioni è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).
