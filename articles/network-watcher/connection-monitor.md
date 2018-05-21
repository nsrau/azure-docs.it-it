---
title: Monitorare la comunicazione di rete - Esercitazione - Portale di Azure | Microsoft Docs
description: Informazioni su come monitorare la comunicazione di rete tra due macchine virtuali con la funzionalità di monitoraggio della connessione di Network Watcher di Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: bfd9552a0d7c3b1e631fcc1a25d240608754c6a3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Esercitazione: Monitorare la comunicazione di rete tra due macchine virtuali tramite il portale di Azure

La corretta comunicazione tra una macchina virtuale (VM) e un endpoint, ad esempio, un'altra VM, può essere fondamentale per l'organizzazione. In alcuni casi, vengono introdotte modifiche di configurazione che possono interrompere la comunicazione. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare due VM
> * Monitorare la comunicazione tra le VM con la funzionalità di monitoraggio della connessione di Network Watcher
> * Diagnosticare un problema di comunicazione tra due VM e informazioni su come risolverlo

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-vms"></a>Creare VM

Creare due macchine virtuali.

### <a name="create-the-first-vm"></a>Creare la prima VM

1. Selezionare **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo**, quindi selezionare un sistema operativo. In questa esercitazione viene usato **Windows Server 2016 Datacenter**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myVm1|
    |Nome utente| Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Gruppo di risorse| Selezionare **Crea nuovo** e immettere **myResourceGroup**.|
    |Località| Selezionare **Stati Uniti orientali**.|

4. Selezionare una dimensione per la VM e quindi selezionare **Seleziona**.
5. In **Impostazioni** selezionare **Estensioni**. Selezionare **Aggiungi estensione**, quindi **Agente Network Watcher per Windows**, come illustrato nell'immagine seguente:

    ![Estensione agente Network Watcher](./media/connection-monitor/nw-agent-extension.png)

6. In **Agente Network Watcher per Windows** selezionare **Crea**, in **Installa estensione** selezionare **OK**, quindi in **Estensioni**, selezionare **OK**.
7. Accettare le impostazioni predefinite per le **Impostazioni** restanti e fare clic su **OK**.
8. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della VM.

### <a name="create-the-second-vm"></a>Creare la seconda VM

