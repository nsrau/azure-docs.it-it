---
title: Guida alla configurazione accelerata di un lab per le classi per Azure Lab Services
description: Questa guida consente agli autori di lab di configurare rapidamente un account apposito da usare all'interno della scuola.
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
ms.date: 05/19/2020
ms.author: spelluru
ms.openlocfilehash: 5c2ea38dfa799239adde158506e1359fb84adcc4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650528"
---
# <a name="classroom-lab-setup-guide"></a>Guida alla configurazione di un lab per le classi

Il processo per la pubblicazione di un lab per gli studenti può richiedere fino a diverse ore, a seconda del numero di macchine virtuali (VM) che verranno create nel lab. La configurazione di un lab richiede almeno un giorno per verificare che funzioni correttamente e avere il tempo sufficiente per pubblicare le macchine virtuali degli studenti.

## <a name="understand-the-lab-requirements-of-your-class"></a>Comprendere i requisiti del lab della classe

Prima di configurare un nuovo lab è consigliabile considerare le domande seguenti.

### <a name="what-software-requirements-does-the-class-have"></a>Quali sono i requisiti software della classe?

In base agli obiettivi di apprendimento della classe stabilire il sistema operativo, le applicazioni e gli strumenti da installare nelle macchine virtuali del lab. Per configurare le macchine virtuali del lab sono disponibili tre opzioni:

- **Usare un'immagine di Azure Marketplace**: Azure Marketplace offre centinaia di immagini che è possibile usare durante la creazione di un lab. Per alcune classi, una di queste immagini potrebbe già contenere tutto il necessario per la classe.

- **Creare una nuova immagine personalizzata**: è possibile creare un'immagine personalizzata usando un'immagine di Azure Marketplace come punto di partenza e personalizzandola tramite l'installazione di software aggiuntivo e modifiche di configurazione.

- **Usare un'immagine personalizzata esistente**: è possibile riusare le immagini personalizzate esistenti create in precedenza oppure create da altri amministratori o docenti della scuola. In questo caso è necessario che gli amministratori abbiano configurato una raccolta di immagini condivise, ovvero un repository per salvare le immagini personalizzate.

> [!NOTE]
> Gli amministratori sono responsabili dell'abilitazione delle immagini di Azure Marketplace e di quelle personalizzate in modo che possano essere usate. Coordinarsi con il reparto IT per verificare che le immagini necessarie siano abilitate. Le immagini personalizzate create vengono automaticamente abilitate per l'uso nei lab di cui si è proprietari.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quali sono i requisiti hardware della classe?

È possibile scegliere tra diverse dimensioni di calcolo:

- Dimensioni di virtualizzazione annidata, in modo da poter concedere agli studenti l'accesso a una macchina virtuale in grado di ospitare più macchine virtuali annidate. È possibile, ad esempio, usare queste dimensioni di calcolo per i corsi relativi alle reti.

- Dimensioni GPU, in modo che gli studenti possano usare tipi di applicazioni a elevato utilizzo di calcolo. Questa scelta può essere ad esempio appropriata per l'intelligenza artificiale e il Machine Learning.

Per l'elenco completo delle dimensioni di calcolo disponibili, vedere la guida relativa alle [dimensioni delle VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing).

> [!NOTE]
> La disponibilità delle dimensioni di calcolo varia in base all'area geografica. Pertanto è possibile che se ne visualizzi un numero minore, a seconda dell'area geografica del lab. In genere, è consigliabile selezionare le dimensioni di calcolo minime più vicine alle proprie esigenze. Con Azure Lab Services è possibile configurare un nuovo lab con una capacità di calcolo diversa in un secondo momento, se necessario.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quali sono le dipendenze della classe da risorse di Azure o di rete esterne?

Se le macchine virtuali del lab devono usare risorse esterne, ad esempio un database, una condivisione file o un server di licenze, coordinarsi con gli amministratori per assicurarsi che il lab abbia accesso a queste risorse.

Per l'accesso alle risorse di Azure *non* protette da una rete virtuale, non è necessaria alcuna configurazione aggiuntiva da parte degli amministratori. È possibile accedere a queste risorse tramite la rete Internet pubblica.

> [!NOTE]
> È consigliabile valutare se sia possibile ridurre le dipendenze del lab da risorse esterne rendendo disponibile la risorsa direttamente nella macchina virtuale. Ad esempio, per eliminare la necessità di leggere i dati da un database esterno, è possibile installare il database direttamente nella macchina virtuale.  

### <a name="how-will-costs-be-controlled"></a>Come saranno controllati i costi?

Lab Services usa un modello di prezzi con pagamento in base al consumo dove il cliente paga solo per il tempo in cui una macchina virtuale del lab è in esecuzione. Per controllare i costi sono disponibili tre opzioni che in genere vengono usate in combinazione:

- **Pianificazione**: una pianificazione consente di controllare automaticamente quando le macchine virtuali dei lab vengono avviate e arrestate.
- **Quota**: la quota controlla il numero di ore in cui gli studenti avranno accesso a una macchina virtuale al di fuori delle ore pianificate. Se la quota viene raggiunta durante l'uso da parte di uno studente, la macchina virtuale viene arrestata automaticamente. Lo studente potrà riavviare la macchina virtuale solo se la quota viene aumentata.
- **Arresto automatico**: quando è abilitata, questa impostazione determina l'arresto automatico delle macchine virtuali Windows dopo un certo periodo di tempo, dopo che uno studente si è disconnesso da una sessione Remote Desktop Protocol (RDP). Per impostazione predefinita, questa impostazione è disabilitata.  

    > [!NOTE]
    > Questa impostazione è attualmente disponibile solo per Windows.

