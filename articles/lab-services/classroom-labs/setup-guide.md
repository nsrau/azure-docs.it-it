---
title: Guida alla configurazione accelerata del laboratorio di classe per Azure Lab ServicesAccelerated classroom lab setup guide for Azure Lab Services
description: Questa guida consente ai creatori di laboratorio di configurare rapidamente un account lab per l'utilizzo all'interno della propria scuola.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878103"
---
# <a name="classroom-lab-setup-guide"></a>Guida alla configurazione del laboratorio di Classroom

Il processo di pubblicazione di un lab per gli studenti può richiedere diverse ore, a seconda del numero di macchine virtuali (VM) che verranno create nel lab. Concedere almeno un giorno per configurare un lab, per assicurarsi che funzioni correttamente e per concedere tempo sufficiente per pubblicare le macchine virtuali degli studenti.

## <a name="understand-the-lab-requirements-of-your-class"></a>Comprendere i requisiti di laboratorio della classe

Prima di configurare un nuovo lab, è necessario considerare le domande seguenti.

### <a name="what-software-requirements-does-the-class-have"></a>Quali requisiti software ha la classe?

In base agli obiettivi di apprendimento della classe, decidere quale sistema operativo, applicazioni e strumenti devono essere installati nelle macchine virtuali del lab. Per configurare le macchine virtuali lab, sono disponibili tre opzioni:To set up lab VMs, you have three options:

- **Usare un'immagine**di Azure Marketplace: Azure Marketplace offre centinaia di immagini che è possibile usare durante la creazione di un lab. Per alcune classi, una di queste immagini potrebbe già contenere tutto ciò che serve per la classe.

- **Creare una nuova immagine personalizzata:** è possibile creare un'immagine personalizzata usando un'immagine di Azure Marketplace come punto di partenza e personalizzandola installando software aggiuntivo e apportando modifiche alla configurazione.

- **Usa un'immagine personalizzata esistente:** puoi riutilizzare le immagini personalizzate esistenti create in precedenza o create da altri amministratori o docenti della tua scuola. Ciò richiede che gli amministratori abbiano configurato una raccolta di immagini condivise, ovvero un archivio per il salvataggio di immagini personalizzate.

> [!NOTE]
> Gli amministratori sono responsabili dell'abilitazione delle immagini di Azure Marketplace e delle immagini personalizzate in modo da poterle usare. Coordinarsi con il reparto IT per assicurarsi che le immagini necessarie siano abilitate. Le immagini personalizzate create vengono abilitate automaticamente per l'utilizzo all'interno di laboratori di cui si è proprietari.

### <a name="what-hardware-requirements-does-the-class-have"></a>Quali requisiti hardware ha la classe?

È possibile scegliere tra diverse dimensioni di calcolo:

- Dimensioni di virtualizzazione annidate, in modo che sia possibile concedere l'accesso agli studenti a una macchina virtuale in grado di ospitare più macchine virtuali annidate. Ad esempio, è possibile utilizzare questa dimensione di calcolo per i corsi di rete.

- GPU, in modo che gli studenti possano utilizzare tipi di applicazioni ad alta intensità di computer. Ad esempio, questa scelta può essere appropriata per l'intelligenza artificiale e l'apprendimento automatico.

Fare riferimento alla guida sul [ridimensionamento della macchina virtuale](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) per visualizzare l'elenco completo delle dimensioni di calcolo disponibili.

> [!NOTE]
> A seconda dell'area del lab, è possibile che le dimensioni di calcolo disponibili vengano visualizzate meno, poiché questa operazione varia in base all'area. In genere, è consigliabile selezionare la dimensione di calcolo più piccola più vicina alle proprie esigenze. Con Azure Lab Services è possibile configurare un nuovo lab con una capacità di calcolo diversa in un secondo momento, se necessario.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Quali dipendenze ha la classe in Azure esterno o risorse di rete?

Se le macchine virtuali lab devono usare risorse esterne, ad esempio un database, una condivisione file o un server licenze, coordinarsi con gli amministratori per assicurarsi che il lab abbia accesso a queste risorse.

Per l'accesso alle risorse di Azure *non* protette da una rete virtuale, non è necessario richiedere una configurazione aggiuntiva da parte degli amministratori. È possibile accedere a queste risorse tramite Internet pubblico.

> [!NOTE]
> È necessario considerare se è possibile ridurre le dipendenze del lab alle risorse esterne fornendo la risorsa direttamente nella macchina virtuale. Ad esempio, per eliminare la necessità di leggere i dati da un database esterno, è possibile installare il database direttamente nella macchina virtuale.  

### <a name="how-will-costs-be-controlled"></a>Come saranno controllati i costi?

Lab Services usa un modello di determinazione dei prezzi con pagamento in base al consumo, il che significa che si paga solo per il tempo di esecuzione di una macchina virtuale lab. Per controllare i costi, sono disponibili tre opzioni che vengono in genere utilizzate in combinazione tra loro:

