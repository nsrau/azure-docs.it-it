---
title: Appliance Agente di raccolta in Azure Migrate | Microsoft Docs
description: Offre una panoramica dell'appliance Agente di raccolta e illustra come configurarla.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: 49f3d5ba55a9c1abfcd6dcb50058ed7a001a2eec
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="collector-appliance"></a>Appliance Agente di raccolta

[Azure Migrate](migrate-overview.md) valuta i carichi di lavoro locali per la migrazione ad Azure. Questo articolo fornisce informazioni su come usare l'appliance Agente di raccolta.



## <a name="overview"></a>Panoramica

Agente di raccolta di Azure Migrate è un'appliance leggera che può essere usata per l'individuazione dell'ambiente vCenter locale. L'appliance individua i computer VMware locali e ne invia i metadati al servizio Azure Migrate.

L'appliance Agente di raccolta è un pacchetto OVF che è possibile scaricare dal progetto di Azure Migrate. Crea un'istanza di una macchina virtuale VMware con 4 core, 8 GB di RAM e un disco da 80 GB. Il sistema operativo dell'appliance è Windows Server 2012 R2 (64 bit).

È possibile creare l'agente di raccolta seguendo i passaggi descritti in [Creare la macchina virtuale dell'agente di raccolta](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Diagramma di comunicazione dell'agente di raccolta

![Diagramma di comunicazione dell'agente di raccolta](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Componente      | Comunicazione con   | Porta necessaria                            | Motivo                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Agente di raccolta      | Servizio Azure Migrate | TCP 443                                  | L'agente di raccolta deve essere in grado di comunicare con il servizio tramite la porta SSL 443 |
| Agente di raccolta      | Server vCenter        | Porta predefinita 443                             | L'agente di raccolta deve essere in grado di comunicare con il server vCenter. Si connette a vCenter tramite la porta 443 per impostazione predefinita. Se il server vCenter è in ascolto su una porta diversa, la porta deve essere disponibile come porta in uscita nell'agente di raccolta |
| Agente di raccolta      | RDP|   | TCP 3389 | Per consentire le comunicazioni tramite protocollo RDP al computer dell'agente di raccolta |





## <a name="collector-pre-requisites"></a>Prerequisiti di Agente di raccolta

Agente di raccolta deve superare alcuni controlli dei prerequisiti per verificare che possa connettersi al servizio Azure Migrate e caricare i dati individuati. Questo articolo illustra i prerequisiti e i motivi per cui sono necessari.

### <a name="internet-connectivity"></a>Connettività Internet

L'appliance dell'agente di raccolta deve essere connessa a Internet per inviare le informazioni sui computer individuati. È possibile connettere il computer a Internet in due modi.

1. Si può configurare Agente di raccolta per disporre di connettività Internet diretta.
2. Si può configurare Agente di raccolta per la connessione tramite un server proxy.
    * Se il server proxy richiede l'autenticazione, è possibile specificare il nome utente e la password nelle impostazioni di connessione.
    * L'IP indirizzo/FQDN del server proxy deve essere in formato http://IndirizzoIP o http://FQDN. È supportato solo il proxy HTTP.

> [!NOTE]
> I server proxy basati su HTTPS non sono supportati dall'agente di raccolta.

#### <a name="whitelisting-urls-for-internet-connection"></a>Inserimento degli URL per la connessione Internet nell'elenco elementi consentiti

Il controllo dei prerequisiti ha esito positivo se Agente di raccolta può connettersi a Internet usando le impostazioni fornite. Il controllo della connettività viene convalidato mediante la connessione a un elenco di URL indicati nella tabella seguente. Se si usa qualsiasi proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di inserire nell'elenco dei consentiti questi URL richiesti:

**URL** | **Scopo**  
--- | ---
*.portal.azure.com | Necessario per controllare la connettività al servizio di Azure e convalidare i problemi relativi alla sincronizzazione dell'ora.

Inoltre, il controllo tenta anche di convalidare la connettività agli URL seguenti, ma non ha esito negativo se questi non sono accessibili. La configurazione dell'elenco elementi consentiti per gli URL seguenti è facoltativa, ma per risolvere i problemi rilevati dal controllo dei prerequisiti è necessario eseguire procedure manuali.

**URL** | **Scopo**  | **Se non inserito nell'elenco elementi consentiti**
--- | --- | ---
*.oneget.org:443 | Necessario per il download del modulo vCenter PowerCLI basato su PowerShell. | L'installazione di PowerCLI non riesce. Installare manualmente il modulo.
*.windows.net:443 | Necessario per il download del modulo vCenter PowerCLI basato su PowerShell. | L'installazione di PowerCLI non riesce. Installare manualmente il modulo.
*.windowsazure.com:443 | Necessario per il download del modulo vCenter PowerCLI basato su PowerShell. | L'installazione di PowerCLI non riesce. Installare manualmente il modulo.
*.powershellgallery.com:443 | Necessario per il download del modulo vCenter PowerCLI basato su PowerShell. | L'installazione di PowerCLI non riesce. Installare manualmente il modulo.
*.msecnd.net:443 | Necessario per il download del modulo vCenter PowerCLI basato su PowerShell. | L'installazione di PowerCLI non riesce. Installare manualmente il modulo.
*.visualstudio.com:443 | Necessario per il download del modulo vCenter PowerCLI basato su PowerShell. | L'installazione di PowerCLI non riesce. Installare manualmente il modulo.

### <a name="time-is-in-sync-with-the-internet-server"></a>L'ora deve essere sincronizzata con il server Internet

Agente di raccolta deve essere sincronizzato con il server di riferimento ora Internet per assicurarsi che le richieste al servizio siano autenticate. Perché sia possibile convalidare l'orario, Agente di raccolta deve poter raggiungere l'URL portal.azure.com. Se il computer non è sincronizzato, modificare l'ora nella macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente, come illustrato di seguito:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
1. Per controllare il fuso orario, eseguire w32tm /tz.
1. Per sincronizzare l'ora, eseguire w32tm /resync.

### <a name="collector-service-should-be-running"></a>Il servizio Agente di raccolta deve essere in esecuzione

Il servizio Agente di raccolta di Azure Migrate deve essere in esecuzione nel computer. Questo servizio viene avviato automaticamente all'avvio del computer. Se il servizio *Agente di raccolta di Azure Migrate* non è in esecuzione, è possibile avviarlo tramite il pannello di controllo. Il servizio Agente di raccolta è responsabile della connessione al server vCenter, della raccolta dei metadati e dei dati sulle prestazioni delle macchine virtuali e dell'invio al servizio.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5 

Il modulo PowerShell VMware PowerCLI deve essere installato perché Agente di raccolta possa comunicare con il server vCenter ed eseguire query relative ai dettagli e ai dati sulle prestazioni delle macchine virtuali. Il modulo PowerShell viene scaricato e installato automaticamente come parte del controllo dei prerequisiti. Per il download automatico è necessaria la presenza di alcuni URL nell'elenco elementi consentiti. In caso contrario occorre fornire accesso inserendoli nell'elenco elementi consentiti oppure installare manualmente il modulo.

Installare il modulo manualmente seguendo questa procedura:

1. Per installare PowerCLI in Agente di raccolta senza connessione Internet, seguire i passaggi forniti in [questo collegamento](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html).
2. Dopo aver installato il modulo PowerShell in un computer diverso, dotato di accesso a Internet, copiare i file VMware.* da tale computer al computer dell'agente di raccolta.
3. Riavviare i controlli dei prerequisiti e verificare che PowerCLI sia installato.

## <a name="connecting-to-vcenter-server"></a>Connessione al server vCenter

Agente di raccolta deve connettersi al server vCenter ed essere in grado di eseguire query per le macchine virtuali, i relativi metadati e i relativi contatori delle prestazioni. Questi dati vengono usati dal progetto per calcolare una valutazione.

1. Per eseguire la connessione al server vCenter è possibile usare un account di sola lettura con le autorizzazioni indicate nella tabella seguente per eseguire l'individuazione. 

    |Attività  |Ruolo/account richiesto  |Autorizzazioni  |
    |---------|---------|---------|
    |Individuazione basata sull'appliance Agente di raccolta    | È necessario almeno un utente di sola lettura        |Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura)         |

2. Solo i data center accessibili all'account vCenter sono disponibili per l'individuazione.
3. È necessario specificare il FQDN/indirizzo IP per la connessione al server vCenter. Per impostazione predefinita verrà usata la porta 443. Se il server vCenter è configurato per l'ascolto su un numero di porta diverso, è possibile specificarlo come parte dell'indirizzo del server nel formato IndirizzoIP:Numero_porta o FQDN:Numero_porta.
4. Prima di iniziare la distribuzione, è consigliabile impostare le statistiche del server vCenter sul livello 3. Se si imposta un livello inferiore a 3, viene eseguita l'individuazione, ma non vengono raccolti i dati sulle prestazioni per l'archiviazione e la rete. In questo caso, i consigli della valutazione relativi alle dimensioni si baseranno sui dati delle prestazioni per la CPU e la memoria e solo sui dati di configurazione per le schede del disco e di rete. [Altre informazioni](./concepts-collector.md) sui dati raccolti e sul modo in cui incidono sulla valutazione.
5. Agente di raccolta deve avere visibilità di rete sul server vCenter.

> [!NOTE]
> Sono ufficialmente supportati solo i server VMware vCenter versioni 5.5, 6.0 e 6.5.

> [!IMPORTANT]
> È consigliabile impostare il livello comune più alto (livello 3) per le statistiche in modo che tutti i contatori vengano raccolti correttamente. Se vCenter è impostato su un livello inferiore, è possibile che solo alcuni contatori vengano raccolti completamente, mentre i restanti vengono impostati su 0. La valutazione potrebbe di conseguenza generare dati incompleti. 

### <a name="selecting-the-scope-for-discovery"></a>Selezione dell'ambito di individuazione

Dopo la connessione a vCenter, è possibile selezionare un ambito di individuazione. Selezionando un ambito vengono individuate tutte le macchine virtuali nel percorso dell'inventario vCenter specificato.

1. L'ambito può essere un data center, una cartella o un host ESXi. 
2. È possibile selezionare solo un ambito alla volta. Per selezionare più macchine virtuali, è possibile completare un'individuazione e riavviare il processo di individuazione con un nuovo ambito.
3. È possibile selezionare solo un ambito con *meno di 1000 macchine virtuali*. Se si seleziona un ambito con più di 1000 macchine virtuali, è necessario suddividere l'ambito in unità più piccole mediante la creazione di cartelle. Successivamente, è necessario eseguire individuazioni indipendenti nelle cartelle più piccole.

## <a name="specify-migration-project"></a>Specificare il progetto di migrazione

Dopo la connessione al vCenter locale e dopo aver specificato un ambito, è possibile specificare i dettagli del progetto di migrazione da usare per l'individuazione e la valutazione. Specificare l'ID e la chiave del progetto ed eseguire la connessione.

## <a name="start-discovery-and-view-collection-progress"></a>Avviare l'individuazione e visualizzare lo stato della raccolta

Una volta avviata l'individuazione, le macchine virtuali vCenter vengono individuate e i relativi metadati e dati sulle prestazioni vengono inviati al server. Lo stato di avanzamento mostra inoltre, gli ID seguenti:

1. ID di agente di raccolta: ID univoco assegnato al computer dell'agente di raccolta. Questo ID resta invariato per un computer specifico tra diverse individuazioni. È possibile usarlo in caso di errori quando si segnala il problema al supporto tecnico Microsoft.
2. ID sessione: ID univoco del processo di raccolta in esecuzione. È possibile fare riferimento allo stesso ID di sessione nel portale al completamento del processo di individuazione. Questo ID cambia per ogni processo di raccolta. In caso di errori, è possibile comunicarlo al supporto tecnico Microsoft.

### <a name="what-data-is-collected"></a>Quali dati vengono raccolti?

Il processo di raccolta individua i metadati statici seguenti relativi alle macchine virtuali selezionate. 

1. Nome visualizzato della macchina virtuale (in vCenter)
2. Percorso dell'inventario della macchina virtuale (host/cartella in vCenter)
3. Indirizzo IP
4. Indirizzo MAC
5. Numero di core, dischi, schede di interfaccia di rete
6. RAM, dimensioni dei dischi
7. Contatori delle prestazioni per macchina virtuale, disco e rete, come indicato nella tabella di seguito.

La tabella seguente contiene un elenco dei contatori delle prestazioni raccolti e un elenco dei risultati della valutazione che saranno compromessi se un determinato contatore non viene raccolto.

|Contatore                                  |Level    |Livello per dispositivo  |Impatto sulla valutazione                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |ND                |Dimensione e costi consigliati della macchina virtuale                    |
|mem.usage.average                        | 1       |ND                |Dimensione e costi consigliati della macchina virtuale                    |
|virtualDisk.read.average                 | 2       |2                 |Dimensione disco, costi di archiviazione e dimensione della macchina virtuale         |
|virtualDisk.write.average                | 2       |2                 |Dimensione disco, costi di archiviazione e dimensione della macchina virtuale         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Dimensione disco, costi di archiviazione e dimensione della macchina virtuale         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Dimensione disco, costi di archiviazione e dimensione della macchina virtuale         |
|net.received.average                     | 2       |3                 |Dimensione della macchina virtuale e costi della rete                        |
|net.transmitted.average                  | 2       |3                 |Dimensione della macchina virtuale e costi della rete                        |

> [!WARNING]
> Se è stato appena impostato un livello più alto per le statistiche, la generazione dei contatori delle prestazioni richiederà fino a un giorno. È quindi consigliabile eseguire l'individuazione dopo un giorno.

### <a name="time-required-to-complete-the-collection"></a>Tempo necessario per completare la raccolta

Agente di raccolta si limita a individuare i dati sui computer e inviarli al progetto. Il progetto potrebbe richiedere ulteriore tempo prima che i dati individuati vengano visualizzati nel portale e sia possibile avviare la creazione di una valutazione.

In base al numero di macchine virtuali nell'ambito selezionato, sono necessari fino a 15 minuti per l'invio dei metadati statici al progetto. Quando i metadati statici sono disponibili nel portale, è possibile visualizzare l'elenco dei computer nel portale e avviare la creazione di gruppi. Non è possibile creare una valutazione finché il processo di raccolta non viene completato e il progetto non ha elaborato i dati. Una volta completato il processo di raccolta in Agente di raccolta, perché i dati sulle prestazioni siano disponibili nel portale può essere necessaria fino a un'ora, in base al numero di macchine virtuali nell'ambito selezionato.

## <a name="how-to-upgrade-collector"></a>Come eseguire l'aggiornamento dell'agente di raccolta

È possibile aggiornare l'agente di raccolta per la versione più recente senza scaricare nuovamente il file con estensione OVA.

1. Scaricare il [pacchetto di aggiornamento](https://aka.ms/migrate/col/latestupgrade) più recente.
2. Per garantire la sicurezza dell'hotfix scaricato, aprire il prompt dei comandi come amministratore ed eseguire il comando seguente per generare l'hash del file ZIP. L'hash generato deve corrispondere con all'hash indicato nella versione specifica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    
    (esempio d'uso C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.5.zip SHA256)
3. Copiare il file ZIP nella macchina virtuale dell'agente di raccolta di Azure Migrate (Agente di raccolta di Azure Migrate).
4. Fare clic con il pulsante destro del mouse sul file ZIP e selezionare Estrai tutto.
5. Fare clic con il pulsante destro del mouse sul file Setup.ps1, selezionare Run with PowerShell (Esegui con PowerShell) e quindi seguire le istruzioni visualizzate sullo schermo per installare l'aggiornamento.

### <a name="list-of-updates"></a>Elenco degli aggiornamenti

#### <a name="upgrade-to-version-1095"></a>Eseguire l'aggiornamento alla versione 1.0.9.5

Per eseguire l'aggiornamento alla 1.0.9.5, scaricare questo [pacchetto](https://aka.ms/migrate/col/upgrade_9_5)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | d969ebf3bdacc3952df0310d8891ffdf
SHA1 | f96cc428eaa49d597eb77e51721dec600af19d53
SHA256 | 07c03abaac686faca1e82aef8b80e8ad8eca39067f1f80b4038967be1dc86fa1

## <a name="next-steps"></a>Passaggi successivi

[Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure](tutorial-assessment-vmware.md)