### <a name="how-will-students-save-their-work"></a>In che modo gli studenti salveranno il proprio lavoro?

A ogni studente viene assegnata una macchina virtuale per la durata del lab. Lo studente può scegliere di:

- Salvare il proprio lavoro direttamente nella macchina virtuale.
- Salvarlo in un percorso esterno, ad esempio OneDrive o GitHub.

È possibile configurare OneDrive automaticamente per gli studenti nelle macchine virtuali del lab.

> [!NOTE]
> Per assicurarsi l'accesso continuo al lavoro salvato al di fuori del lab, anche dopo la fine del corso, è consigliabile che gli studenti salvino il proprio lavoro in un repository esterno.

### <a name="how-will-students-connect-to-their-vm"></a>In che modo gli studenti si connetteranno alla macchina virtuale?

Per RDP su macchine virtuali Windows, è consigliabile che gli studenti usino il [client Desktop remoto Microsoft](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Il client Desktop remoto supporta dispositivi Mac, Chromebook e Windows.

Per le macchine virtuali Linux, gli studenti possono usare SSH o RDP. Per consentire la connessione tramite RDP occorre installare e configurare i pacchetti RDP e GUI necessari.

## <a name="set-up-your-lab"></a>Configurare il lab

Dopo aver compreso i requisiti, si può procedere alla configurazione del lab della classe. Seguire i collegamenti disponibili in questa sezione per vedere come configurare il lab.

1. **Creare un lab.** Per le istruzioni, vedere l'esercitazione relativa alla [creazione di un lab per le classi](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab).

    > [!NOTE]
    > Se la classe richiede la virtualizzazione annidata, vedere la procedura descritta in [Abilitare la virtualizzazione annidata](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalizzare le immagini e pubblicare le macchine virtuali del lab.** Connettersi a una macchina virtuale speciale denominata macchina virtuale modello. Vedere i passaggi nelle guide seguenti:
    - [Creare e gestire una macchina virtuale modello](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Usare una raccolta di immagini condivise](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se si usa Windows, è consigliabile vedere anche le istruzioni relative alla [preparazione di una macchina virtuale modello Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Queste istruzioni includono i passaggi per la configurazione di OneDrive e Office per l'uso da parte degli studenti.

1. **Gestire il pool di macchine virtuali e la capacità.** È possibile aumentare o ridurre facilmente la capacità delle macchine virtuali, in base alle esigenze della classe. Tenere presente che l'aumento della capacità delle macchine virtuali può richiedere diverse ore, dal momento che comporta la configurazione di nuove macchine virtuali. Vedere i passaggi descritti nell'articolo relativo alla [configurazione e gestione di un pool di macchine virtuali](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Aggiungere e gestire gli utenti del lab.** Per aggiungere utenti al lab, vedere i passaggi descritti nelle esercitazioni seguenti:
   - [Aggiungere utenti al lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Inviare inviti agli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Per informazioni sui tipi di account che gli studenti possono usare, vedere [Account per studenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Impostare i controlli dei costi.** Per controllare i costi del lab, impostare pianificazioni, quote e arresto automatico. Vedere le esercitazioni seguenti:

   - [Impostare una pianificazione](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > A seconda del tipo di sistema operativo installato, l'avvio di una macchina virtuale può richiedere diversi minuti. Per assicurarsi che una macchina virtuale del lab sia pronta per l'uso durante le ore pianificate, è consigliabile avviare le macchine virtuali 30 minuti prima.

   - [Impostare quote per gli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e [Impostare una quota aggiuntiva per un utente specifico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Abilitare l'arresto automatico alla disconnessione](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Pianificazioni e quote non si applicano alla macchina virtuale modello, mentre si applicano le impostazioni di arresto automatico. 
        > 
        > Quando si crea un lab, la macchina virtuale modello viene creata, ma non avviata. È possibile avviarla, connettersi a essa, installare eventuale software prerequisito per il lab e quindi pubblicarla. Quando si pubblica la macchina virtuale modello, questa viene automaticamente arrestata, se non è stato fatto. 
        > 
        > Le macchine virtuali modello comportano **costi** durante l'esecuzione, quindi assicurarsi che la macchina virtuale modello venga arrestata quando non è necessario che sia in esecuzione. 


1. **Usare la dashboard.** Per istruzioni, vedere l'articolo relativo all'[uso della dashboard del lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Il costo stimato indicato nella dashboard è il costo massimo che è possibile prevedere per l'utilizzo del lab da parte degli studenti. Ad esempio, *non* verranno addebitate le ore quota non usate dagli studenti. I costi stimati *non* riflettono eventuali addebiti relativi all'uso della macchina virtuale modello o della raccolta di immagini condivise.

## <a name="next-steps"></a>Passaggi successivi

- [Tenere traccia dell'utilizzo di un lab per le classi](tutorial-track-usage.md)
  
- [Accedere a un lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)