Completare nuovamente i passaggi descritti in [Creare la prima VM](#create-the-first-vm) con le modifiche seguenti:

|Passaggio|Impostazione|Valore|
|---|---|---|
| 1 | Selezionare **Ubuntu Server 17.10 VM** |                                                                         |
| 3 | NOME                              | myVM2                                                                   |
| 3 | Tipo di autenticazione               | Incollare la chiave pubblica SSH o selezionare **Password** e immettere una password. |
| 3 | Gruppo di risorse                    | Selezionare **Usa esistente** e selezionare **myResourceGroup**.                 |
| 6 | Estensioni                        | **Agente di rete per Linux**                                             |

La distribuzione della VM richiede alcuni minuti. Attendere che la VM completi la distribuzione prima di continuare con i passaggi rimanenti.

## <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione

Creare un monitoraggio della connessione per monitorare la comunicazione della connessione tramite la porta TCP 22 da *myVm1* a *myVm2*.

1. Sul lato sinistro del portale selezionare **Altri servizi**.
2. Iniziare a digitare *network watcher* nella casella **Filtro**. Selezionare **Network Watcher** quando viene visualizzato tra i risultati della ricerca.
3. In **MONITORAGGIO** selezionare **Monitoraggio connessione**.
4. Selezionare **+ Aggiungi**.
5. Immettere o selezionare le informazioni per la connessione da monitorare e quindi selezionare **Aggiungi**. Nell'esempio illustrato nella figura seguente viene monitorata la connessione dalla VM *myVm1* alla VM *myVm2* tramite la porta 22:

    | Impostazione                  | Valore               |
    | ---------                | ---------           |
    | NOME                     | myVm1-myVm2(22)     |
    | Sorgente                   |                     |
    | Macchina virtuale          | myVm1               |
    | Destination              |                     |
    | Selezionare una macchina virtuale |                     |
    | Macchina virtuale          | myVM2               |
    | Porta                     | 22                  |

    ![Aggiungere un monitoraggio connessione](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Visualizzare un monitoraggio della connessione

1. Completare i passaggi da 1 a 3 in [Creare un monitoraggio della connessione](#create-a-connection-monitor) per visualizzare il monitoraggio connessione. Verrà visualizzato un elenco dei monitoraggi della connessione, come illustrato nell'immagine seguente:

    ![Monitoraggi della connessione](./media/connection-monitor/connection-monitors.png)

2. Selezionare il monitoraggio con il nome **myVm1-myVm2(22)**, come illustrato nell'immagine precedente, per visualizzare i dettagli per il monitoraggio, come illustrato nell'immagine seguente:

    ![Dettagli del monitoraggio](./media/connection-monitor/vm-monitor.png)

    Tenere presente quanto segue:

    | Elemento                     | Valore                      | Dettagli                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Raggiungibile                  | Consente di conoscere se l'endpoint è raggiungibile o meno.|
    | AVG. ROUND TRIP          | Consente di conoscere il tempo di round trip per stabilire la connessione, in millisecondi. Il monitoraggio della connessione esegue il probe della connessione ogni 60 secondi per consentire di monitorare la latenza nel corso del tempo.                                         |
    | Hops                     | Il monitoraggio della connessione consente di conoscere gli hop tra i due endpoint. In questo esempio la connessione è compresa tra due VM nella stessa rete virtuale, pertanto c'è solo un hop, all'indirizzo IP 10.0.0.5. Se qualsiasi sistema esistente o route personalizzata, instrada il traffico tra le VM tramite un gateway VPN o appliance virtuale di rete, ad esempio, vengono elencati altri hop.                                                                                                                         |
    | STATO                   | I segni di spunta verde per ogni endpoint consentono di verificare che ogni endpoint sia integro.    ||

## <a name="view-a-problem"></a>Visualizzare un problema

Per impostazione predefinita, Azure consente la comunicazione su tutte le porte tra le VM nella stessa rete virtuale. Nel tempo, l'utente o un'altra persona nell'organizzazione, potrebbe sostituire le regole predefinite di Azure, provocando inavvertitamente un errore di comunicazione. Completare i passaggi seguenti per creare un problema di comunicazione e visualizzare nuovamente il monitoraggio della connessione:

1. Nella casella di ricerca nella parte superiore del portale immettere *myResourceGroup*. Selezionare il gruppo di risorse **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare il gruppo di sicurezza di rete **myVm2-nsg**.
3. Selezionare **Regole di sicurezza in ingresso**, quindi **Aggiungi**, come illustrato nell'immagine seguente:

    ![Regole di sicurezza in ingresso](./media/connection-monitor/inbound-security-rules.png)

4. La regola predefinita che consente la comunicazione tra tutte le VM in una rete virtuale è la regola denominata **AllowVnetInBound**. Creare una regola con una priorità maggiore (un numero inferiore) rispetto alla regola **AllowVnetInBound** che nega la comunicazione in ingresso sulla porta 22. Selezionare o immettere le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Aggiungi**:

    | Impostazione                 | Valore          |
    | ---                     | ---            |
    | Intervalli di porte di destinazione | 22             |
    | Azione                  | Nega           |
    | Priorità                | 100            |
    | NOME                    | DenySshInbound |

5. Poiché il monitoraggio della connessione esegue il probe a intervalli di 60 secondi, attendere qualche minuto e quindi sul lato sinistro del portale selezionare **Network Watcher**, **Monitoraggio della connessione** e quindi selezionare nuovamente il monitoraggio  **myVm1-myVm2(22)**. I risultati sono diversi a questo punto, come illustrato nell'immagine seguente:

    ![Monitor details fault](./media/connection-monitor/vm-monitor-fault .png)

    È possibile notare che esiste un'icona punto esclamativo di colore rosso nella colonna stato per l'interfaccia di rete **myvm2529**.

6. Per informazioni sui motivi per cui lo stato è stato modificato, selezionare 10.0.0.5, nell'immagine precedente. Il monitoraggio della connessione indica che la causa dell'errore di comunicazione è: *Traffico bloccato a causa la regola del gruppo di sicurezza di rete seguente: UserRule_DenySshInbound*.

    Se si era a conoscenza che un utente ha implementato la regola di protezione creata nel passaggio 4, si è appreso dal monitoraggio della connessione che la regola provoca il problema di comunicazione. È quindi stato possibile modificare, eseguire l'override o rimuovere la regola, per ripristinare la comunicazione tra le VM.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come monitorare una connessione tra due VM. Si è appreso che una regola del gruppo di sicurezza di rete ha impedito la comunicazione con una VM. Per altre informazioni su tutte le risposte diverse che può restituire il monitoraggio della connessione, vedere [tipi di risposta](network-watcher-connectivity-overview.md#response). È inoltre possibile monitorare una connessione tra una VM, un nome di dominio completo, un URI (Uniform Resource Identifier) o un indirizzo IP.

A un certo punto, è possibile che le risorse in una rete virtuale non siano in grado di comunicare con le risorse in altre reti connesse tramite un gateway di rete virtuale di Azure. Passare alla prossima esercitazione per apprendere come diagnosticare un problema con un gateway di rete virtuale.

> [!div class="nextstepaction"]
> [Diagnosticare problemi di comunicazione tra le reti](diagnose-communication-problem-between-networks.md)