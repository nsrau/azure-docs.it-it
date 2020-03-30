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
ms.openlocfilehash: d58e11551c2857c269e8985e81f84138f6d389ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370821"
---
# <a name="classroom-lab-setup-guide"></a>Guida alla configurazione del laboratorio di Classroom

Il processo di pubblicazione di un lab per gli studenti può richiedere diverse ore a seconda del numero di macchine virtuali (VM) che verranno create nel lab.  È consigliabile concedersi almeno un giorno per configurare un lab per assicurarsi che funzioni correttamente e per concedere tempo sufficiente per pubblicare le macchine virtuali degli studenti.

## <a name="understand-your-classs-lab-requirements"></a>Comprendere i requisiti di laboratorio della classe

Prima di configurare un nuovo lab, è necessario considerare le domande seguenti:Before you set up a new lab, you should consider the following questions:

**Quali requisiti software ha la classe?**

In base agli obiettivi di apprendimento della classe, è necessario decidere quale sistema operativo, applicazioni, strumenti e così via devono essere installati nelle macchine virtuali del lab.   Per configurare le macchine virtuali lab, sono disponibili tre opzioni:To set up lab VMs, you have three options:

- **Usare un'immagine** di Azure Marketplace: il Marketplace offre centinaia di immagini che è possibile usare durante la creazione di un lab.  Per alcune classi, un'immagine del marketplace potrebbe già contenere tutto ciò di cui hai bisogno per la tua classe.

- **Creare una nuova immagine personalizzata** - È possibile creare la propria immagine personalizzata utilizzando un'immagine del marketplace come punto di partenza e personalizzandola installando software aggiuntivo, apportando modifiche alla configurazione e così via.

- **Utilizzare un'immagine personalizzata esistente:** puoi riutilizzare le immagini personalizzate esistenti create in precedenza o create da altri amministratori o docenti della tua scuola. questo richiede che gli amministratori abbiano configurato una Galleria di immagini condivise che è un repository per il salvataggio di immagini personalizzate.

> [!NOTE]
> I tuoi amministratori sono responsabili dell'abilitazione di Marketplace e delle immagini personalizzate in modo da poterle utilizzare; è necessario coordinarsi con il reparto IT per assicurarsi che le immagini necessarie siano abilitate.  Le immagini personalizzate create vengono abilitate automaticamente per l'utilizzo all'interno di laboratori di cui si è proprietari.

**Quali requisiti hardware ha la classe?**

È possibile scegliere tra diverse dimensioni di calcolo, tra cui:There are a variety of compute sizes that you can choose from that includes:

- Dimensioni di virtualizzazione annidate in modo da poter concedere l'accesso agli studenti a una macchina virtuale in grado di ospitare più macchine virtuali annidate. ad esempio, questa dimensione di calcolo viene spesso utilizzata per i corsi di rete.

- GPU in modo che gli studenti possano utilizzare tipi di applicazioni ad alta intensità di computer, ad esempio per l'intelligenza artificiale e l'apprendimento automatico.

Fare riferimento alla guida sul [ridimensionamento della macchina virtuale](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) per visualizzare l'elenco completo delle dimensioni di calcolo disponibili.

> [!NOTE]
> A seconda dell'area in cui verrà creato il lab, è possibile che siano disponibili meno dimensioni di calcolo, poiché questa area varia in base all'area.  Si consiglia di selezionare la dimensione di calcolo più piccola più vicina alle proprie esigenze.  Con Azure Lab Services è possibile configurare un nuovo lab con una capacità di calcolo diversa in un secondo momento, se necessario.

**Quali dipendenze ha la classe in Azure esterno o risorse di rete?**

Se le macchine virtuali lab devono usare risorse esterne, ad esempio un database, una condivisione file, un server licenze e così via, sarà necessario coordinarsi con gli amministratori per assicurarsi che il lab abbia accesso a queste risorse.

Per accedere a risorse di Azure *non* protette da una rete virtuale, è possibile accedere a queste risorse tramite Internet pubblico senza alcuna configurazione aggiuntiva da parte degli amministratori.

> [!NOTE]
> È necessario considerare se è possibile ridurre le dipendenze del lab alle risorse esterne fornendo la risorsa direttamente nella macchina virtuale.  Ad esempio, per eliminare la necessità di leggere i dati da un database esterno, è possibile installare il database direttamente nella macchina virtuale.  

**Come saranno controllati i costi?**

Lab Services usa un modello di determinazione dei prezzi con pagamento in base al consumo, il che significa che si paga solo per il tempo di esecuzione di una macchina virtuale lab.  Per controllare i costi, sono disponibili tre opzioni che vengono in genere utilizzate in combinazione tra loro:

- **Pianificazione:** una pianificazione consente di controllare automaticamente quando le macchine virtuali dei lab vengono avviate e arrestate.
- **Quota:** controlla il numero di ore in cui gli studenti avranno accesso a una macchina virtuale al di fuori delle ore pianificate.  Se la quota viene raggiunta mentre uno studente la sta usando, la macchina virtuale viene arrestata automaticamente e lo studente non è in grado di riavviare la macchina virtuale a meno che la quota non venga aumentata.
- **Arresto automatico:** se abilitata, l'impostazione di arresto automatico determina l'arresto automatico delle macchine virtuali Windows dopo un determinato periodo di tempo dopo un determinato periodo di tempo dopo che uno studente si è disconnesso dalla sessione RDP.  Per impostazione predefinita, questa impostazione è disabilitata.  

    > [!NOTE]
    > Questa impostazione attualmente esiste solo per Windows.

