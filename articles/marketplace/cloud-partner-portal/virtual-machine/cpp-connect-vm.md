---
title: Connettersi alla macchina virtuale basata su Microsoft Azure Azure Marketplace
description: L'articolo descrive come connettersi alla nuova macchina virtuale creata in Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 6f2652a98aa081b62ba80951212ef3a3de513593
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272662"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Connettersi alla macchina virtuale basata su Azure

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo la gestione mobile delle offerte di Macchine virtuali di Azure al Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Creare gli asset tecnici della macchina virtuale](https://aka.ms/AzureVMTechAsset) di Azure per gestire le offerte migrate.

Questo articolo illustra come connettersi ed eseguire l'accesso alle macchine virtuali create in Azure.  Dopo aver eseguito correttamente la connessione, è possibile usare la macchina virtuale come se si fosse connessi localmente al relativo server host. 

## <a name="connect-to-a-windows-based-vm"></a>Connettersi a una macchina virtuale basata su Windows

Si userà il client desktop remoto per connettersi alla macchina virtuale basata su Windows ospitata in Azure.You'll use the remote desktop client to connect to the Windows-based VM hosted on Azure.  La maggior parte delle versioni di Windows in modalità nativa dispone del supporto per il protocollo RDP (Remote Desktop Protocol).  Per altre macchine, altre informazioni sui client sono disponibili in [Client Desktop remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

L'articolo seguente illustra in modo dettagliato come usare il supporto RDP di Windows per connettersi alla macchina virtuale: [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> È possibile ricevere avvisi di sicurezza durante il processo, che indicano ad esempio che il file con estensione rdp proviene da un autore sconosciuto o che le credenziali utente non possono essere verificate.  È opportuno ignorare questi avvisi.


## <a name="connect-to-a-linux-based-vm"></a>Connettersi a una macchina virtuale basata su Linux

Per connettere la macchina virtuale basata su Linux, è necessario un client SSH (Secure Shell Protocol).  In questa discussione verrà usato il terminale SHH [PuTTY](https://www.ssh.com/ssh/putty/) gratuito.

1. Accedere al [portale di Azure](https://ms.portal.azure.com). Cercare e selezionare **Macchine virtuali**. 
2. Selezionare la macchina virtuale a cui connettersi.  
3. **Avviare** la macchina virtuale se non è già in esecuzione.
4. Fare clic sul nome della macchina virtuale da aprire nella pagina **Panoramica**.
5. Annotare l'indirizzo IP pubblico e il nome DNS della macchina virtuale.  Se questi valori non sono impostati, è necessario [creare un'interfaccia di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).

   ![Impostazioni di panoramica della macchina virtuale](./media/publishvm_019.png)
 
6. Aprire l'applicazione PuTTY.  
7. Nella finestra di dialogo per la configurazione di PuTTY immettere l'indirizzo IP e il nome DNS della macchina virtuale. 

   ![Impostazioni di terminale PuTTY](./media/publishvm_020.png)
 
8. Fare clic su **Apri** per aprire un terminale PuTTY.  
9. Quando richiesto, immettere il nome account e la password dell'account della macchina virtuale Linux.When you're prompted, enter the account name and password of your Linux VM account. 

Se si verificano problemi di connessione, vedere la documentazione relativa al client SSH, ad esempio [Chapter 10: Common error messages](https://www.ssh.com/ssh/putty/putty-manuals) (Capitolo 10: Messaggi di errori comuni).

Per altre informazioni, ad esempio sul modo in cui aggiungere un desktop a una macchina virtuale Linux di cui è stato eseguito il provisioning, vedere [Installare e configurare Desktop remoto per connettersi a una macchina virtuale Linux di Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Arrestare macchine virtuali non usate
Azure emette le fatture per l'hosting di macchine virtuali quando una macchina virtuale è in esecuzione o *inattiva*.  Di conseguenza, è consigliabile arrestare le macchine virtuali che non sono attualmente in uso.  Le macchine virtuali di test e di backup e ritirate sono candidate per l'arresto. Per arrestare una macchina virtuale, completare i passaggi seguenti:To shut down a VM, complete the following steps:

1. Nel pannello **Macchine virtuali** selezionare la macchina virtuale da arrestare. 
2. Nella barra degli strumenti accanto alla parte superiore della pagina fare clic sul pulsante **Arresta**.

   ![Arrestare una macchina virtuale](./media/publishvm_018.png)

Azure arresta rapidamente la macchina virtuale in un processo denominato *deallocazione*, che non solo arresta il sistema operativo nella macchina virtuale, ma consente anche di liberare le risorse hardware e di rete di cui è stato eseguito il provisioning in precedenza.

Se si intende riattivare in seguito una macchina virtuale arrestata, selezionarla e quindi fare clic sul pulsante **Avvia**.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver connesso in remoto, si è pronti per [configurare la macchina virtuale.](./cpp-configure-vm.md)
