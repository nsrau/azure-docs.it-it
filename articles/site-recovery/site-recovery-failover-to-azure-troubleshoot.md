---
title: Risoluzione dei problemi di failover di Azure | Microsoft Docs
description: Questo articolo descrive come risolvere gli errori comuni durante l'esecuzione del failover in Azure
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 838eac510fc17d56f808f541f4e205a279f63c56
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318892"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Risolvere gli errori durante il failover di una macchina virtuale in Azure

Durante il failover di una macchina virtuale in Azure è possibile che l'utente riceva uno dei seguenti errori. Per risolvere i problemi, usare i passaggi descritti per ogni condizione di errore.

## <a name="failover-failed-with-error-id-28031"></a>Il failover non è riuscito con errore ID 28031

Site Recovery non è riuscito a creare un'operazione di failover sulla macchina virtuale in Azure. Questo può verificarsi a causa di uno dei motivi seguenti:

* Non è disponibile una quota sufficiente per creare la macchina virtuale: è possibile controllare la quota disponibile in Sottoscrizione -> Utilizzo e quote. È possibile aprire una [nuova richiesta di supporto](http://aka.ms/getazuresupport) per aumentare la quota.

* Si sta tentando di effettuare il failover delle macchine virtuali delle famiglie di dimensioni diverse nello stesso set di disponibilità. Assicurarsi di aver scelto la stessa famiglia di dimensioni per tutte le macchine virtuali nello stesso set di disponibilità. È possibile modificare le dimensioni passando alle impostazioni Calcolo e rete della macchina virtuale e ritentando il failover.

* Nella sottoscrizione esiste un criterio che impedisce la creazione di una macchina virtuale. Modificare i criteri per consentire la creazione di una macchina virtuale, quindi ripetere il failover.

## <a name="failover-failed-with-error-id-28092"></a>Il failover non è riuscito con errore ID 28092

Site Recovery non è riuscito a creare un'interfaccia di rete per il failover della macchina virtuale. Assicurarsi di disporre di una quota sufficiente per creare interfacce di rete nella sottoscrizione. È possibile controllare la quota disponibile in Sottoscrizione -> Utilizzo e quote. È possibile aprire una [nuova richiesta di supporto](http://aka.ms/getazuresupport) per aumentare la quota. Se si dispone di una quota sufficiente, potrebbe trattarsi di un problema intermittente, riprovare. Se il problema persiste anche dopo vari tentativi, lasciare un commento alla fine di questo documento.  

## <a name="failover-failed-with-error-id-70038"></a>Il failover non è riuscito con errore ID 70038

Site Recovery non è riuscito a creare un'operazione di failover sulla macchina virtuale classica in Azure. Questo può verificarsi perché:

* Non esiste una delle risorse, ad esempio una rete virtuale necessaria per la macchina virtuale da creare. Creare la rete virtuale come specificato nelle impostazioni di Calcolo e rete della macchina virtuale o modificare l'impostazione per una rete virtuale che esiste già, quindi riprovare a eseguire il failover.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Impossibile connettersi o stabilire una connessione RDP/SSH alla macchina virtuale con failover perché il pulsante di connessione è disabilitato nella macchina virtuale

Se il pulsante di connessione è disabilitato e non si è connessi ad Azure tramite una connessione Express Route o VPN da sito a sito, seguire questa procedura:

1. Andare a **Macchina virtuale** > **Rete** e fare clic sul nome dell'interfaccia di rete richiesta.  ![interfaccia di rete](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Passare a **Configurazioni IP** e quindi fare clic sul campo del nome della configurazione IP richiesta. ![Configurazioni IP](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Per abilitare l'indirizzo IP pubblico, fare clic su **Abilita**. ![Abilita IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Fare clic su **Configurare le impostazioni necessarie** > **Crea nuovo**. ![Crea nuovo](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Immettere il nome dell'indirizzo pubblico, scegliere le opzioni predefinite per **SKU** e **assegnazione** e quindi fare clic su **OK**.
6. Per salvare le modifiche apportate, fare clic su **Salva**.
7. Chiudere i pannelli e passare alla sezione **Panoramica** della macchina virtuale per connettersi o stabilire la connessione RDP.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Impossibile connettersi o stabilire una connessione RDP/SSH alla macchina virtuale con failover anche se il pulsante di connessione è disponibile (non disabilitato) nella macchina virtuale

Controllare **Diagnostica di avvio** nella macchina virtuale e verificare gli errori come indicato in questo articolo.

1. Se la macchina virtuale non si è avviata, provare a effettuare il failover a un punto di ripristino meno recente.
2. Se l'applicazione all'interno della macchina virtuale non si avvia, provare a effettuare il failover a un punto di ripristino coerente con l'app.
3. Se la macchina virtuale è aggiunta a un dominio, verificare che il controller di dominio funzioni accuratamente. Questa operazione può essere eseguita seguendo la procedura indicata di seguito.
    a. Creare una nuova macchina virtuale nella stessa rete.

    b.  Assicurarsi che possa essere aggiunta allo stesso dominio in cui è previsto che la macchina virtuale con failover venga avviata.

    c. Se il controller di dominio **non** funziona accuratamente, provare ad accedere alla macchina virtuale con failover usando un account amministratore locale.
4. Se si usa un server DNS personalizzato, assicurarsi che sia raggiungibile. Questa operazione può essere eseguita seguendo la procedura indicata di seguito.
    a. Creare una nuova macchina virtuale nella stessa rete e b. Verificare se la macchina virtuale è in grado di procedere alla risoluzione dei nomi tramite il server DNS personalizzato.

>[!Note]
>Per l'abilitazione di qualsiasi altra impostazione che non sia Diagnostica di avvio è necessario installare l'agente di macchine virtuali di Azure nella macchina virtuale prima del failover.

## <a name="next-steps"></a>Passaggi successivi

Se l'utente richiede ancora assistenza, registrare la query nel [forum di Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) o lasciare un commento alla fine di questo documento. È disponibile una community attiva in grado di offrire supporto.
