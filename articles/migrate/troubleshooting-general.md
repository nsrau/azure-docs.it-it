---
title: Risolvere problemi relativi ad Azure Migrate | Microsoft Docs
description: Questo articolo offre una panoramica dei problemi noti relativi al servizio Azure Migrate e alcuni suggerimenti per la risoluzione degli errori comuni.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 1fcc9e12e63eda73d53ae2085bc2a64d31ea2067
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="troubleshoot-azure-migrate"></a>Risolvere i problemi relativi ad Azure Migrate

## <a name="troubleshoot-common-errors"></a>Risoluzione dei problemi comuni

[Azure Migrate](migrate-overview.md) valuta i carichi di lavoro locali per la migrazione ad Azure. Attenersi a questo articolo per risolvere i problemi che possono verificarsi durante la distribuzione e l'utilizzo di Azure Migrate.


**L'agente di raccolta non è in grado di connettersi a Internet**

Ciò può verificarsi quando il computer in uso è protetto da un proxy. Assicurarsi di fornire le credenziali di autorizzazione, se richiesto dal proxy.
Se si usa qualsiasi proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di inserire nell'elenco dei consentiti questi URL richiesti:

**URL** | **Scopo**  
--- | ---
*.portal.azure.com | Necessario per controllare la connettività al servizio di Azure e convalidare i problemi relativi alla sincronizzazione dell'ora.
*.oneget.org | Necessario per il download del modulo vCenter PowerCLI basato su PowerShell.

**L'agente di raccolta non riesce a connettersi al progetto usando l'ID progetto e la chiave copiata dal portale.**

Verificare di aver copiato e incollato le informazioni corrette. Per risolvere il problema, installare Microsoft Monitoring Agent (MMA) come descritto di seguito.

