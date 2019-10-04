---
title: Diagnosticare un problema di routing di rete di una macchina virtuale - esercitazione - Portale di Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come diagnosticare un problema di routing di rete di una macchina virtuale usando la funzionalità Hop successivo di Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 3ad9cd8b620b55aaa17e84343a82ac361081de44
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684509"
---
# <a name="tutorial-diagnose-a-virtual-machine-network-routing-problem-using-the-azure-portal"></a>Esercitazione: Diagnosticare un problema di routing di rete di una macchina virtuale tramite il portale di Azure

Quando si distribuisce una macchina virtuale, Azure crea per essa più route predefinite. È possibile creare le proprie route per eseguire l'override delle route predefinite di Azure. In alcuni casi, una route personalizzata può comportare l'impossibilità per una macchina virtuale di comunicare con altre risorse. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una macchina virtuale
> * Verificare la comunicazione con un URL usando la funzionalità Hop successivo di Network Watcher
> * Verificare la comunicazione con un indirizzo IP
> * Diagnosticare un problema di routing e informazioni su come risolverlo

Se si preferisce, è possibile diagnosticare un problema di routing di rete di una macchina virtuale tramite l'[interfaccia della riga di comando di Azure](diagnose-vm-network-routing-problem-cli.md) o [Azure PowerShell](diagnose-vm-network-routing-problem-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Selezionare **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter** o **Ubuntu Server 17.10 VM**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myVm|
    |Nome utente| Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Gruppo di risorse| Selezionare **Crea nuovo** e immettere **myResourceGroup**.|
    |Località| Selezionare **Stati Uniti orientali**.|

4. Selezionare una dimensione per la VM e quindi selezionare **Seleziona**.
5. In **Impostazioni**  accettare tutte le impostazioni predefinite e scegliere **OK**.
6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della VM. La distribuzione della VM richiede alcuni minuti. Attendere che la macchina virtuale completi la distribuzione prima di continuare con i passaggi rimanenti.

## <a name="test-network-communication"></a>Testare la comunicazione di rete

Per testare la comunicazione di rete con Network Watcher è necessario innanzitutto abilitare un Network Watcher almeno in un'area di Azure, quindi usare la funzionalità Hop successivo di Network Watcher per testare la comunicazione.

### <a name="enable-network-watcher"></a>Abilitare Network Watcher

Se si dispone già di un Network Watcher abilitato in almeno un'area, passare al paragrafo [Usare la funzionalità Hop successivo](#use-next-hop).

1. Nel portale selezionare **Tutti i servizi**. Nella **casella del filtro** immettere *Network Watcher*. Selezionare **Network Watcher** quando viene visualizzato tra i risultati.
2. Selezionare **Area** per espandere e quindi selezionare **...** a destra di **Stati Uniti orientali**, come mostrato nell'immagine seguente:

    ![Abilitare Network Watcher](./media/diagnose-vm-network-traffic-filtering-problem/enable-network-watcher.png)

3. Selezionare **Abilita Network Watcher**.

### <a name="use-next-hop"></a>Usare la funzionalità Hop successivo

Azure crea automaticamente le route per le destinazioni predefinite. È possibile creare route personalizzate per eseguire l'override delle route predefinite. In alcuni casi, le route personalizzate possono impedire la comunicazione. Usare la funzionalità Hop successivo di Network Watcher per determinare quali route vengono usate da Azure per indirizzare il traffico.

1. Nel portale di Azure, selezionare **Hop successivo** in **Network Watcher**.
2. Selezionare la sottoscrizione, immettere o selezionare i valori seguenti e quindi selezionare **Hop successivo**, come illustrato nell'immagine seguente:

    |Impostazione                  |Valore                                                   |
    |---------                |---------                                               |
    | Gruppo di risorse          | Selezionare myResourceGroup                                 |
    | Macchina virtuale         | Selezionare myVm                                            |
    | interfaccia di rete       | myvm - Il nome dell'interfaccia di rete potrebbe essere diverso.   |
    | Indirizzo IP di origine       | 10.0.0.4                                               |
    | Indirizzo IP di destinazione  | 13.107.21.200: uno degli indirizzi per <www.bing.com>. |

    ![Hop successivo](./media/diagnose-vm-network-routing-problem/next-hop.png)

    Dopo alcuni secondi, il risultato indica che il tipo di Hop successivo è **Internet** e che l'**ID tabella di route** è **System Route**. Questo risultato informa l'utente che è presente una route di sistema valida per la destinazione.

3. Modificare l'**Indirizzo IP di destinazione** in *172.31.0.100* e selezionare nuovamente **Hop successivo**. Il risultato informa l'utente che **Nessuno** è il **Tipo di hop successivo**e che l'**ID tabella di route** è **System Route**. Questo risultato informa che, nonostante sia presente una route di sistema valida per la destinazione, non vi è alcun hop successivo per indirizzare il traffico alla destinazione.

## <a name="view-details-of-a-route"></a>Visualizzare i dettagli di una route

1. Per analizzare ulteriormente il routing, esaminare la route valide per l'interfaccia di rete. Nella casella di ricerca nella parte superiore del portale, immettere *myvm* (o altro nome dell'interfaccia di rete controllata). Selezionare **myvm** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Route valide** in **SUPPORTO E RISOLUZIONE DEI PROBLEMI**, come illustrato nell'immagine seguente:

    ![Route valide](./media/diagnose-vm-network-routing-problem/effective-routes.png)

    Quando è stato eseguito il test con 13.107.21.200 in [Usa hop successivo](#use-next-hop), la route con prefisso 0.0.0.0/0 è stata usata per instradare il traffico all'indirizzo, poiché nessun'altra route include l'indirizzo. Per impostazione predefinita, tutti gli indirizzi non specificati entro il prefisso dell'indirizzo di un'altra route vengono instradati su internet.

    Quando è stato eseguito il test con 172.31.0.100, tuttavia, il risultato informa che non è presente alcun hop successivo. Come si può notare nell'immagine precedente, benché vi sia una route predefinita per il prefisso 172.16.0.0/12, che include l'indirizzo 172.31.0.100, il **TIPO DI HOP SUCCESSIVO** è **Nessuno**. Azure crea una route predefinita per 172.16.0.0/12, ma non specifica alcun tipo di hop successivo fino a quando non c'è un motivo per farlo. Se, ad esempio, è stato aggiunto l'intervallo di indirizzi 172.16.0.0/12 allo spazio di indirizzi della rete virtuale, Azure modifica il **TIPO DI HOP SUCCESSIVO** in **Rete virtuale** per la route. Il segno di spunta indica **Rete virtuale** come **TIPO DI HOP SUCCESSIVO**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una macchina virtuale e diagnosticato il routing di rete dalla macchina virtuale. Si è appreso che Azure crea più route predefinite diverse e testa il routing verso due destinazioni diverse. Per altre informazioni, vedere il [routing in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e come [creare route personalizzate](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Per le connessioni delle macchine virtuali in uscita è anche possibile determinare la latenza, il traffico di rete consentito e negato tra la macchina virtuale e un endpoint e la route usata per un endpoint, tramite la funzionalità di [risoluzione dei problemi di connessione](network-watcher-connectivity-portal.md) di Network Watcher. Informazioni su come monitorare la comunicazione nel corso del tempo tra una macchina virtuale e un endpoint, ad esempio un indirizzo IP o un URL, usando la funzionalità di monitoraggio connessione di Network Watcher.

> [!div class="nextstepaction"]
> [Monitorare una connessione di rete](connection-monitor.md)