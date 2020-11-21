---
title: Guida alla configurazione del Lab accelerato per Azure Lab Services
description: Se sei un creatore di Lab, questa guida ti aiuta a configurare rapidamente un account Lab presso la tua scuola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021731"
---
# <a name="lab-setup-guide"></a>Guida all'installazione di Lab

In questa guida si apprenderà come creare un Lab per studenti presso la propria scuola.

Il processo per la pubblicazione di un Lab per gli studenti può richiedere fino a diverse ore. La quantità di tempo di installazione dipende dal numero di macchine virtuali (VM) che si vuole creare nel Lab. Consentire almeno un giorno per garantire che il Lab funzioni correttamente e consentire il tempo sufficiente per pubblicare le macchine virtuali degli studenti.

## <a name="understand-the-lab-requirements-of-your-class"></a>Comprendere i requisiti del lab della classe

Prima di configurare un nuovo lab è consigliabile considerare le domande seguenti.

### <a name="what-software-requirements-does-the-class-have"></a>Quali sono i requisiti software della classe?

Vedere gli obiettivi di apprendimento della classe quando si decide quale sistema operativo, applicazioni e strumenti è necessario installare nelle macchine virtuali del Lab. Per configurare le macchine virtuali del lab sono disponibili tre opzioni:

- **Usare un'immagine di Azure Marketplace**: Azure Marketplace offre centinaia di immagini che è possibile usare durante la creazione di un lab. Per alcune classi, una di queste immagini potrebbe già contenere tutto il necessario per la classe.

- **Creare una nuova immagine personalizzata**: è possibile creare un'immagine personalizzata usando un'immagine di Azure Marketplace come punto di partenza. È quindi possibile personalizzarlo installando software aggiuntivo e apportando modifiche di configurazione.

- **Usare un'immagine personalizzata esistente**: è possibile riutilizzare immagini personalizzate create in precedenza o immagini create da altri amministratori o docenti presso la propria scuola. Per usare immagini personalizzate, è necessario che gli amministratori configurano una raccolta di immagini condivise.  Una raccolta di immagini condivise è un repository usato per salvare immagini personalizzate.

> [!NOTE]
> Gli amministratori sono responsabili dell'abilitazione delle immagini di Azure Marketplace e di quelle personalizzate in modo che possano essere usate. Coordinarsi con il reparto IT per assicurarsi che le immagini necessarie siano abilitate. Le immagini personalizzate create vengono automaticamente abilitate per l'uso nei lab di cui si è proprietari.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quali sono i requisiti hardware della classe?

È possibile scegliere tra diverse dimensioni di calcolo:

- **Dimensioni della virtualizzazione annidata**: consente agli studenti di accedere a una macchina virtuale in grado di ospitare più macchine virtuali nidificate. Ad esempio, è possibile usare questa dimensione di calcolo per le classi di rete o di hacking etico.

- **Dimensioni GPU**: consente agli studenti di usare tipi di applicazioni a elevato utilizzo di computer. Questa scelta, ad esempio, viene spesso usata con l'intelligenza artificiale e l'apprendimento automatico.

