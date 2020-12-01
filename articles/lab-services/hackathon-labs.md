---
title: Usare Azure Lab Services per hackathon
description: Questo articolo descrive come usare Azure Lab Services per la creazione di Lab che è possibile usare per l'esecuzione di gli hackathon.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 561351636a37c2cd5c3070dbfef2a3122e5c29b0
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434261"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Usare Azure Lab Services per il hackathon successivo
Azure Lab Services è progettato per essere leggero e facile da usare, in modo che sia possibile creare rapidamente un nuovo Lab di macchine virtuali (VM) per la hackathon.  Usare l'elenco di controllo seguente per assicurarsi che il hackathon venga eseguito nel modo più semplice possibile. Questo elenco di controllo deve essere completato dal reparto IT o dai docenti responsabili della creazione e della gestione di hackathon Lab. 

Per usare i servizi Lab per la hackathon, assicurarsi che sia l'account Lab che il Lab vengano creati almeno alcuni giorni prima dell'avvio del hackathon. Seguire anche le indicazioni seguenti:

## <a name="guidance"></a>Indicazioni

- **Creare il Lab in un'area o in una località più vicina ai partecipanti**. 

    Per ridurre la latenza, creare il Lab in un'area più vicina ai partecipanti a hackathon.  Se i partecipanti si trovano in tutto il mondo, è necessario usare il giudizio migliore per creare un Lab situato centralmente.  In alternativa, suddividere il hackathon per usare più Lab in base alle posizioni in cui si trovano i partecipanti.
