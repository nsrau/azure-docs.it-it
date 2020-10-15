---
title: Configurare un Lab per insegnare a MATLAB con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab per insegnare a MATLAB con Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444999"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Configurare un Lab per insegnare a MATLAB

[Matlab](https://www.mathworks.com/products/matlab.html), acronimo di Matrix Laboratory, è la piattaforma di programmazione di [MathWorks](https://www.mathworks.com/).  Combina la potenza di calcolo e la visualizzazione rendendolo lo strumento più diffuso nei campi matematici, ingegneristici, fisici e chimici.

Se si usa una [licenza a livello di Campus](https://www.mathworks.com/academia/tah-support-program/administrators.html), vedere directions on [Download matlab Installation Files](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) per scaricare i file del programma di installazione MATLAB nel computer modello.  

Questo articolo illustra come configurare una classe che usa il software client MATLAB con un server licenze.

## <a name="license-server"></a>Server licenze

Prima di modificare il computer modello per il Lab, è necessario configurare il server per l'esecuzione del software di [gestione delle licenze di rete](https://www.mathworks.com/help/install/administer-network-licenses.html) .  Queste istruzioni sono valide solo per gli istituti che scelgono l'opzione di gestione delle licenze di rete per MATLAB, che consente agli utenti di condividere un pool di codici di licenza.  Sarà inoltre necessario salvare il file di licenza e la chiave di installazione del file per un momento successivo.  Per istruzioni dettagliate su come scaricare un file di licenza, vedere il primo passaggio nell'articolo [installare Gestione licenze di rete con connessione Internet](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) .

Le istruzioni dettagliate per la procedura di installazione di un server licenze sono disponibili in [installare Gestione licenze di rete con connessione Internet](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Per consentire l'assunzione di prestiti, vedere l'articolo relativo alle [licenze di prestito](https://www.mathworks.com/help/install/license/borrow-licenses.html) .

Supponendo che il server licenze si trovi in una rete locale o in una rete privata in Azure, non dimenticare di eseguire [il peering della rete virtuale](how-to-connect-peer-virtual-network.md) all' [account Lab](tutorial-setup-lab-account.md).  Il peering di rete deve essere eseguito prima di creare il Lab in modo che le macchine virtuali del Lab possano accedere al server licenze.

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo lab, è necessaria una sottoscrizione di Azure per iniziare.  Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services o usare un account esistente.  Per creare un nuovo account Lab, vedere l' [esercitazione configurare un account Lab](tutorial-setup-lab-account.md).

Per creare un nuovo Lab, seguire [la procedura per configurare un'esercitazione in aula Lab](tutorial-setup-classroom-lab.md).  Applicare le impostazioni seguenti:

| Dimensioni della macchina virtuale | Immagine |
| -------------------- | ----- |
| Media | Windows 10 |

MATLAB è supportato in più sistemi operativi.  Per informazioni dettagliate, vedere [requisiti di sistema MATLAB](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) .

> [!WARNING]
> Non dimenticare di eseguire il [peering della rete virtuale](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) per l'account Lab alla rete virtuale per il server licenze prima di creare il Lab.

## <a name="template-machine"></a>Computer modello

Una volta creato il modello, avviare il computer e connettersi al computer per completare le attività principali seguenti.

1. Scaricare i file di installazione per il software client MATLAB.
2. Installare MATLAB usando la chiave di installazione del file.

L'installazione di MATLAB sarà un processo multiparte.  Nella prima parte vengono scaricati i file di MATLAB e di tutti gli altri prodotti che si desidera installare.  L'uso di una chiave di installazione file richiede che tutti i file di installazione per i prodotti da installare siano già scaricati.  Nella seconda parte verrà installato il software MATLAB nella macchina virtuale del modello e verrà attivato il software.  Se la macchina virtuale modello è configurata per l'attivazione tramite il server licenze, le macchine virtuali degli studenti eseguiranno la stessa operazione.

### <a name="download-installation-files"></a>Scaricare i file di installazione

È necessario essere un amministratore delle licenze per scaricare i file di installazione, nonché ottenere il file di licenza e la chiave di installazione del file.  Di seguito sono riportati i passaggi per scaricare i file di installazione.

1. Accedere all'account per [https://www.mathworks.com](https://www.mathworks.com) .
2. Scegliere **il mio account**.
3. Nella sezione **software** della pagina account fare clic sulla licenza collegata al programma di installazione di Network License Manager per il Lab.
4. Nella pagina Dettagli licenza fare clic su **Scarica prodotti**.
5. Attendere l'estrazione automatica del programma di installazione.
6. Avviare il programma di installazione.  
7. Nella pagina **accedi al tuo account MathWorks** Immetti l'account MathWorks.
8. Nella pagina **contratto di licenza MathWorks** accettare il termine e fare clic sul pulsante **Avanti** .
9. Fare clic sull'elenco a discesa **Opzioni avanzate** e scegliere **Scarica senza installare**.
10. Nella **cartella selezionare la destinazione**fare clic su **Avanti**.
11. Selezionare **Windows** come piattaforma del computer in cui verrà installato MATLAB.
12. Nella pagina **Seleziona prodotto** verificare che sia selezionato matlab insieme a qualsiasi altro prodotto MathWorks che si vuole installare.
13. Nella pagina **Conferma selezioni e Scarica** fare clic su **inizia il download**.  
14. Attendere il download dei prodotti selezionati.  Fare clic su **Fine**.

È anche possibile scaricare un'immagine ISO dal sito Web MathWorks.

1. Accedere all'account per [https://www.mathworks.com](https://www.mathworks.com) .
2. Passare a [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads).
3. Selezionare la versione di MATLAB che si vuole installare.
4. Fare clic sul collegamento "get {version}. ISO image" sotto i collegamenti correlati in cui {Version} è simile a R2020a.
5. Fare clic sul collegamento di **download della versione** blu per Windows.

### <a name="run-installer"></a>Esegui programma di installazione

Una volta scaricati i file, il secondo passaggio consiste nell'eseguire il programma di installazione. Ancora una volta, è necessario essere un amministratore delle licenze per completare questo passaggio.  Solo gli amministratori delle licenze possono installare MATLAB con una chiave di installazione del file.

1. Controllare il file di licenza scaricato e verificare che la riga SERVER elenchi correttamente il server licenze.  Per informazioni sulla modalità di formattazione del file di licenza, vedere l'articolo relativo all' [aggiornamento della licenza di rete](https://www.mathworks.com/help/install/ug/network-license-files.html), l'assunzione di [licenze](https://www.mathworks.com/help/install/license/borrow-licenses.html)e la [ricerca di ID host](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) .
2. Avviare il programma di installazione di MATLAB.
3. Nella pagina **accedi al tuo account MathWorks** Immetti l'account MathWorks.
4. Nella pagina **contratto di licenza MathWorks** accettare il termine e fare clic sul pulsante **Avanti** .
5. Fare clic sull'elenco a discesa **Opzioni avanzate** e scegliere la **chiave di installazione del file**.
6. Nella pagina **installa utilizzando la chiave di installazione file** immettere la chiave di installazione del file per il server licenze.   Fare clic su **Avanti**.
7. Nella pagina **Seleziona file di licenza** passare al file di licenza salvato quando si scaricano i file di installazione in precedenza.
8. Nella pagina **Selezione cartella di destinazione** fare clic su **Avanti**.
9. Nella pagina **Seleziona prodotti** fare clic su **Avanti**.
10. Nella pagina **Seleziona opzioni** fare clic su **Avanti**.
11. Nella pagina **Conferma selezioni e installa** fare clic su **Avvia installazione**.
12. Nella pagina **installazione completata** verificare che **Activate MATLAB** sia selezionato.  Fare clic su **Fine**.

## <a name="cost-estimate"></a>Stima dei costi

Verrà ora trattata una possibile stima dei costi per questa classe.  Questa stima non include il costo di esecuzione del server licenze.  Verrà usata una classe di 25 studenti.  Sono previste 20 ore di tempo della classe pianificata.  Ogni studente riceve inoltre una quota di 10 ore per il lavoro o assegnazioni al di fuori dell'orario di classe pianificato.  Le dimensioni della macchina virtuale scelte sono medie, ovvero 55 unità Lab.

Di seguito è riportato un esempio di una possibile stima dei costi per questa classe:

25 studenti \* (20 ore pianificate + 10 ore di quota) \* 55 unità Lab \*  0,01 USD all'ora = 412,50 USD

>[!IMPORTANT]
> La stima dei costi è solo a scopo esemplificativo.  Per informazioni aggiornate sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare, gestire e pubblicare un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