1. Scaricare [MMA](https://go.microsoft.com/fwlink/?LinkId=828603) nella macchina virtuale dell'agente di raccolta.
2. Per avviare l'installazione, fare doppio clic sul file scaricato.
3. Nella pagina di **benvenuto** della procedura di configurazione fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
4. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
5. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics (OMS)** > **Avanti**.
6. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro di Log Analytics. Incollare l'ID progetto e la chiave copiata in precedenza. Quindi fare clic su **Next**.
7. Verificare che l'agente riesca a connettersi al progetto. In caso contrario, verificare le impostazioni. Se l'agente riesce a connettersi, ma non l'agente di raccolta, contattare il supporto tecnico.


**Errore 802: viene visualizzato un errore di sincronizzazione di data e ora.**

È possibile che l'orologio del server abbia perso la sincronizzazione con l'ora corrente da più di cinque minuti. Modificare l'ora nella macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente, come illustrato di seguito:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
2. Per controllare il fuso orario, eseguire w32tm /tz.
3. Per sincronizzare l'ora, eseguire w32tm /resync.

**Nella parte finale della chiave di progetto sono presenti simboli "= =", codificati in altri caratteri alfanumerici dall'agente di raccolta. È normale?**

Sì, ogni chiave di progetto termina con "= =". L'agente di raccolta esegue la crittografia della chiave di progetto prima di elaborarla.

**I dati sulle prestazioni relative ai dischi e alle schede di rete sono pari a zero** 

Questo problema può verificarsi se il livello delle impostazioni relative alle statistiche nel server vCenter è impostato su un valore inferiore a tre. Se impostato sul livello tre o superiore, vCenter archivia la cronologia delle prestazioni della macchina virtuale per scopi di calcolo, archiviazione e rete. Se impostato su un livello inferiore a tre, vCenter non archivia dati di archiviazione e di rete ma solo dati relativi alla CPU e alla memoria. In questo scenario, i dati sulle prestazioni risultano pari a zero in Azure Migrate, che fornisce indicazioni sulle dimensioni di dischi e reti in base ai metadati raccolti dalle macchine locali.

Per abilitare la raccolta dei dati sulle prestazioni di dischi e reti, impostare il livello relativo alle impostazioni delle statistiche su tre. Attendere quindi almeno un giorno per identificare l'ambiente e valutarlo. 

**Ho installato gli agenti e usato la visualizzazione delle dipendenze per creare gruppi. Ora, in seguito a un failover, sulle macchine viene visualizzata l'azione "Installa agente" anziché "Visualizza dipendenze"**
* In seguito a un failover pianificato o non pianificato, le macchine locali vengono disattivate e le macchine equivalenti vengono riattivate in Azure. Queste macchine acquisiscono un indirizzo MAC diverso ed eventualmente anche un indirizzo IP diverso, in base alla scelta dell'utente di mantenere o meno l'indirizzo IP locale. Se gli indirizzi IP e MAC sono diversi, Azure Migrate non associa le macchine locali ai dati sulle dipendenze di Elenco dei servizi e chiede all'utente di installare gli agenti anziché visualizzare le dipendenze.
* In seguito al failover di test, le macchine locali rimangono attivate come previsto. Le macchine equivalenti riattivate in Azure acquisiscono un indirizzo MAC diverso ed eventualmente anche un indirizzo IP diverso. Se l'utente non blocca il traffico di OMS in uscita da queste macchine, Azure Migrate non associa le macchine locali ai dati sulle dipendenze di Elenco dei servizi e chiede all'utente di installare gli agenti anziché visualizzare le dipendenze.


## <a name="troubleshoot-readiness-issues"></a>Risolvere problemi di idoneità

**Problema** | **Correzione**
--- | ---
Tipo di avvio non supportato | Passare al BIOS prima di eseguire una migrazione.
Il numero di dischi supera il limite | Rimuovere i dischi non usati dalla macchina prima di eseguire la migrazione.
Le dimensioni del disco hanno superato il limite | Ridurre le dimensioni del disco a meno di 4 TB prima di eseguire la migrazione. 
Il disco non è disponibile nella località specificata | Assicurarsi che il disco si trovi nella località di destinazione prima di eseguire la migrazione.
Il disco non è disponibile per la ridondanza specificata | Il disco deve usare il tipo di archiviazione di ridondanza definito nelle impostazioni di valutazione (per impostazione predefinita, LRS).
Non è stato possibile determinare l'idoneità del disco a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo. 
La macchina virtuale con core e memoria richiesti non è stata trovata | Azure non può trovare un tipo di macchina virtuale idonea. Prima di eseguire la migrazione, quindi, ridurre la memoria e il numero di core della macchina locale. 
Uno o più dischi non idonei. | Prima di eseguire una migrazione, verificare che le dimensioni dei dischi locali non superino 4 TB.
Una o più schede di rete non idonee. | Prima di eseguire la migrazione, rimuovere le schede di rete non usate dalla macchina.
Non è stato possibile determinare l'idoneità della macchina virtuale a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo. 
Non è stato possibile determinare l'idoneità di uno o più dischi a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo.
Non è stato possibile determinare l'idoneità di una o più schede di rete a causa di un errore interno. | Provare a creare una nuova valutazione per il gruppo.
Non è stata trovata alcuna macchina virtuale per le prestazioni di archiviazione richieste. | Le prestazioni di archiviazione (IOPS/velocità effettiva) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Prima di eseguire la migrazione, ridurre i requisiti di archiviazione per la macchina.
Non è stata trovata alcuna macchina virtuale per le prestazioni di rete richieste. | Le prestazioni di rete (ingresso/uscita) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Ridurre i requisiti di rete per la macchina. 
Non è stata trovata alcuna macchina virtuale per il piano tariffario specificato. | Verificare le impostazioni del piano tariffario. 
La macchina virtuale non è stata trovata nella località specificata. | Usare una località di destinazione diversa prima di eseguire migrazione.
Problemi di supporto del sistema operativo Linux | Assicurarsi di eseguire la versione a 64 bit dei [sistemi operativi](../virtual-machines/linux/endorsed-distros.md) supportati.
Problemi di supporto del sistema operativo Windows | Assicurarsi che sia eseguito un sistema operativo supportato. [Altre informazioni](concepts-assessment-calculation.md#azure-suitability-analysis)
Sistema operativo sconosciuto. | Verificare che il sistema operativo specificato in vCenter sia corretto e ripetere il processo di individuazione.
Richiede una sottoscrizione di Visual Studio. | I sistemi operativi client di Windows sono gli unici supportati dalle sottoscrizioni di Visual Studio (MSDN).


## <a name="collect-logs"></a>Raccogliere i log

**Come è possibile raccogliere i log nella macchina virtuale dell'agente di raccolta?**

La funzionalità di creazione di log è abilitata per impostazione predefinita. I log si trovano nei percorsi seguenti:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Per raccogliere Event Trace for Windows, seguire questa procedura:

1. Nella macchina virtuale dell'agente di raccolta aprire una finestra di comando di PowerShell.
2. Eseguire **Get-EventLog -LogName Application | export-csv eventlog.csv**.

**Come è possibile raccogliere i log relativi al traffico di rete del portale?**

1. Aprire il browser e accedere al [portale](https://portal.azure.com).
2. Premere F12 per avviare Strumenti di sviluppo. Se necessario, deselezionare l'impostazione **Cancella voci durante l'esplorazione**.
3. Fare clic sulla scheda **Rete** e avviare l'acquisizione del traffico di rete:
 - In Chrome selezionare **Preserve log** (Mantieni log). La registrazione viene avviata automaticamente. Un cerchio rosso indica che è in corso l'acquisizione del traffico. Se non viene visualizzato, fare clic sul cerchio nero per avviarla.
 - Anche in Edge/Internet Explorer la registrazione viene avviata automaticamente. In caso contrario, fare clic sul pulsante di esecuzione verde.
4. Provare a riprodurre l'errore.
5. Dopo aver riscontrato l'errore durante la registrazione, arrestare la registrazione e salvare una copia dell'attività registrata:
 - In Chrome fare clic con il pulsante destro del mouse su **Save as HAR with content** (Salva come HAR con contenuto). In questo modo i log vengono compressi ed esportati come file con estensione har.
 - In Edge/Internet Explorer fare clic sull'icona **Esporta traffico catturato**. In questo modo il log viene compresso ed esportato.
6. Passare alla scheda **Console** per verificare la presenza di eventuali avvisi o errori. Per salvare il log della console:
 - In Chrome fare clic con il pulsante destro del mouse in un punto qualsiasi del log della console. Selezionare **Salva con nome** per esportare e comprimere il log.
 - In Edge/Internet Explorer fare clic con il pulsante destro del mouse sugli errori e selezionare **Copia tutto**. 
7. Chiudere Strumenti di sviluppo.
 