- **Scegliere una dimensione di calcolo ideale per le esigenze di utilizzo**.

    Generalmente, maggiori sono le dimensioni di calcolo, più veloci saranno le prestazioni della macchina virtuale. Tuttavia, per limitare i costi, è necessario selezionare le dimensioni di calcolo appropriate in base alle esigenze dei partecipanti. Per informazioni dettagliate sulle dimensioni di calcolo disponibili, vedere [informazioni sul dimensionamento delle VM nella Guida dell'amministratore](administrator-guide.md#vm-sizing) .
- **Configurare RDP\SSH per la connessione Desktop remoto alle macchine virtuali Linux**.

    Se il hackathon usa VM Linux, assicurarsi che desktop remoto sia abilitato in modo che i partecipanti possano usare RDP (Remote Desktop Protocol) o SSH (Secure Shell) per connettersi alle macchine virtuali. Questo passaggio è necessario solo per le macchine virtuali Linux e deve essere abilitato quando si crea il Lab. Inoltre, per RDP, potrebbe essere necessario installare e configurare il server RDP e i pacchetti GUI nella macchina virtuale del modello prima della pubblicazione.  Per ulteriori informazioni, vedere la [Guida alle procedure per l'abilitazione di desktop remoto per Linux](how-to-enable-remote-desktop-linux.md).

- **Installare e arrestare gli aggiornamenti di Windows**. 

    Se si usa un'immagine di Windows, è consigliabile installare gli aggiornamenti di Windows più recenti nella [macchina virtuale modello](how-to-create-manage-template.md) del Lab prima di pubblicarla per creare macchine virtuali dei Lab. È a scopo di sicurezza e impedisce ai partecipanti di essere interrotti durante hackathon per installare gli aggiornamenti, che possono anche causare il riavvio delle macchine virtuali. È anche possibile disattivare gli aggiornamenti di Windows per evitare interruzioni future. Vedere la [Guida alle procedure per l'installazione e la configurazione degli aggiornamenti di Windows](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Decidere in che modo gli studenti eseguiranno il backup del proprio lavoro**. 

    Agli studenti viene assegnata una macchina virtuale per la durata del hackathon. È possibile salvare il proprio lavoro direttamente nel computer, ma è consigliabile che gli studenti riportino il proprio lavoro in modo che possano accedervi dopo il completamento del hackathon. Ad esempio, devono essere salvati in una posizione esterna, ad esempio OneDrive, GitHub e così via. Per usare OneDrive, è possibile scegliere di configurarlo automaticamente per gli studenti nelle macchine virtuali del Lab. Vedere la [Guida alle procedure per installare e configurare OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Impostare la capacità della macchina virtuale in base al numero di partecipanti**. 

    Verificare che la capacità della macchina virtuale del Lab sia impostata in base al numero di partecipanti previsti nella hackathon. Quando si pubblica la macchina virtuale modello, la creazione di tutti i computer nel lab può richiedere diverse ore. Questo è il motivo per cui si consiglia di eseguire questa operazione in anticipo fino all'inizio del hackathon. Per ulteriori informazioni, vedere la [Guida alle procedure per l'aggiornamento della capacità del Lab](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Decidere se limitare l'accesso al Lab**. 

    Quando si aggiungono utenti al Lab, è disponibile un'opzione limita accesso abilitata per impostazione predefinita. Questa funzionalità richiede l'aggiunta di tutti i messaggi di posta elettronica dei partecipanti di hackathon all'elenco prima che sia possibile registrarsi e accedere al Lab usando il collegamento di registrazione. Se si dispone di un hackathon in cui non si conoscono i partecipanti prima dell'evento, è possibile scegliere di disabilitare l'opzione limita accesso, che consente a chiunque di effettuare la registrazione al Lab usando il collegamento di registrazione. Per ulteriori informazioni, vedere la [Guida alle procedure](how-to-configure-student-usage.md#add-users-to-a-lab)per l'aggiunta di utenti.

- **Verificare le impostazioni di pianificazione, quota e arresto** automatico. 

    Lab Services offre diversi controlli dei costi per limitare l'utilizzo delle macchine virtuali. Tuttavia, se queste impostazioni non sono configurate correttamente, possono causare la chiusura imprevista delle macchine virtuali del Lab. Per assicurarsi che queste impostazioni siano configurate in modo appropriato per la hackathon, verificare le impostazioni seguenti:

    **Pianificazione**: una [pianificazione](how-to-create-schedules.md) consente di controllare automaticamente quando i computer dei Lab vengono avviati e arrestati. Per impostazione predefinita, non viene configurata alcuna pianificazione quando si crea un nuovo Lab. Tuttavia, è necessario assicurarsi che la pianificazione del Lab sia configurata in base a quanto è sensato per la hackathon.  Ad esempio, se il hackathon inizia il sabato alle 8:00 AM e termina la domenica alle 5:00 PM, è possibile creare una pianificazione che avvii automaticamente il computer alle 7:30 di sabato (circa 30 minuti prima dell'inizio del Hackathon) e lo arresterà alle 5:00 di domenica. In alternativa, è possibile decidere di non usare una pianificazione.

    **Quota**: la [quota](how-to-configure-student-usage.md#set-quotas-for-users) controlla il numero di ore per cui i partecipanti avranno accesso a una macchina virtuale al di fuori delle ore pianificate. Se la quota viene raggiunta mentre un partecipante lo usa, il computer viene automaticamente arrestato e il partecipante non sarà in grado di riavviarlo, a meno che la quota non venga aumentata. Per impostazione predefinita, quando si crea un Lab, la quota viene impostata su 10 ore. Anche in questo caso, è necessario assicurarsi di impostare la quota in modo da consentire un tempo sufficiente per la hackathon, che è particolarmente importante se non è stata creata una pianificazione.

    **AutoShutdown**: se abilitata, l'impostazione di [AutoShutdown](how-to-enable-shutdown-disconnect.md) causa l'arresto automatico delle macchine virtuali Windows dopo un determinato periodo di tempo quando uno studente si disconnette dalla sessione RDP. Per impostazione predefinita, questa impostazione è disabilitata.

- **Configurare le impostazioni del firewall per consentire le connessioni alle macchine virtuali del Lab**. 

    Assicurarsi che le impostazioni del firewall dell'organizzazione o della scuola consentano la connessione alle macchine virtuali del Lab usando RDP\SSH. Per ulteriori informazioni, vedere la [Guida alle procedure per la configurazione delle impostazioni del firewall della rete](how-to-configure-firewall-settings.md).

- **Installare il client di RDP\SSH su tablet, Mac, PC e così via dei partecipanti**.

    I partecipanti di hackathon devono avere un client RDP e/o SSH installato nei tablet o nei portatili che useranno per connettersi alle macchine virtuali del Lab. È possibile scegliere tra client RDP o SSH diversi, ad esempio:

    - App **Connessione desktop remoto** Microsoft per le connessioni RDP. L'app Connessione Desktop remoto è supportata su diversi tipi di piattaforme, inclusi i Chromebook e [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162).
    - [Putty](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) per l'uso di SSH per connettersi a una VM Linux.
- **Verificare le macchine virtuali del Lab**. 

    Dopo aver pubblicato le VM Lab, è necessario verificare che siano configurate correttamente. È sufficiente eseguire questa verifica per una delle macchine virtuali Lab del partecipante:

    1. Connettersi tramite RDP e\o SSH.
    2. Aprire tutte le applicazioni e gli strumenti aggiuntivi installati per personalizzare l'immagine di macchina virtuale di base.
    3. Esaminare alcuni scenari di base rappresentativi delle attività che i partecipanti faranno per garantire che le prestazioni della macchina virtuale siano adeguate in base alla dimensione di calcolo selezionata.

## <a name="on-the-day-of-hackathon"></a>Il giorno della hackathon
Questa sezione descrive i passaggi per completare il giorno della hackathon.

1. **Avviare le macchine virtuali del Lab**.

    A seconda del sistema operativo, l'avvio del computer lab potrebbe richiedere fino a 30 minuti. Di conseguenza, è importante avviare i computer prima che il hackathon venga avviato in modo che i partecipanti non debbano attendere. Se si usa una pianificazione, assicurarsi che le macchine virtuali vengano avviate automaticamente almeno 30 minuti prima.
2. **Invitare gli studenti a registrarsi e ad accedere alla macchina virtuale Lab**. 

    Fornire ai partecipanti le informazioni seguenti per consentire ai partecipanti di accedere alle macchine virtuali del Lab. 

    - Collegamento di registrazione del Lab. 
    - Credenziali da utilizzare per la connessione al computer. Questo passaggio si applica solo se il Lab usa un'immagine basata su Windows e sono state configurate tutte le macchine virtuali per usare la stessa password.
    - Istruzioni sul modo in cui i partecipanti e\o SSH RDP ai propri computer.

        Per altre informazioni, vedere [Guida alle procedure per l'invio di inviti agli utenti e la](how-to-configure-student-usage.md?branch=master#send-invitations-to-users) [connessione a VM Linux](how-to-use-remote-desktop-linux-student.md?branch=master). 

## <a name="next-steps"></a>Passaggi successivi
Iniziare con la creazione di un account Lab in Labs seguendo le istruzioni riportate nell'articolo: [esercitazione: configurare un account Lab con Azure Lab Services](tutorial-setup-lab-account.md).