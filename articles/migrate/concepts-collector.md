---
title: Appliance Agente di raccolta in Azure Migrate | Microsoft Docs
description: Offre una panoramica dell'appliance Agente di raccolta e illustra come configurarla.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/25/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: 74caf0ab052e1f6558dc20d15d84c01177b3f9cb
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665581"
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
    * L'IP indirizzo/FQDN del server proxy deve essere in formato http://IPaddress o http://FQDN. È supportato solo il proxy HTTP.

> [!NOTE]
> I server proxy basati su HTTPS non sono supportati dall'agente di raccolta.

#### <a name="internet-connectivity-with-intercepting-proxy"></a>Connettività Internet con proxy di intercettazione

Se il server proxy usato per la connessione a Internet è un proxy di intercettazione, occorre importare il certificato del proxy nella macchina virtuale dell'agente di raccolta. Seguono i passaggi per importare il certificato nella macchina virtuale dell'agente di raccolta.

1. Nella macchina virtuale dell'agente di raccolta, passare al **Menu Start** quindi trovare e aprire **Gestisci i certificati computer**.
2. Nello strumento Certificati, nel riquadro sinistro, sotto **Certificati - Computer locale**, trovare **Autori attendibili**. In **Autori attendibili**, fare clic su **Certificati** per visualizzare l'elenco dei certificati nel riquadro a destra.

    ![Strumento Certificati](./media/concepts-intercepting-proxy/certificates-tool.png)

3. Copiare il certificato di proxy nella macchina virtuale dell'agente di raccolta. Potrebbe essere necessario contattare il team di amministrazione di rete dell'organizzazione per ottenere il certificato.
4. Fare doppio clic sul certificato per aprirlo. Fare clic su **Installa certificato**. Verrà avviata l'importazione guidata del certificato.
5. Nell'importazione guidata del certificato per Percorso archivio scegliere **Computer locale**. **Fare clic su Avanti**.

    ![Percorso dell'archivio certificati](./media/concepts-intercepting-proxy/certificate-store-location.png)

6. Selezionare l'opzione **Place all certificates in the following store** (Colloca tutti i certificati nell'archivio seguente). Fare clic su **Esplora** e selezionare **Autori attendibili** dall'elenco dei certificati visualizzato. Fare clic su **Avanti**.

    ![Archivio certificati](./media/concepts-intercepting-proxy/certificate-store.png)
    
7. Fare clic su **Fine**. Questa operazione importa il certificato. 
8. Facoltativamente, è possibile verificare che il certificato sia stato importato aprendo lo strumento Certificato come indicato nei passaggi 1 e 2 precedenti.
9. Nell'app dell'agente di raccolta Azure Migrate verificare che il controllo dei prerequisiti di connettività Internet abbia avuto esito positivo.


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
3. È possibile selezionare solo un ambito con *meno di 1500 macchine virtuali*.

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
5. Sistema operativo
5. Numero di core, dischi, schede di interfaccia di rete
6. Dimensione della memoria, dimensioni dei dischi
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

## <a name="locking-down-the-collector-appliance"></a>Blocco dell'appliance dell'agente di raccolta
È consigliabile eseguire aggiornamenti continui di Windows sull'appliance dell'agente di raccolta. Se un agente di raccolta non viene aggiornato per 60 giorni, avvierà l'arresto automatico del computer. Qualora sia in corso un processo di individuazione, anche se viene superato il periodo di 60 giorni, il computer non si spegnerà. Al termine del processo, verrà tuttavia spento. Se si usa l'agente di raccolta per più di 45 giorni, è consigliabile mantenere sempre aggiornato il computer eseguendo Windows Update.

È inoltre consigliabile adottare le seguenti misure per proteggere l'appliance
1. Non condividere le password di amministratore con parti non autorizzate, né smarrirle.
2. Arrestare l'appliance quando non è in uso.
3. Collegare l'appliance in una rete protetta.
4. Al termine dell'operazione di migrazione, eliminare l'istanza dell'appliance. Assicurarsi di eliminare anche i file di supporto dei dischi (file VMDK), poiché i dischi potrebbero contenere credenziali di vCenter memorizzate nella cache.

## <a name="how-to-upgrade-collector"></a>Come eseguire l'aggiornamento dell'agente di raccolta

È possibile aggiornare l'agente di raccolta per la versione più recente senza scaricare nuovamente il file con estensione OVA.

1. Scaricare il [pacchetto di aggiornamento](https://aka.ms/migrate/col/upgrade_9_14) più recente (versione 1.0.9.14).
2. Per garantire la sicurezza dell'hotfix scaricato, aprire il prompt dei comandi come amministratore ed eseguire il comando seguente per generare l'hash del file ZIP. L'hash generato deve corrispondere con all'hash indicato nella versione specifica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (esempio d'utilizzo C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Copiare il file ZIP nella macchina virtuale dell'agente di raccolta di Azure Migrate (Agente di raccolta di Azure Migrate).
4. Fare clic con il pulsante destro del mouse sul file ZIP e selezionare Estrai tutto.
5. Fare clic con il pulsante destro del mouse sul file Setup.ps1, selezionare Run with PowerShell (Esegui con PowerShell) e quindi seguire le istruzioni visualizzate sullo schermo per installare l'aggiornamento.

### <a name="list-of-updates"></a>Elenco degli aggiornamenti

#### <a name="upgrade-to-version-10914"></a>Eseguire l'aggiornamento alla versione 1.0.9.14

Valori hash per l'aggiornamento al [pacchetto 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="upgrade-to-version-10913"></a>Eseguire l'aggiornamento alla versione 1.0.9.13

Valori hash per l'aggiornamento al [pacchetto 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

#### <a name="upgrade-to-version-10911"></a>Eseguire l'aggiornamento alla versione 1.0.9.11

Valori hash per l'aggiornamento al [pacchetto 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>Eseguire l'aggiornamento alla versione 1.0.9.7

Valori hash per l'aggiornamento al [pacchetto 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritmo** | **Valore hash**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>Passaggi successivi

[Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure](tutorial-assessment-vmware.md)
