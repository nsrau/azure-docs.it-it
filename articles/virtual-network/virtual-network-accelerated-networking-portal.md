---
title: Rete accelerata per una macchina virtuale - Portale | Documentazione Microsoft
description: "Informazioni su come configurare la funzionalità Rete accelerata per una macchina virtuale di Azure usando il portale di Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: af4515c6-4377-4d4a-a104-18fe1348252c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 90dc3b45f470bfac03c34c1a20a7b54b9acbc281
ms.lasthandoff: 03/31/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-the-azure-portal"></a>Rete accelerata per una macchina virtuale con il portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

La funzionalità Rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le macchine virtuali, migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate ignora l'host del percorso dati riducendo la latenza, l'instabilità e l'utilizzo della CPU e può essere usato con i carichi di lavoro di rete più gravosi nei tipi di macchine virtuali supportati. Questo articolo spiega come usare il portale di Azure per configurare Rete accelerata nel modello di distribuzione Azure Resource Manager. È possibile creare una macchina virtuale con Rete accelerata anche usando Azure PowerShell. Per informazioni, fare clic sulla casella PowerShell nella parte superiore di questo articolo.

L'immagine seguente illustra le comunicazioni tra due macchine virtuali (VM), con e senza Rete accelerata:

![Confronto](./media/virtual-network-accelerated-networking-portal/image1.png)

Senza Rete accelerata, tutto il traffico di rete in ingresso e in uscita dalla macchina virtuale deve attraversare l'host e il commutatore virtuale. Quest'ultimo è responsabile dell'applicazione di tutti i criteri al traffico di rete, ad esempio gruppi di sicurezza di rete, elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzati. Per altre informazioni, vedere l'articolo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualizzazione rete Hyper-V e commutatore virtuale).

Con Rete accelerata il traffico di rete raggiunge la scheda di rete (NIC) e viene quindi inoltrato alla macchina virtuale. Il carico di tutti i criteri di rete applicati dal commutatore virtuale senza Rete accelerata viene ripartito e applicato all'hardware. L'applicazione dei criteri all'hardware permette alla scheda di rete di inoltrare il traffico di rete direttamente alla macchina virtuale ignorando l'host e il commutatore virtuale, pur mantenendo tutti i criteri applicati all'host.

I vantaggi della funzionalità Rete accelerata si applicano solo alla macchina virtuale in cui è abilitata. Per ottenere risultati ottimali, è consigliabile abilitarla in almeno due macchine virtuali connesse alla stessa rete virtuale. Nella comunicazione tra reti virtuali o nella connessione locale questa funzionalità ha un impatto minimo sulla latenza complessiva.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Vantaggi
* **Latenza più bassa e più pacchetti al secondo (pps):** rimuovendo il commutatore virtuale dal percorso dati viene eliminato il tempo di attesa dei pacchetti nell'host per l'elaborazione dei criteri, aumentando così il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
* **Instabilità ridotta:** l'elaborazione del commutatore dipende dalla quantità di criteri da applicare e dal carico di lavoro della CPU che esegue l'elaborazione. La ripartizione del carico di applicazione dei criteri nell'hardware elimina tale variabilità recapitando i pacchetti direttamente alla macchina virtuale, rimuovendo l'host dalle comunicazioni con la macchina virtuale nonché tutte le interruzioni software e i cambi di contesto.
* **Utilizzo ridotto della CPU:** ignorando il commutatore virtuale nell'host si ottiene un minore utilizzo della CPU per l'elaborazione del traffico di rete.

## <a name="limitations"></a>Limitazioni
L'uso della funzionalità Rete accelerata presenta le limitazioni seguenti:

* **Creazione di un'interfaccia di rete:** la funzionalità Rete accelerata può essere abilitata solo per una nuova interfaccia di rete.  Non può essere abilitata su un'interfaccia di rete esistente.
* **Creazione di una macchina virtuale:** un'interfaccia di rete con Rete accelerata abilitata può essere associata a una macchina virtuale solo durante la creazione della macchina virtuale. Non è possibile collegare l'interfaccia di rete a una macchina virtuale esistente.
* **Aree:** sono disponibili solo le aree di Azure Stati Uniti centro-occidentali ed Europa occidentale. In futuro il set di aree disponibili verrà ampliato.
* **Sistemi operativi supportati:** Microsoft Windows Server 2012 R2 e Windows Server 2016 Technical Preview 5. Presto verrà aggiunto il supporto per Linux e Windows Server 2012.
* **Dimensioni della macchina virtuale:** le uniche dimensioni delle istanze di macchina virtuale supportate sono Standard_D15_v2 e Standard_DS15_v2. Per altre informazioni, vedere l'articolo [Dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . In futuro il set di dimensioni delle istanze di macchina virtuale supportate verrà ampliato.

Le modifiche apportate a queste limitazioni verranno annunciate nella pagina relativa agli [aggiornamenti sulla rete virtuale di Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Creare una macchina virtuale Windows con Rete accelerata
1. Registrarsi all'anteprima inviando un messaggio di posta elettronica a [Sottoscrizioni di Rete accelerata](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID sottoscrizione e l'uso previsto. Non completare i passaggi rimanenti finché non si riceve un messaggio di posta elettronica che comunica che si è stati accettati nell'anteprima.
2. Accedere al portale di Azure all'indirizzo http://portal.azure.com.
3. Creare una macchina virtuale seguendo la procedura riportata nell'articolo [Creare una macchina virtuale Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Selezionare le opzioni seguenti:
   
   * Selezionare uno dei sistemi operativi elencati nella sezione Limitazioni di questo articolo.
   * Selezionare una delle località (aree) elencate nella sezione Limitazioni di questo articolo.
   * Selezionare una delle dimensioni di macchina virtuale elencate nella sezione Limitazioni di questo articolo. Se una delle dimensioni supportate non è inclusa nell'elenco, fare clic su **Visualizza tutto** nel pannello **Scegli una dimensione** per selezionare una dimensione da un elenco espanso.
   * Nel pannello **Impostazioni** fare clic su *Abilitata* per **Rete accelerata**, come mostrato nella figura seguente:
     
       ![Impostazioni](./media/virtual-network-accelerated-networking-portal/image3.png)
     
     > [!NOTE]
     > L'opzione Rete accelerata viene visualizzata solo se l'utente rispetta i requisiti seguenti:
     > 
     > * È stato accettato nell'anteprima
     > * Ha selezionato un sistema operativo, una località e una dimensione di macchina virtuale supportati e riportati nella sezione Limitazioni di questo articolo.
     > 
     > 
4. Dopo aver creato la macchina virtuale, scaricare il [driver di Rete accelerata](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), connettersi e accedere alla macchina virtuale, quindi eseguire il programma di installazione del driver all'interno della macchina virtuale.
5. Fare clic con il pulsante destro del mouse sul pulsante Windows e scegliere **Gestione dispositivi**. Espandere l'opzione **Rete** e verificare che la voce **Scheda Ethernet VF Mellanox ConnectX-3** sia visualizzata, come illustra la figura seguente:
   
    ![Gestione dispositivi](./media/virtual-network-accelerated-networking-portal/image2.png)


