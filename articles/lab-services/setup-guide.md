---
title: Guida alla configurazione del Lab accelerato per Azure Lab Services
description: Questa guida consente agli autori di lab di configurare rapidamente un account apposito da usare all'interno della scuola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f7423a76fd3ceb238c8c5c1a4ea794ff83b28b4a
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491665"
---
# <a name="lab-setup-guide"></a>Guida all'installazione di Lab

Il processo per la pubblicazione di un Lab per gli studenti può richiedere fino a diverse ore.  La quantità di tempo dipende dal numero di macchine virtuali (VM) che verranno create nel Lab. Consentire almeno un giorno per configurare un Lab, per assicurarsi che funzioni correttamente e per concedere tempo sufficiente per pubblicare le macchine virtuali degli studenti.

## <a name="understand-the-lab-requirements-of-your-class"></a>Comprendere i requisiti del lab della classe

Prima di configurare un nuovo lab è consigliabile considerare le domande seguenti.

### <a name="what-software-requirements-does-the-class-have"></a>Quali sono i requisiti software della classe?

In base agli obiettivi di apprendimento della classe stabilire il sistema operativo, le applicazioni e gli strumenti da installare nelle macchine virtuali del lab. Per configurare le macchine virtuali del lab sono disponibili tre opzioni:

- **Usare un'immagine di Azure Marketplace** : Azure Marketplace offre centinaia di immagini che è possibile usare durante la creazione di un lab. Per alcune classi, una di queste immagini potrebbe già contenere tutto il necessario per la classe.

- **Creare una nuova immagine personalizzata** : è possibile creare un'immagine personalizzata usando un'immagine di Azure Marketplace come punto di partenza e personalizzandola tramite l'installazione di software aggiuntivo e modifiche di configurazione.

- **Usare un'immagine personalizzata esistente** : è possibile riusare le immagini personalizzate esistenti create in precedenza oppure create da altri amministratori o docenti della scuola. Per usare immagini personalizzate, è necessario che gli amministratori configurano una raccolta di immagini condivise.  Una raccolta di immagini condivise è un repository usato per salvare immagini personalizzate.

> [!NOTE]
> Gli amministratori sono responsabili dell'abilitazione delle immagini di Azure Marketplace e di quelle personalizzate in modo che possano essere usate. Coordinarsi con il reparto IT per verificare che le immagini necessarie siano abilitate. Le immagini personalizzate create vengono automaticamente abilitate per l'uso nei lab di cui si è proprietari.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quali sono i requisiti hardware della classe?

È possibile scegliere tra diverse dimensioni di calcolo:

- Dimensioni di virtualizzazione annidate, in modo che sia possibile concedere l'accesso agli studenti a una macchina virtuale in grado di ospitare più macchine virtuali nidificate. Ad esempio, è possibile usare questa dimensione di calcolo per le classi di rete o di hacking etico.

- Dimensioni GPU, in modo che gli studenti possano usare tipi di applicazioni a elevato utilizzo di calcolo. Questa scelta, ad esempio, viene spesso usata con l'intelligenza artificiale e l'apprendimento automatico.