- **Pianificazione:** una pianificazione consente di controllare automaticamente quando le macchine virtuali dei lab vengono avviate e arrestate.
- **Quota:** la quota controlla il numero di ore in cui gli studenti avranno accesso a una macchina virtuale al di fuori delle ore pianificate. Se la quota viene raggiunta mentre uno studente la sta usando, la macchina virtuale viene arrestata automaticamente. Lo studente non è in grado di riavviare la macchina virtuale a meno che la quota non venga aumentata.
- **Arresto automatico**: Se abilitata, l'impostazione di arresto automatico determina l'arresto automatico delle macchine virtuali Windows dopo un determinato periodo di tempo, dopo che uno studente si è disconnesso da una sessione RDP (Remote Desktop Protocol). Per impostazione predefinita, questa impostazione è disabilitata.  

    > [!NOTE]
    > Questa impostazione attualmente esiste solo per Windows.

### <a name="how-will-students-save-their-work"></a>In che modo gli studenti salveranno il loro lavoro?

Agli studenti viene assegnata la propria macchina virtuale, assegnata loro per la durata del lab. Possono scegliere di:

- Salvare direttamente nella macchina virtuale.
- Salvare in un percorso esterno, ad esempio OneDrive o GitHub.Save to an external location, such as OneDrive or GitHub.

È possibile configurare OneDrive automaticamente per gli studenti nelle macchine virtuali del lab.

> [!NOTE]
> Per garantire che i tuoi studenti abbiano accesso continuo al lavoro salvato al di fuori del laboratorio e, al termine della lezione, consigliamo agli studenti di salvare il lavoro in un repository esterno.

### <a name="how-will-students-connect-to-their-vm"></a>In che modo gli studenti si connetteranno alla macchina virtuale?

Per le macchine virtuali RDP e Windows, è consigliabile che gli studenti utilizzino il [client Microsoft Remote Desktop.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) Il client Desktop remoto supporta Mac, Chromebook e Windows.

Per le macchine virtuali Linux, gli studenti possono usare SSH o RDP. Per fare in modo che gli studenti si connettano tramite RDP, è necessario installare e configurare i pacchetti RDP e GUI necessari.

## <a name="set-up-your-lab"></a>Configurare un ambiente lab

Dopo aver compreso i requisiti per il laboratorio della classe, è possibile configurarlo. Seguire i collegamenti in questa sezione per informazioni su come configurare il lab.

1. **Creare un lab.** Per istruzioni, fare riferimento all'esercitazione sulla [creazione di un laboratorio](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) in classe.

    > [!NOTE]
    > Se la classe richiede la virtualizzazione annidata, vedere i passaggi descritti nell'abilitazione della [virtualizzazione annidata.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)

1. **Personalizzare le immagini e pubblicare le macchine virtuali del lab.** Connettersi a una macchina virtuale speciale denominata macchina virtuale modello. Vedere i passaggi nelle guide seguenti:
    - [Creare e gestire una macchina virtuale modelloCreate and manage a template VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Usare raccolta immagini condivisa](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se si usa Windows, dovresti vedere anche le istruzioni per preparare una macchina virtuale modello di [Windows.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template) Queste istruzioni includono la procedura per configurare OneDrive e Office per gli studenti da usare.

1. **Gestire il pool e la capacità della macchina virtuale.** È possibile aumentare o ridurre facilmente la capacità della macchina virtuale, in base alle esigenze della classe. Tenere presente che l'aumento della capacità della macchina virtuale potrebbe richiedere diverse ore, perché ciò comporta la configurazione di nuove macchine virtuali. Vedere i passaggi per [la configurazione e la gestione di un pool di macchine virtuali](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Aggiungere e gestire gli utenti del lab.** Per aggiungere utenti al lab, fare riferimento ai passaggi nelle esercitazioni seguenti:To add users to your lab, refer to steps in the following tutorials:
   - [Aggiungere utenti al lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Inviare inviti agli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Per informazioni sui tipi di account che gli studenti possono utilizzare, vedere [Account studente](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Impostare i controlli dei costi.** Per controllare i costi del lab, impostare pianificazioni, quote e arresto automatico. Vedere le esercitazioni seguenti:

   - [Impostare una pianificazione](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > A seconda del tipo di sistema operativo installato, l'avvio di una macchina virtuale potrebbe richiedere alcuni minuti. Per assicurarsi che una macchina virtuale del lab sia pronta per l'uso durante le ore pianificate, è consigliabile avviare le macchine virtuali con 30 minuti di anticipo.

   - [Impostare quote per gli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e una quota [aggiuntiva per un utente specifico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Abilitare l'arresto automatico alla disconnessione](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Le pianificazioni, le quote e l'arresto automatico non si applicano alla macchina virtuale del modello. Di conseguenza, è necessario assicurarsi di arrestare la macchina virtuale del modello quando non viene usata. In caso contrario, continua a sostenere i costi. Inoltre, per impostazione predefinita, quando si crea un lab, la macchina virtuale del modello viene avviata automaticamente. Assicurarsi di completare immediatamente la configurazione del lab e arrestare la macchina virtuale del modello.

1. **Utilizzare il dashboard.** Per istruzioni, vedere [Utilizzo del dashboard del lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Il costo stimato indicato nel dashboard è il costo massimo che è possibile prevedere per l'utilizzo del lab da parte degli studenti. Ad esempio, *i* tuoi studenti non ti verranno addebitate le ore di quota inutilizzate. I costi stimati *non* riflettono eventuali costi per l'utilizzo della macchina virtuale del modello o della raccolta di immagini condivise.

## <a name="next-steps"></a>Passaggi successivi

- [Tenere traccia dell'utilizzo di un lab per le classi](tutorial-track-usage.md)
  
- [Accedere a un lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)