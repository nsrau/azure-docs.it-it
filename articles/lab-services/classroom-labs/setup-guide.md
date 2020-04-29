---
title: Guida all'installazione di Lab in aula accelerata per Azure Lab Services
description: Questa guida consente agli autori di Lab di configurare rapidamente un account Lab da usare all'interno della scuola.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878103"
---
# <a name="classroom-lab-setup-guide"></a>Guida all'installazione di Lab in aula

Il processo per la pubblicazione di un Lab per gli studenti può richiedere fino a diverse ore, a seconda del numero di macchine virtuali (VM) che verranno create nel Lab. Consentire almeno un giorno per configurare un Lab, per verificare che funzioni correttamente e per concedere tempo sufficiente per la pubblicazione delle macchine virtuali degli studenti.

## <a name="understand-the-lab-requirements-of-your-class"></a>Comprendere i requisiti del Lab della classe

Prima di configurare un nuovo Lab, è necessario prendere in considerazione le domande seguenti.

### <a name="what-software-requirements-does-the-class-have"></a>Quali sono i requisiti software della classe?

In base agli obiettivi di apprendimento della classe, decidere quale sistema operativo, applicazioni e strumenti devono essere installati nelle macchine virtuali del Lab. Per configurare le macchine virtuali del Lab, sono disponibili tre opzioni:

- **Usare un'immagine di Azure Marketplace**: Azure Marketplace offre centinaia di immagini che è possibile usare durante la creazione di un Lab. Per alcune classi, una di queste immagini potrebbe già contenere tutti gli elementi necessari per la classe.

- **Creare una nuova immagine personalizzata**: è possibile creare un'immagine personalizzata usando un'immagine di Azure Marketplace come punto di partenza e personalizzarla installando software aggiuntivo e apportando modifiche di configurazione.

- **Usare un'immagine personalizzata esistente**: è possibile riutilizzare le immagini personalizzate esistenti create in precedenza o create da altri amministratori o docenti presso la propria scuola. A tale scopo è necessario che gli amministratori abbiano configurato una raccolta di immagini condivise, ovvero un repository per il salvataggio di immagini personalizzate.

> [!NOTE]
> Gli amministratori hanno la responsabilità di abilitare le immagini di Azure Marketplace e le immagini personalizzate per poterle usare. Coordinarsi con il reparto IT per assicurarsi che le immagini necessarie siano abilitate. Le immagini personalizzate create vengono abilitate automaticamente per l'uso nei laboratori di cui si è proprietari.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quali sono i requisiti hardware della classe?

È possibile scegliere tra diverse dimensioni di calcolo:

- Dimensioni di virtualizzazione annidate, in modo che sia possibile concedere l'accesso agli studenti a una macchina virtuale in grado di ospitare più macchine virtuali nidificate. Ad esempio, è possibile usare questa dimensione di calcolo per i corsi di rete.

- Dimensioni GPU, in modo che gli studenti possano utilizzare tipi di applicazioni a elevato utilizzo di computer. Questa scelta, ad esempio, può essere appropriata per l'intelligenza artificiale e l'apprendimento automatico.

Per un elenco completo delle dimensioni di calcolo disponibili, vedere la guida relativa al [dimensionamento delle macchine virtuali](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) .

> [!NOTE]
> A seconda dell'area geografica del Lab, è possibile che sia disponibile un minor numero di dimensioni di calcolo, dal momento che varia in base all'area. In genere, è consigliabile selezionare le dimensioni di calcolo minime più vicine alle proprie esigenze. Con Azure Lab Services è possibile configurare un nuovo Lab con una capacità di calcolo diversa in un secondo momento, se necessario.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quali dipendenze ha la classe sulle risorse di rete o Azure esterne?

Se le macchine virtuali del lab devono usare risorse esterne, ad esempio un database, una condivisione file o un server licenze, coordinarsi con gli amministratori per assicurarsi che il Lab abbia accesso a queste risorse.

Per l'accesso alle risorse di Azure *non* protette da una rete virtuale, non è necessario cercare una configurazione aggiuntiva da parte degli amministratori. È possibile accedere a queste risorse tramite la rete Internet pubblica.

> [!NOTE]
> È necessario valutare se è possibile ridurre le dipendenze del Lab a risorse esterne fornendo la risorsa direttamente nella macchina virtuale. Ad esempio, per eliminare la necessità di leggere i dati da un database esterno, è possibile installare il database direttamente nella macchina virtuale.  

### <a name="how-will-costs-be-controlled"></a>Come verranno controllati i costi?

Lab Services usa un modello di determinazione prezzi con pagamento in base al consumo, ovvero si paga solo per il tempo di esecuzione di una macchina virtuale Lab. Per controllare i costi, sono disponibili tre opzioni che vengono in genere usate insieme tra loro:

- **Pianificazione**: una pianificazione consente di controllare automaticamente quando le macchine virtuali dei Lab vengono avviate e arrestate.
- **Quota**: la quota controlla il numero di ore per cui gli studenti avranno accesso a una macchina virtuale al di fuori delle ore pianificate. Se la quota viene raggiunta mentre uno studente lo usa, la macchina virtuale viene arrestata automaticamente. Lo studente non è in grado di riavviare la macchina virtuale, a meno che la quota non venga aumentata.
- **Arresto automatico**: se abilitata, l'impostazione di arresto automatico causa l'arresto automatico delle macchine virtuali Windows dopo un determinato periodo di tempo, dopo che uno studente si è disconnesso da una sessione di Remote Desktop Protocol (RDP). Per impostazione predefinita, questa impostazione è disabilitata.  

    > [!NOTE]
    > Questa impostazione è attualmente disponibile solo per Windows.

### <a name="how-will-students-save-their-work"></a>In che modo gli studenti salveranno il proprio lavoro?

Agli studenti viene assegnata una propria VM, che viene assegnata per la durata del Lab. Possono scegliere di:

- Salvare direttamente nella macchina virtuale.
- Salvare in un percorso esterno, ad esempio OneDrive o GitHub.

È possibile configurare OneDrive automaticamente per gli studenti nelle proprie macchine virtuali del Lab.

> [!NOTE]
> Per assicurarsi che gli studenti abbiano continuato ad accedere al proprio lavoro salvato al di fuori del Lab e al termine della classe, è consigliabile che gli studenti salvino il proprio lavoro in un repository esterno.

### <a name="how-will-students-connect-to-their-vm"></a>In che modo gli studenti si connetteranno alla VM?

Per il protocollo RDP per le macchine virtuali Windows, è consigliabile che gli studenti usino il [client desktop remoto Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Desktop remoto client supporta Mac, Chromebook e Windows.

Per le macchine virtuali Linux, gli studenti possono usare SSH o RDP. Per fare in modo che gli studenti si connettano tramite RDP, è necessario installare e configurare i pacchetti RDP e GUI necessari.

## <a name="set-up-your-lab"></a>Configurare un ambiente lab

Dopo aver compreso i requisiti per il Lab della classe, si è pronti per configurarli. Seguire i collegamenti in questa sezione per informazioni su come configurare il Lab.

1. **Creare un Lab.** Per istruzioni, vedere l'esercitazione relativa alla [creazione di un Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) per le aule.

    > [!NOTE]
    > Se la classe richiede la virtualizzazione nidificata, vedere la procedura descritta in [Abilitazione della virtualizzazione annidata](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalizzare le immagini e pubblicare macchine virtuali del Lab.** Connettersi a una macchina virtuale speciale denominata VM modello. Vedere i passaggi nelle guide seguenti:
    - [Creare e gestire una macchina virtuale modello](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Usare raccolta immagini condivisa](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se si usa Windows, è necessario vedere anche le istruzioni riportate in [preparazione di una macchina virtuale modello Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Queste istruzioni includono i passaggi per la configurazione di OneDrive e Office per gli studenti da usare.

1. **Gestire il pool di macchine virtuali e la capacità.** È possibile aumentare o ridurre facilmente la capacità delle macchine virtuali, in base alle esigenze della classe. Tenere presente che l'aumento della capacità della macchina virtuale può richiedere diverse ore, perché ciò comporta l'impostazione di nuove macchine virtuali. Vedere i passaggi descritti in [configurazione e gestione di un pool di macchine virtuali](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Aggiungere e gestire gli utenti del Lab.** Per aggiungere utenti al Lab, vedere la procedura nelle esercitazioni seguenti:
   - [Aggiungere utenti al lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Invia inviti agli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Per informazioni sui tipi di account che gli studenti possono usare, vedere [account per studenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Imposta i controlli dei costi.** Per controllare i costi del Lab, impostare pianificazioni, quote e spegnimento automatico. Vedere le esercitazioni seguenti:

   - [Impostare una pianificazione](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > A seconda del tipo di sistema operativo installato, l'avvio di una macchina virtuale potrebbe richiedere diversi minuti. Per assicurarsi che una macchina virtuale Lab sia pronta per l'uso durante le ore pianificate, è consigliabile avviare le VM 30 minuti prima.

   - [Impostare quote per gli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [impostare quote aggiuntive per un utente specifico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Abilitare l'arresto automatico alla disconnessione](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Le pianificazioni, le quote e la chiusura automatica non si applicano alla macchina virtuale del modello. Di conseguenza, è necessario assicurarsi di arrestare la macchina virtuale del modello quando non è in uso. In caso contrario, continua a sostenere i costi. Inoltre, per impostazione predefinita, quando si crea un Lab, la macchina virtuale del modello viene avviata automaticamente. Assicurarsi di completare immediatamente la configurazione del Lab e arrestare la macchina virtuale del modello.

1. **Usare il dashboard.** Per istruzioni, vedere [uso del dashboard del Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Il costo stimato visualizzato nel dashboard è il costo massimo che è possibile prevedere per l'utilizzo da parte degli studenti del Lab. Ad esempio, per gli studenti *non* verranno addebitate le ore di quota inutilizzate. I costi stimati *non* riflettono gli addebiti per l'uso della macchina virtuale modello o della raccolta di immagini condivise.

## <a name="next-steps"></a>Passaggi successivi

- [Tenere traccia dell'utilizzo di un lab per le classi](tutorial-track-usage.md)
  
- [Accedere a un lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)