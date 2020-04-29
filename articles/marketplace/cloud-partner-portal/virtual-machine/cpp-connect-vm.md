---
title: Connettersi alla macchina virtuale basata su Microsoft Azure | Azure Marketplace
description: L'articolo descrive come connettersi alla nuova macchina virtuale creata in Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 4aea624c2127c9b0a61d72b8d14929ce6f47df24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142498"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Connettersi alla macchina virtuale basata su Azure

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo la gestione in corso delle offerte della macchina virtuale di Azure al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [creare risorse tecniche per le macchine virtuali di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) .

Questo articolo illustra come connettersi ed eseguire l'accesso alle macchine virtuali create in Azure.  Una volta stabilita la connessione, è possibile usare la macchina virtuale come se si fosse connessi localmente al server host.

## <a name="connect-to-a-windows-based-vm"></a>Connettersi a una macchina virtuale basata su Windows

Il client desktop remoto verrà usato per connettersi alla macchina virtuale basata su Windows ospitata in Azure.  La maggior parte delle versioni di Windows in modalità nativa dispone del supporto per il protocollo RDP (Remote Desktop Protocol).  Per altre macchine, altre informazioni sui client sono disponibili in [Client Desktop remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

L'articolo seguente illustra in modo dettagliato come usare il supporto RDP di Windows per connettersi alla macchina virtuale: [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> È possibile ricevere avvisi di sicurezza durante il processo, che indicano ad esempio che il file con estensione rdp proviene da un autore sconosciuto o che le credenziali utente non possono essere verificate.  È opportuno ignorare questi avvisi.


## <a name="connect-to-a-linux-based-vm"></a>Connettersi a una macchina virtuale basata su Linux

Per connettere la macchina virtuale basata su Linux, è necessario un client SSH (Secure Shell Protocol).  In questa discussione verrà usato il terminale SHH [PuTTY](https://www.ssh.com/ssh/putty/) gratuito.

1. Accedere al [portale di Azure](https://ms.portal.azure.com). Cercare e selezionare **Macchine virtuali**. 
2. Selezionare la macchina virtuale a cui ci si vuole connettere.  
3. **Avviare** la macchina virtuale se non è già in esecuzione.
4. Fare clic sul nome della macchina virtuale da aprire nella pagina **Panoramica**.
5. Annotare l'indirizzo IP pubblico e il nome DNS della macchina virtuale.  Se questi valori non sono impostati, è necessario [creare un'interfaccia di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).

   ![Impostazioni di panoramica della macchina virtuale](./media/publishvm_019.png)
 
6. Aprire l'applicazione PuTTY.  
7. Nella finestra di dialogo per la configurazione di PuTTY immettere l'indirizzo IP e il nome DNS della macchina virtuale. 

   ![Impostazioni di terminale PuTTY](./media/publishvm_020.png)
 
8. Fare clic su **Apri** per aprire un terminale PuTTY.  
9. Quando richiesto, immettere il nome dell'account e la password dell'account VM Linux. 

Se si verificano problemi di connessione, vedere la documentazione relativa al client SSH, ad esempio [Chapter 10: Common error messages](https://www.ssh.com/ssh/putty/putty-manuals) (Capitolo 10: Messaggi di errori comuni).

Per altre informazioni, ad esempio sul modo in cui aggiungere un desktop a una macchina virtuale Linux di cui è stato eseguito il provisioning, vedere [Installare e configurare Desktop remoto per connettersi a una macchina virtuale Linux di Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Arrestare macchine virtuali non usate
Azure emette le fatture per l'hosting di macchine virtuali quando una macchina virtuale è in esecuzione o *inattiva*.  Di conseguenza, è consigliabile arrestare le macchine virtuali che non sono attualmente in uso.  Le macchine virtuali di test e di backup e ritirate sono candidate per l'arresto. Per arrestare una macchina virtuale, completare i passaggi seguenti:

1. Nel pannello **Macchine virtuali** selezionare la macchina virtuale da arrestare. 
2. Nella barra degli strumenti accanto alla parte superiore della pagina fare clic sul pulsante **Arresta**.

   ![Arrestare una macchina virtuale](./media/publishvm_018.png)

Azure arresta rapidamente la macchina virtuale in un processo denominato *deallocazione*, che non solo arresta il sistema operativo nella macchina virtuale, ma consente anche di liberare le risorse hardware e di rete di cui è stato eseguito il provisioning in precedenza.

Se si intende riattivare in seguito una macchina virtuale arrestata, selezionarla e quindi fare clic sul pulsante **Avvia**.


## <a name="next-steps"></a>Passaggi successivi

Dopo la connessione remota, si è pronti per [configurare la macchina virtuale](./cpp-configure-vm.md).