Per istruzioni sulla selezione delle dimensioni appropriate per la macchina virtuale, vedere gli articoli seguenti:
- [Dimensioni di VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)
- [Passaggio da un Lab fisico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> La disponibilità delle dimensioni di calcolo varia in base all'area geografica. Pertanto è possibile che se ne visualizzi un numero minore, a seconda dell'area geografica del lab. In genere, è consigliabile selezionare le dimensioni di calcolo minime più vicine alle proprie esigenze. Con Azure Lab Services è possibile configurare un nuovo lab con una capacità di calcolo diversa in un secondo momento, se necessario.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quali sono le dipendenze della classe da risorse di Azure o di rete esterne?
È possibile che le macchine virtuali del Lab debbano accedere a risorse esterne, ad esempio accedere a un database, a una condivisione file o a un server licenze.  Per consentire alle macchine virtuali del Lab di usare risorse esterne, coordinarsi con gli amministratori IT.

> [!NOTE]
> È consigliabile valutare se sia possibile ridurre le dipendenze del lab da risorse esterne rendendo disponibile la risorsa direttamente nella macchina virtuale. Ad esempio, per eliminare la necessità di leggere i dati da un database esterno, è possibile installare il database direttamente nella macchina virtuale.  

### <a name="how-will-costs-be-controlled"></a>Come saranno controllati i costi?
Lab Services usa un modello di prezzi con pagamento in base al consumo dove il cliente paga solo per il tempo in cui una macchina virtuale del lab è in esecuzione. Per controllare i costi, sono disponibili tre opzioni che vengono in genere utilizzate insieme:

- **Pianificazione** : una pianificazione consente di controllare automaticamente quando le macchine virtuali dei lab vengono avviate e arrestate.
- **Quota** : la quota controlla il numero di ore in cui gli studenti avranno accesso a una macchina virtuale al di fuori delle ore pianificate.  Quando uno studente usa la macchina virtuale e viene raggiunta la quota, la macchina virtuale viene arrestata automaticamente.  Lo studente potrà riavviare la macchina virtuale solo se la quota viene aumentata.
- **AutoShutdown** : se abilitata, l'impostazione AutoShutdown causa l'arresto automatico delle macchine virtuali Windows dopo che uno studente si è disconnesso da una sessione di Remote Desktop Protocol (RDP). Per impostazione predefinita, questa impostazione è disabilitata.

Per ulteriori informazioni, leggere gli articoli seguenti:
- [Stimare i costi](https://docs.microsoft.com/azure/lab-services/cost-management-guide#estimate-the-lab-costs)
- [Gestire i costi](https://docs.microsoft.com/azure/lab-services/cost-management-guide#manage-costs)

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

### <a name="will-students-also-be-using-microsoft-teams"></a>Gli studenti useranno anche Microsoft Teams?
Azure Lab Services si integra con Microsoft teams in modo che i docenti possano creare e gestire i propri Lab all'interno dei team.  Allo stesso modo, gli studenti possono accedere al Lab all'interno dei team.

Per altre informazioni, vedere l'articolo seguente:
- [Azure Lab Services in Microsoft Teams](https://docs.microsoft.com/azure/lab-services/lab-services-within-teams-overview)

## <a name="set-up-your-lab"></a>Configurare il lab

Dopo aver compreso i requisiti, si può procedere alla configurazione del lab della classe. Seguire i collegamenti disponibili in questa sezione per vedere come configurare il lab.  Si noti che sono disponibili diversi passaggi a seconda dell'uso di Lab in team.

1. **Creare un lab.** Vedere le esercitazioni sulla creazione di un Lab:
    - Per istruzioni, [creare un Lab in classe](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) .
    - [Creare un lab da Teams](https://docs.microsoft.com/azure/lab-services/how-to-get-started-create-lab-within-teams)

    > [!NOTE]
    > Se la classe richiede la virtualizzazione annidata, vedere la procedura descritta in [Abilitare la virtualizzazione annidata](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalizzare le immagini e pubblicare le macchine virtuali del lab.** Connettersi a una macchina virtuale speciale denominata macchina virtuale modello. Vedere i passaggi nelle guide seguenti:
    - [Creare e gestire una macchina virtuale modello](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Usare una raccolta di immagini condivise](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se si usa Windows, è consigliabile vedere anche le istruzioni relative alla [preparazione di una macchina virtuale modello Windows](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). Queste istruzioni includono i passaggi per la configurazione di OneDrive e Office per l'uso da parte degli studenti.

1. **Gestire il pool di macchine virtuali e la capacità.** È possibile aumentare o ridurre facilmente la capacità delle macchine virtuali, in base alle esigenze della classe. Tenere presente che l'aumento della capacità della macchina virtuale può richiedere diverse ore perché sono in corso la configurazione di nuove macchine virtuali. Vedere i passaggi negli articoli seguenti:
    - [Configurare e gestire un pool di macchine virtuali](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)
    - [Gestire un pool di macchine virtuali in Lab Services dai team](https://docs.microsoft.com/azure/lab-services/how-to-manage-vm-pool-within-teams)

1. **Aggiungere e gestire gli utenti del lab.** Per aggiungere utenti al lab, vedere i passaggi descritti nelle esercitazioni seguenti:
   - [Aggiungere utenti al lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Inviare inviti agli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)
   - [Gestire gli elenchi utenti di Lab Services dai team](https://docs.microsoft.com/azure/lab-services/how-to-manage-user-lists-within-teams)

    Per informazioni sui tipi di account che gli studenti possono usare, vedere [Account per studenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Impostare i controlli dei costi.** Per controllare i costi del Lab, impostare le pianificazioni, le quote e l'arresto automatico. Vedere le esercitazioni seguenti:

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

    - [Creare e gestire pianificazioni di servizi Lab nei team](https://docs.microsoft.com/azure/lab-services/how-to-create-schedules-within-teams) 

1. **Usare la dashboard.** Per istruzioni, vedere l'articolo relativo all'[uso della dashboard del lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Il costo stimato indicato nella dashboard è il costo massimo che è possibile prevedere per l'utilizzo del lab da parte degli studenti. Ad esempio, *non* verranno addebitate le ore quota non usate dagli studenti. I costi stimati *non* riflettono gli addebiti per l'uso della VM modello, della raccolta di immagini condivise o quando l'autore del Lab avvia un computer utente.

## <a name="next-steps"></a>Passaggi successivi

- [Tenere traccia dell'utilizzo di un lab per le classi](tutorial-track-usage.md)
  
- [Accedere a un lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)