Per istruzioni sulla selezione delle dimensioni appropriate per la macchina virtuale, vedere:
- [Dimensioni di VM](./administrator-guide.md#vm-sizing)
- [Passa da un Lab fisico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Poiché la disponibilità delle dimensioni di calcolo varia in base all'area, potrebbe essere disponibile un minor numero di dimensioni per il Lab. In genere, è necessario selezionare la dimensione di calcolo più piccola adatta alle proprie esigenze. Con Azure Lab Services è possibile configurare un nuovo Lab con una capacità di calcolo maggiore in un secondo momento, se necessario.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quali sono le dipendenze della classe da risorse di Azure o di rete esterne?
È possibile che le macchine virtuali del Lab debbano accedere a risorse esterne, ad esempio un database, una condivisione file o un server licenze.  Per consentire alle macchine virtuali del Lab di usare risorse esterne, coordinarsi con gli amministratori IT.

> [!NOTE]
> È necessario valutare se è possibile ridurre la dipendenza del Lab dalle risorse esterne fornendo risorse di rete direttamente nella macchina virtuale. Ad esempio, per eliminare la necessità di leggere i dati da un database esterno, è possibile installare il database direttamente nella macchina virtuale.  

### <a name="how-will-you-control-costs"></a>Come si controllano i costi?
Lab Services usa un modello di determinazione prezzi con pagamento in base al consumo, ovvero si paga solo per il tempo di esecuzione di una macchina virtuale Lab. Per controllare i costi, usare una o tutte le opzioni seguenti:

- **Pianificazione**: usare le pianificazioni per controllare automaticamente quando le macchine virtuali del Lab vengono avviate e arrestate.
- **Quota**: usare le quote per controllare il numero di ore per cui gli studenti hanno accesso a una macchina virtuale al di fuori delle ore pianificate.  Quando uno studente usa una macchina virtuale e raggiunge una quota, la macchina virtuale viene arrestata automaticamente.  Lo studente non può riavviare la macchina virtuale, a meno che non si aumenti la quota.
- **Arresto automatico**: quando si Abilita l'impostazione di arresto automatico, le macchine virtuali Windows vengono arrestate automaticamente dopo che uno studente si è disconnesso da una sessione di Remote Desktop Protocol (RDP). Per impostazione predefinita, questa impostazione è disabilitata.

Per ulteriori informazioni sul controllo dei costi, vedere:
- [Stimare i costi](./cost-management-guide.md#estimate-the-lab-costs)
- [Gestire i costi](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>In che modo gli studenti salveranno il proprio lavoro?
A ogni singolo studente viene assegnata una macchina virtuale per la durata del Lab. Gli studenti possono salvare il proprio lavoro:

- Alla VM.
- In una posizione esterna, ad esempio OneDrive o GitHub. È possibile configurare OneDrive automaticamente per gli studenti nelle macchine virtuali del lab.

> [!NOTE]
> Per assicurarsi che gli studenti abbiano continuato ad accedere al proprio lavoro salvato al di fuori del Lab e al termine della classe, si consiglia di salvare il lavoro in un repository esterno.

### <a name="how-will-students-connect-to-their-vms"></a>In che modo gli studenti si connetteranno alle macchine virtuali?
Per le connessioni RDP alle macchine virtuali Windows, è consigliabile che gli studenti usino il [client desktop remoto Microsoft](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Il client Desktop remoto supporta i dispositivi Mac, Chromebook e Windows.

Per le macchine virtuali Linux, gli studenti possono usare il protocollo Secure Shell (SSH) o RDP. Per fare in modo che gli studenti si connettano tramite RDP, è necessario installare e configurare i pacchetti RDP e interfaccia utente grafica (GUI) necessari.

### <a name="will-students-also-use-microsoft-teams"></a>Gli studenti utilizzeranno anche Microsoft Teams?
Azure Lab Services si integra con Microsoft teams in modo che i membri dei docenti possano creare e gestire i propri laboratori in team.  Allo stesso modo, gli studenti possono accedere ai propri Lab in teams.

Per ulteriori informazioni, vedere [Azure Lab Services in Microsoft teams](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Configurare il lab

Dopo aver compreso i requisiti, si può procedere alla configurazione del lab della classe. Per informazioni su come, seguire i collegamenti in questa sezione. Vengono inoltre fornite istruzioni per la configurazione dei Lab in team.

1. **Creare un Lab**. Vedere le esercitazioni seguenti:
    - [Creare un lab per le classi](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Creare un Lab in teams](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Se la classe richiede la virtualizzazione nidificata, vedere [abilitare la virtualizzazione annidata](./how-to-enable-nested-virtualization-template-vm.md).

1. **Personalizzare le immagini e pubblicare macchine virtuali del Lab**. Per connettersi a una macchina virtuale speciale denominata VM modello, vedere:
    - [Creare e gestire una macchina virtuale modello](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Usare una raccolta di immagini condivise](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Se si usa Windows, vedere anche [configurare una VM modello Windows](./how-to-prepare-windows-template.md). Queste istruzioni includono i passaggi per la configurazione di OneDrive e Microsoft Office per gli studenti.

1. **Gestire il pool di macchine virtuali e la capacità**. È possibile aumentare o ridurre facilmente la capacità delle macchine virtuali, in base alle esigenze della classe. Tenere presente che l'aumento della capacità della macchina virtuale può richiedere diverse ore perché sono in corso la configurazione di nuove macchine virtuali. Vedere gli articoli seguenti:
    - [Configurare e gestire un pool di macchine virtuali](./how-to-set-virtual-machine-passwords.md)
    - [Gestire un pool di macchine virtuali in Lab Services nei team](./how-to-manage-vm-pool-within-teams.md)

1. **Aggiungere e gestire gli utenti del Lab**. Per aggiungere utenti al Lab, vedere:
   - [Aggiungere utenti al lab](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Inviare inviti agli utenti](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Gestire gli elenchi di utenti di Lab Services nei team](./how-to-manage-user-lists-within-teams.md)

    Per informazioni sui tipi di account che gli studenti possono usare, vedere [account per studenti](./how-to-configure-student-usage.md#student-accounts).
  
1. **Imposta i controlli dei costi**. Per impostare una pianificazione, stabilire le quote e abilitare l'arresto automatico, vedere le esercitazioni seguenti:

   - [Impostare una pianificazione](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > A seconda del sistema operativo installato, l'avvio di una macchina virtuale potrebbe richiedere diversi minuti. Per assicurarsi che una macchina virtuale Lab sia pronta per essere usata durante le ore pianificate, è consigliabile avviarla 30 minuti prima.

   - [Impostare le quote per gli utenti](./how-to-configure-student-usage.md#set-quotas-for-users) e [impostare quote aggiuntive per utenti specifici](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Abilitare l'arresto automatico alla disconnessione](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Le pianificazioni e le quote non si applicano alla macchina virtuale modello, ma le impostazioni di arresto automatico si applicano. 
        > 
        > Quando si crea un Lab, la macchina virtuale del modello viene creata ma non avviata. È possibile avviare la macchina virtuale del modello, connettersi, installare i prerequisiti software per il Lab e quindi pubblicarli. Quando si pubblica la macchina virtuale del modello, questa viene arrestata automaticamente se non è stata eseguita manualmente. 
        > 
        > Le VM modello comportano *costi* quando sono in esecuzione, quindi assicurarsi che la macchina virtuale del modello venga arrestata quando non è necessario che sia in esecuzione.

    - [Creare e gestire pianificazioni di servizi Lab in team](./how-to-create-schedules-within-teams.md) 

1. **Usare il dashboard**. Per istruzioni, vedere [use the aule Lab dashboard](./use-dashboard.md).

    > [!NOTE]
    > Il costo stimato visualizzato nel dashboard è il costo massimo che si può prevedere per l'utilizzo del Lab per studenti. Ad esempio, *non* verranno addebitate le ore quota non usate dagli studenti. I costi stimati *non* riflettono gli addebiti per l'uso della VM modello, della raccolta di immagini condivise o quando l'autore del Lab avvia un computer utente.

## <a name="next-steps"></a>Passaggi successivi

Come parte della gestione dei laboratori, vedere gli articoli seguenti:
- [Tenere traccia dell'utilizzo del Lab in aula](tutorial-track-usage.md)  
- [Accedere a un lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)
