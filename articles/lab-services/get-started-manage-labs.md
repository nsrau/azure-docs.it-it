---
title: Inizia a usare Azure Lab Services
description: Questo articolo descrive come iniziare a usare Azure Lab Services.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: 6fb0da7f91e0eecdeefecdb6635b657245fda9af
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434431"
---
# <a name="get-started-with-lab-services"></a>Introduzione a Lab Services 

Azure Lab Services offre agli studenti e ai docenti l'accesso ai laboratori di computer virtuali direttamente dai rispettivi computer.

Gli insegnanti devono conoscere come insegnare agli studenti e ai genitori l'uso di servizi Lab nella propria istruzione tramite l'hardware uno a uno studente emesso. Di conseguenza, gli studenti potranno accedere al software standard del settore necessario per i programmi di studio tramite macchine virtuali (VM). 

Una macchina virtuale è un ambiente virtuale che funge da computer virtuale. Le macchine virtuali dispongono di processore, memoria e archiviazione. Le macchine virtuali forniscono un sostituto per un computer reale e possono concedere agli utenti l'accesso a sistemi operativi e software senza la necessità di utilizzarli nel proprio dispositivo. Azure Lab Services offre agli studenti gli strumenti per accedere alle macchine virtuali e esplorarle e per gestire i propri laboratori di computer virtuali. 

Questo articolo fornisce informazioni per insegnare al personale su come accedere, gestire e insegnare a studenti/genitori di usare Azure Lab Services.

## <a name="key-concepts"></a>Concetti chiave

### <a name="quota-hours"></a>Ore quota

Gli studenti possono accedere alle proprie macchine virtuali in qualsiasi momento durante le classi pianificate senza alcun effetto sulle ore di quota. Le ore di quota sono impostate per l'intero semestre e determinano il numero di ore in cui uno studente può usare la propria VM al di fuori del tempo di classe pianificato regolarmente.

8 ore alla settimana, viene reimpostata la domenica non cumulativa.