**In che modo gli studenti salveranno il loro lavoro?**

Agli studenti viene assegnata la propria macchina virtuale assegnata per la durata del lab.  Possono scegliere di:

- Salvare direttamente nella macchina virtuale.
- Salvare in un repository esterno, ad esempio OneDrive, GitHub e così via.

Per usare OneDrive, è possibile scegliere di configurarlo automaticamente per gli studenti nelle macchine virtuali del lab.  Ulteriori informazioni a questo scopo sono fornite di seguito.

> [!NOTE]
> Per garantire che i tuoi studenti abbiano accesso continuo al lavoro salvato al di fuori del laboratorio, incluso al termine della lezione, consigliamo agli studenti di salvare il loro lavoro in un repository esterno.

**In che modo gli studenti si connetteranno alla macchina virtuale?**

Per le macchine virtuali RDP e Windows, si consiglia agli studenti di utilizzare [il client Microsoft Remote Desktop.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)  Il client Desktop remoto supporta Mac, Chromebook e Windows.

Per le macchine virtuali Linux, gli studenti possono usare SSH o RDP.   Per connettersi tramite RDP, è necessario installare e configurare i pacchetti RDP e GUI necessari.  Maggiori dettagli su questo è fornito di seguito.

## <a name="set-up-your-lab"></a>Configurare un ambiente lab

Una volta soddisfatti i requisiti per il laboratorio della classe, si è pronti per configurarlo.  Seguire i collegamenti in questa sezione per informazioni su come configurare il lab.

1. **Creare un lab**

   Per istruzioni, fare riferimento all'esercitazione sulla [creazione di un laboratorio](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) in classe.

    > [!NOTE]
    > Se la classe richiede la virtualizzazione annidata, fare riferimento ai passaggi della guida alle procedure che illustra [l'abilitazione della virtualizzazione annidata](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Personalizzare le immagini e pubblicare le macchine virtuali del labCustomize images and publish lab VMs**

    Per personalizzare le immagini e pubblicare macchine virtuali nel lab, ci si connette a una macchina virtuale speciale denominata MACCHINA virtuale modello. fare riferimento alle istruzioni per le seguenti guide:
    - [Creare e gestire una macchina virtuale modelloCreate and manage a template VM](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Utilizzo di una raccolta immagini condivisa](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Se si usa Windows, è inoltre necessario fare riferimento alle istruzioni della guida how-to per la preparazione di [una macchina virtuale modello](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)di Windows .  Queste istruzioni includono la procedura per configurare OneDrive e Office per gli studenti da usare.

1. **Gestire il pool e la capacità delle macchine virtualiManage VM pool and capacity**

   È possibile aumentare o ridurre facilmente la capacità della macchina virtuale in base alle esigenze della classe.  Tenere presente che l'aumento della capacità della macchina virtuale può richiedere diverse ore poiché ciò comporta la configurazione di nuove macchine virtuali.  Fare riferimento ai passaggi della guida alle procedure per [la configurazione e la gestione di un pool di macchine virtuali.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)

1. **Aggiungere e gestire gli utenti del lab**

   Per aggiungere utenti al lab, fare riferimento ai passaggi nelle esercitazioni seguenti:To add users to your lab, refer to steps in the following tutorials:
   - [Aggiungere utenti al lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Inviare inviti agli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Inoltre, fare riferimento al seguente articolo per informazioni sui tipi di account che gli studenti possono utilizzare:
    - [Account degli studenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Impostare i controlli dei costi**

    Per controllare i costi del lab, impostare pianificazioni, quote e arresto automatico; fare riferimento alle istruzioni nelle seguenti esercitazioni:

   - [Impostare una pianificazione](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > A seconda del tipo di sistema operativo installato, l'avvio di una macchina virtuale potrebbe richiedere alcuni minuti.  Per assicurarsi che una macchina virtuale del lab sia pronta per l'uso durante le ore pianificate, è consigliabile avviare le macchine virtuali con 30 minuti di anticipo per assicurarsi che le macchine virtuali siano in esecuzione e pronte per l'uso.

   - [Impostare quote per gli utenti](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) e una quota [aggiuntiva per un utente specifico](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quota-for-a-specific-user)
  
   - [Abilitare l'arresto automatico alla disconnessione](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Le pianificazioni, le quote e l'arresto automatico *non* si applicano alla macchina virtuale del modello.  Di conseguenza, è necessario assicurarsi di arrestare la macchina virtuale del modello quando non viene usata altrimenti continuerà a sostenere i costi.  Inoltre, per impostazione predefinita, quando si crea un lab, la macchina virtuale del modello viene avviata automaticamente, pertanto è consigliabile assicurarsi di completare immediatamente la configurazione del lab e arrestare la macchina virtuale del modello.

1. **Usare il dashboard**

    Per istruzioni, fare riferimento alla guida alle procedure [sull'utilizzo del dashboard del lab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)

    > [!NOTE]
    > I costi stimati visualizzati nel dashboard sono il costo massimo che è possibile prevedere per l'utilizzo del lab da parte degli studenti.  Ad esempio, *i* tuoi studenti non ti verranno addebitate le ore di quota inutilizzate.  I costi stimati *non* riflettono eventuali costi per l'utilizzo della macchina virtuale del modello o della raccolta immagini condivise.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Tenere traccia dell'utilizzo di un lab per le classi](tutorial-track-usage.md)
  
- [Accedere a un lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)