Per altre informazioni, vedere [set quota](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Arresto automatico

Per ridurre i costi e salvare le ore di quota degli studenti, gli arresti automatici sono abilitati per i Lab. Arresti automatici disattiva le macchine virtuali dopo un periodo di inattività (nessun input del mouse o tastiera). Gli arresti automatici funzionano in due fasi, prima che uno studente venga disconnesso dalla macchina virtuale dopo un periodo di inattività. A questo punto, la macchina virtuale è ancora **in esecuzione** e gli studenti sono in grado di connettersi. Dopo un altro periodo di inattività dopo la disconnessione, la macchina virtuale si arresterà automaticamente.

Gli arresti automatici sono uno strumento importante per il risparmio di costi, ma presentano una sfida per gli studenti in merito al salvataggio del lavoro e al rendering di file di progetto di grandi dimensioni. Se gli studenti vengono spesso disconnessi o le VM si spengono troppo rapidamente, contattare l'amministratore della CTE. 

Per altre informazioni, vedere [configurare l'arresto automatico delle macchine virtuali per un account Lab](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Gestione delle macchine virtuali

La gestione del Lab consente agli insegnanti di controllare elementi come la capacità del Lab (il numero di macchine virtuali disponibili per gli studenti) e avviare, arrestare o reimpostare manualmente le macchine virtuali. gli insegnanti possono anche connettersi alle macchine virtuali per sperimentare l'interfaccia degli studenti, accedere ai file e risolvere i problemi relativi al software o alla macchina virtuale stessa.

L'aspetto più importante da ricordare quando si gestiscono le macchine virtuali è che ogni volta che un computer è **in esecuzione**, si verificano costi, anche se nessuno è connesso alla VM.

## <a name="lab-dashboards"></a>Dashboard del Lab

### <a name="overview"></a>Panoramica

I dashboard per i Lab in Azure Lab Services offrono uno snapshot dei diversi aspetti di un determinato Lab, incluse le informazioni sulle VM, il numero di macchine virtuali assegnate e non assegnate, il numero di utenti registrati e non registrati e le informazioni sulle pianificazioni dei Lab. 

> [!NOTE]
> Anche se la maggior parte degli aspetti amministrativi del dashboard e del [sito Web di Azure Lab Services](https://labs.azure.com/) sarà visibile agli insegnanti, le autorizzazioni specifiche per il ruolo potrebbero avere un effetto sulla possibilità di modificare determinati criteri nel dashboard. Se si verifica un problema con la configurazione del lab specifica, rivolgersi all'amministratore della CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="portale di Azure Lab Services":::

### <a name="examine-a-dashboard"></a>Esaminare un dashboard

1. Navigare e accedere al [sito web Azure Lab Services](https://labs.azure.com/).
1. Selezionare il Lab.
1. Sul lato sinistro della finestra viene visualizzato un **Dashboard** . Fare clic su **Dashboard** per visualizzare una serie di riquadri nel dashboard.
1. Sotto il riquadro **costi & la fatturazione** sono disponibili anche riquadri per modelli, pool di macchine virtuali, utenti e pianificazioni, che consentono di modificare gli aspetti e visualizzare altri dettagli sul Lab della classe.

    * Modello: descrive la data in cui è stato creato il modello e l'ultima pubblicazione. 
    * Pool di macchine virtuali: numero di macchine virtuali assegnate e non assegnate.
    * Utenti: numero di utenti registrati e utenti che sono stati aggiunti al Lab, ma non registrati.
    * Pianificazioni: Visualizza gli eventi pianificati imminenti per il Lab e un collegamento per visualizzare più eventi.

Per altre informazioni, vedere [use dashboard](use-dashboard.md).

### <a name="manually-starting-vms"></a>Avvio manuale di macchine virtuali

1. Dalla pagina del **pool di macchine virtuali** è possibile avviare tutte le macchine virtuali in un Lab facendo clic sul pulsante **Start All (Avvia tutto** ) nella parte superiore della pagina.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Avviare le macchine virtuali":::
1. È possibile avviare singole VM facendo clic sull'interruttore stato. 

    L'interruttore verrà letto **a partire** dall'avvio della macchina virtuale e quindi **in esecuzione** dopo l'avvio della macchina virtuale.
1. È anche possibile selezionare un numero di macchine virtuali usando i controlli a sinistra della colonna **nome** . 

    Dopo aver selezionato le macchine virtuali desiderate, fare clic sul pulsante **Avvia** nella parte superiore della schermata.
1. Una volta avviata, è possibile fare clic sul pulsante **Interrompi tutto** per arrestare tutte le macchine virtuali.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Arrestare le macchine virtuali":::

### <a name="stopping-and-resetting-vms"></a>Arresto e reimpostazione di macchine virtuali

* È possibile arrestare singole VM facendo clic sull'interruttore stato.
* È anche possibile arrestare più macchine virtuali usando i controlli e facendo clic sul pulsante "arresta" nella parte superiore della schermata.

Se uno studente sta riscontrando difficoltà di connessione alla macchina virtuale oppure la macchina virtuale deve essere reimpostata per qualsiasi altro motivo, è possibile usare la funzione Reset.
1. Per reimpostare una o più macchine virtuali, selezionarle usando i controlli, quindi fare clic sul pulsante **Reimposta** nella parte superiore della pagina.
1. Nella finestra popup fare clic su **Reimposta**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Ripristinare la macchina virtuale":::

    > [!NOTE]
    > L'attivazione di una macchina virtuale per studenti non influirà sulla quota per lo studente. Quote per gli utenti specifica il numero di ore di Lab disponibili per l'utente al di fuori dell'ora della classe pianificata.

### <a name="connect-to-vms"></a>Connettersi alle macchine virtuali

Gli insegnanti sono in grado di connettersi a una macchina virtuale per studenti a condizione che sia attivata e che lo studente non sia connesso alla macchina virtuale. Connettendosi alla macchina virtuale, sarà possibile accedere ai file locali nella macchina virtuale e aiutare gli studenti a risolvere i problemi.

1. Per connettersi alla macchina virtuale studente, posizionare il mouse sulla macchina virtuale nell'elenco e fare clic sul pulsante **Connetti** . 
1. Segui quindi la Guida introduttiva per gli studenti per i Chromebook, i computer Mac o i PC

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Pulsante per connettersi alla macchina virtuale di uno studente":::

## <a name="manage-users-in-a-lab"></a>Gestire gli utenti in un Lab

Gli insegnanti sono in grado di aggiungere gli utenti degli studenti a un Lab e di monitorarne le quote orarie. 

### <a name="add-users-by-email-address"></a>Aggiungi utenti in base all'indirizzo di posta elettronica

1. Dal [sito Web di Azure Lab Services](https://labs.azure.com/) fare clic su **utenti** sul lato sinistro della finestra.
1. Nella parte superiore della finestra fare clic su **Aggiungi utenti** e selezionare **Aggiungi in base all'indirizzo di posta elettronica**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-button.png" alt-text="Pulsante ' Aggiungi utenti '":::
1. Nel riquadro **Aggiungi utenti** visualizzato a destra, immettere gli indirizzi di posta elettronica degli studenti su righe separate o su una singola riga, separate da punti e virgola.
1. Fare clic su **Salva**.

    :::image type="content" source="./media/get-started-manage-labs/add-students.png" alt-text="Aggiungere studenti al Lab":::
1. L'elenco di utenti verrà ora aggiornato con messaggi di posta elettronica, stato, invito e ore di quota.

    Dopo aver registrato gli studenti per un Lab, i relativi nomi verranno aggiornati con il nome e il cognome del Azure Active Directory.

    > [!NOTE]
    > Per gli utenti, è necessario impostare l'opzione limita l'opzione limita accesso. Ciò significa che solo gli utenti elencati possono registrarsi nel Lab usando il collegamento di registrazione inviato.

### <a name="add-users-using-a-spreadsheet"></a>Aggiungere utenti tramite un foglio di calcolo 

È anche possibile aggiungere utenti caricando un file CSV che contiene gli indirizzi di posta elettronica.

1. In Microsoft Excel creare un file CSV in cui sono elencati gli indirizzi di posta elettronica degli studenti in una colonna.
1. Dal [sito web Azure Lab Services](https://labs.azure.com/), nella parte superiore della pagina **utenti** fare clic sul pulsante **Aggiungi utenti** .
1. Selezionare **carica CSV**.
1. Selezionare il file CSV che contiene gli indirizzi di posta elettronica degli studenti e fare clic su **Apri**.

    :::image type="content" source="./media/get-started-manage-labs/add-users-spreadsheet.png" alt-text="Aggiungere utenti tramite un foglio di calcolo":::
1. I messaggi di posta elettronica verranno ora visualizzati nella finestra a destra. Fare clic su **Salva**.

    :::image type="content" source="./media/get-started-manage-labs/register-users.png" alt-text="Registrare gli utenti":::

### <a name="register-users"></a>Registrare gli utenti

Una volta aggiunti gli utenti al Lab, è necessario registrarsi per accedere alle macchine virtuali. A tale scopo, è possibile invitare gli utenti dal portale, che invierà un messaggio di posta elettronica contenente il collegamento di registrazione per il Lab. Oppure copiando e incollando il collegamento di registrazione in un messaggio di posta elettronica o in un'altra forma di comunicazione con gli studenti.

1. Nella pagina **utenti** selezionare uno studente o più studenti nell'elenco.

    Nella riga per lo studente selezionato selezionare l'icona della busta nell'elenco o fare clic su **invita** nella parte superiore della schermata.

    :::image type="content" source="./media/get-started-manage-labs/send-invitation.png" alt-text="Inviare un invito":::
    
    Nella finestra **Invia invito** per messaggio di posta elettronica immettere un messaggio facoltativo, ad esempio un nome utente e una password, per gli studenti e quindi fare clic su **Invia**. 
    
    :::image type="content" source="./media/get-started-manage-labs/send-invitation-mail.png" alt-text="Invia un invito tramite posta elettronica":::

    In alternativa, è possibile fare clic sul pulsante **collegamento registrazione** nella parte superiore della schermata della stessa pagina **utenti** . 

    :::image type="content" source="./media/get-started-manage-labs/registration-link.png" alt-text="Collegamento di registrazione utente":::
    
    Copiare il collegamento di registrazione dal campo di testo e incollarlo nella posta elettronica o nello strumento di messaggistica sicura preferito.  
    
    :::image type="content" source="./media/get-started-manage-labs/user-registration.png" alt-text="Invia registrazione utente":::

Dopo aver invitato gli utenti o aver condiviso il collegamento, sarà possibile monitorare gli utenti che sono stati registrati correttamente nella pagina **utenti** della colonna **stato** . 

## <a name="clean-up-resources"></a>Pulizia delle risorse

Se non si intende continuare a usare le risorse create in questa Guida introduttiva, eliminare le risorse.

## <a name="next-steps"></a>Passaggi successivi

[Configurare un account Lab](tutorial-setup-lab-account.md)
