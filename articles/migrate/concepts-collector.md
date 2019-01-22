---
title: Informazioni sull'appliance Agente di raccolta in Azure Migrate | Microsoft Docs
description: L'articolo contiene informazioni sull'appliance Agente di raccolta in Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: b9387814b8bdab56117dec27de1e3d5b44ce39b4
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262609"
---
# <a name="about-the-collector-appliance"></a>Informazioni sull'appliance Agente di raccolta

 Questo articolo contiene informazioni su Agente di raccolta di Azure Migrate.

Agente di raccolta di Azure Migrate è un'appliance leggera che può essere usata per individuare l'ambiente vCenter locale a scopo di valutazione con il servizio [Azure Migrate](migrate-overview.md), prima della migrazione ad Azure.  

## <a name="discovery-method"></a>Metodo di individuazione

Per l'appliance Agente di raccolta, un tempo esistevano due opzioni, per l'individuazione una tantum e l'individuazione continua. Il modello di individuazione una tantum è ora deprecato, poiché si basava su impostazioni delle statistiche del server vCenter per la raccolta di dati delle prestazioni (con impostazioni delle statistiche necessariamente impostate sul livello 3) e raccoglieva anche i contatori medi (anziché di picco), con conseguente ridimensionamento insufficiente. Il modello di individuazione continua assicura la raccolta dei dati granulari e produce un ridimensionamento accurato grazie alla raccolta di contatori di picco. Ecco come funziona:

L'appliance Agente di raccolta è connessa in modo continuativo al progetto Azure Migrate e raccoglie continuamente i dati sulle prestazioni delle macchine virtuali.

- Agente di raccolta esegue continuamente una profilatura dell'ambiente locale per raccogliere i dati d'uso in tempo reale ogni 20 secondi.
- L'appliance esegue il rollup dei campioni raccolti ogni 20 secondi e crea un singolo punto dati ogni 15 minuti.
- Per creare il punto dati, l'appliance seleziona il valore di picco dai campioni raccolti ogni 20 secondi e lo invia ad Azure.
- Questo modello non dipende dalle impostazioni delle statistiche del server vCenter per raccogliere i dati sulle prestazioni.
- È possibile arrestare la profilatura continua in qualsiasi momento In Agente di raccolta.

**Risultati immediati:** con l'appliance per l'individuazione continua, è possibile creare le valutazioni subito dopo che l'individuazione è completata. Il processo di individuazione richiede circa due ore, a seconda del numero di macchine virtuali. Poiché la raccolta dei dati sulle prestazioni inizia quando viene avviata l'individuazione, se si vogliono ottenere risultati immediati è necessario selezionare *Come in locale* come criterio di dimensionamento per la valutazione. Per le valutazioni basate sulle prestazioni, è consigliabile attendere almeno un giorno dall'avvio del processo di individuazione per ottenere indicazioni affidabili relative alle dimensioni.

L'appliance si limita a raccogliere i dati sulle prestazioni in modo continuo, non rileva eventuali modifiche alla configurazione nell'ambiente locale (ad esempio, aggiunta ed eliminazione di macchine virtuali, aggiunta di dischi e così via). Se si esegue una modifica della configurazione nell'ambiente locale, è possibile procedere come segue per riflettere le modifiche nel portale:

- Aggiunta di elementi (macchine virtuali, dischi, core e così via): per riflettere tali modifiche nel portale di Azure, è possibile arrestare l'individuazione dall'appliance e quindi riavviarla. Ciò garantisce che le modifiche vengono aggiornate nel progetto Azure Migrate.

- Eliminazione di macchine virtuali: a causa del modo in cui è progettata l'appliance, l'eliminazione di macchine virtuali non viene rilevata anche se si arresta e riavvia l'individuazione. I dati acquisiti dalle individuazioni successive vengono infatti aggiunti alle individuazioni precedenti e non sostituiti. In questo caso è possibile semplicemente ignorare la macchina virtuale nel portale, rimuovendola dal gruppo e ricalcolando la valutazione.

> [!NOTE]
> L'appliance per l'individuazione una tantum è ora deprecata poiché questo metodo si basava sulle impostazioni delle statistiche del server vCenter relative alla disponibilità dei punti dati delle prestazioni e raccoglieva i contatori delle prestazioni medie, determinando così un sottodimensionamento delle macchine virtuali per la migrazione ad Azure.

## <a name="deploying-the-collector"></a>Distribuzione di Agente di raccolta

Per distribuire l'appliance Agente di raccolta, usare un modello OVF nel modo indicato di seguito.

- Scaricare il modello OVF da un progetto Azure Migrate nel portale di Azure. Importare il file scaricato per nel server vCenter per configurare la macchina virtuale dell'appliance Agente di raccolta.
- In OVF VMware consente di configurare una macchina virtuale con 8 core, 16 GB di RAM e un disco da 80 GB. Il sistema operativo è Windows Server 2016 (64 bit).
- Quando si esegue Agente di raccolta, vengono controllati alcuni prerequisiti per verificare che l'appliance sia in grado di connettersi ad Azure Migrate.

- [Altre informazioni](tutorial-assessment-vmware.md#create-the-collector-vm) sulla creazione di Agente di raccolta.


## <a name="collector-prerequisites"></a>Prerequisiti di Agente di raccolta

Agente di raccolta deve superare alcuni controlli dei prerequisiti per verificare che possa connettersi al servizio Azure Migrate e caricare i dati individuati.

- **Verificare il cloud di Azure**: Agente di raccolta deve conoscere il cloud di Azure a cui si prevede di eseguire la migrazione.
    - Selezionare Azure per enti pubblici, se si prevede di eseguire la migrazione al cloud di Azure per enti pubblici.
    - Selezionare Azure globale, se si prevede di eseguire la migrazione al cloud commerciale di Azure.
    - In base al cloud specificato qui, l'appliance invia i metadati individuati ai rispettivi punti finali.
- **Controllo della connessione Internet**: Agente di raccolta può connettersi a Internet direttamente o tramite un proxy.
    - Il controllo dei prerequisiti verifica la connettività a [URL necessari e facoltativi](#urls-for-connectivity).
    - Se si dispone di una connessione diretta a Internet, non è necessaria alcuna azione specifica, ma si deve solo verificare che Agente di raccolta possa accedere agli URL necessari.
    - Se ci si connette tramite un proxy, tenere presenti i [requisiti indicati di seguito](#connect-via-a-proxy).
- **Verificare sincronizzazione ora**: Agente di raccolta deve essere sincronizzato con il server di riferimento ora Internet per assicurarsi che le richieste al servizio siano autenticate.
    - Perché sia possibile convalidare l'orario, Agente di raccolta deve poter raggiungere l'URL portal.azure.com.
    - Se il computer non è sincronizzato, è necessario modificare l'ora dell'orologio della macchina virtuale di Agente di raccolta in modo che corrisponda all'ora corrente. A tale scopo, aprire una finestra del prompt dei comandi dell'amministratore sulla macchina virtuale e quindi eseguire **w32tm /tz** per verificare il fuso orario. Eseguire **w32tm /resync** per sincronizzare l'ora.
- **Verificare che il servizio Agente di raccolta sia in esecuzione**:  il servizio Agente di raccolta di Azure Migrate deve essere in esecuzione nella macchina virtuale di Agente di raccolta.
    - Questo servizio viene avviato automaticamente all'avvio del computer.
    - Se il servizio non è in esecuzione, avviarlo dal Pannello di controllo.
    - Il servizio Agente di raccolta si connette al server vCenter, raccoglie informazioni sui metadati e sulle prestazioni della macchina virtuale e le invia al servizio Azure Migrate.
- **Verificare che VMware PowerCLI 6.5 sia installato**: il modulo VMware PowerCLI 6.5 PowerShell deve essere installato nella macchina virtuale di Agente di raccolta in modo che possa comunicare con il server vCenter.
    - Se Agente di raccolta può accedere agli URL necessari per installare il modulo, quest'ultimo viene installato automaticamente durante la distribuzione di Agente di raccolta.
    - Se Agente di raccolta non è in grado di installare il modulo durante la distribuzione, è necessario [installarlo manualmente](#install-vwware-powercli-module-manually).
- **Verificare la connessione al server vCenter**: Agente di raccolta deve essere in grado di connettersi al server vCenter e di eseguire query in relazione a macchine virtuali, metadati e contatori delle prestazioni. [Verificare i requisiti](#connect-to-vcenter-server) per la connessione.


### <a name="connect-to-the-internet-via-a-proxy"></a>Connettersi a Internet tramite un proxy

- Se il server proxy richiede l'autenticazione, è possibile specificare il nome utente e password quando si configura Agente di raccolta.
- L'indirizzo IP o il nome FQDN del server proxy deve essere specificato come *http://IPaddress* o *http://FQDN*.
- È supportato solo il proxy HTTP. I server proxy basati su HTTPS non sono supportati da Agente di raccolta.
- Se il server proxy è un proxy di intercettazione, è necessario importare il certificato proxy nella macchina virtuale di Agente di raccolta.
    1. Nella macchina virtuale di Agente di raccolta andare al **menu Start** > **Gestisci i certificati computer**.
    2. Nell'area **Certificati - Computer locale** dello strumento Certificati trovare **Autori attendibili** > **Certificati**.

        ![Strumento Certificati](./media/concepts-intercepting-proxy/certificates-tool.png)

    3. Copiare il certificato proxy per la macchina virtuale di Agente di raccolta. È possibile ottenerlo dall'amministratore di rete.
    4. Fare doppio clic per aprire il certificato e quindi fare clic su **Installa certificato**.
    5. Nell'Importazione guidata certificati scegliere **Computer locale** come percorso archivio.

    ![Percorso dell'archivio certificati](./media/concepts-intercepting-proxy/certificate-store-location.png)

    6. Selezionare **Place all certificates in the following store (Colloca tutti i certificati nell'archivio seguente)** > **Sfoglia** > **Autori attendibili**. Fare clic su **Fine** per importare il certificato.

    ![Archivio certificati](./media/concepts-intercepting-proxy/certificate-store.png)

    7. Verificare che l'importazione del certificato e il controllo dei prerequisiti vengano eseguiti nel modo previsto.




### <a name="urls-for-connectivity"></a>URL per la connettività

La verifica della connettività viene eseguita tramite la connessione a un elenco di URL.

**URL** | **Dettagli**  | **Controllo dei prerequisiti**
--- | --- | ---
*.portal.azure.com | Applicabile ad Azure Globale. Viene controllata la connettività al servizio Azure e l'ora di sincronizzazione. | Accesso agli URL necessari.<br/><br/> Il controllo dei prerequisiti ha esito negativo se non è disponibile alcuna connettività.
*.portal.azure.us | Applicabile solo ad Azure per enti pubblici. Viene controllata la connettività al servizio Azure e l'ora di sincronizzazione. | Accesso agli URL necessari.<br/><br/> Il controllo dei prerequisiti ha esito negativo se non è disponibile alcuna connettività.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Usato per scaricare il modulo PowerShell vCenter PowerCLI. | Accesso agli URL facoltativi.<br/><br/> Il controllo dei prerequisiti non riesce.<br/><br/> L'installazione automatica del modulo nella macchina virtuale di Agente di raccolta non riesce. Installare manualmente il modulo.


### <a name="install-vmware-powercli-module-manually"></a>Installare manualmente il modulo VMware PowerCLI

1. Per installare il modulo, seguire [questa procedura](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). I passaggi descrivono sia l'installazione online che quella offline.
2. Se la macchina virtuale di Agente di raccolta è offline e il modulo deve essere installato in un altro computer con accesso a Internet, è necessario copiare i file VMware.* da tale computer nella macchina virtuale di Agente di raccolta.
3. Dopo l'installazione, è possibile riavviare i controlli dei prerequisiti per verificare che PowerCLI sia installato.

### <a name="connect-to-vcenter-server"></a>Connettersi al server vCenter

Agente di raccolta si connette al server vCenter ed esegue query in relazione ai metadati e ai contatori delle prestazioni della macchina virtuale. Di seguito vengono indicati gli aspetti da tenere presenti per la connessione.

- È supportato solo il server vCenter versioni 5.5, 6.0, 6.5 e 6.7.
- È necessario un account di sola lettura con le autorizzazioni elencate di seguito per l'individuazione. Per l'individuazione, è possibile accedere solo ai data center per cui è necessario l'account.
- Per impostazione predefinita la connessione al server vCenter viene eseguita con un indirizzo IP o un nome FQDN. Se il server vCenter è in ascolto su una porta diversa, connettersi al server usando la forma *IndirizzoIP:Numero_porta* o *FQDN:Numero_porta*.
- Agente di raccolta deve avere visibilità di rete sul server vCenter.

#### <a name="account-permissions"></a>Autorizzazioni dell'account

**Account** | **Autorizzazioni**
--- | ---
Almeno un account utente di sola lettura | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura)   


## <a name="collector-communications"></a>Comunicazioni di Agente di raccolta

Agente di raccolta comunica come riepilogato nel diagramma e nella tabella seguenti.

![Diagramma di comunicazione dell'agente di raccolta](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Componente con cui comunica Agente di raccolta** | **Porta** | **Dettagli**
--- | --- | ---
Servizio Azure Migrate | TCP 443 | Agente di raccolta dati comunica con il servizio Azure Migrate tramite SSL 443.
Server vCenter | TCP 443 | Agente di raccolta deve essere in grado di comunicare con il server vCenter.<br/><br/> Per impostazione predefinita, la connessione a vCenter viene eseguita tramite la porta 443.<br/><br/> Se il server vCenter è in ascolto su una porta diversa, la porta deve essere disponibile come porta in uscita in Agente di raccolta.
RDP | TCP 3389 |


## <a name="securing-the-collector-appliance"></a>Protezione dell'appliance Agente di raccolta

Per proteggere l'appliance Agente di raccolta, eseguire queste operazioni.

- Non condividere le password di amministratore con parti non autorizzate, né smarrirle.
- Arrestare l'appliance quando non è in uso.
- Collegare l'appliance in una rete protetta.
- Al termine della migrazione, eliminare l'istanza dell'appliance.
- Dopo migrazione, eliminare anche i file di backup dei dischi (VMDK) perché i dischi potrebbero contenere le credenziali di vCenter memorizzate nella cache.

## <a name="os-license-in-the-collector-vm"></a>Licenza del sistema operativo nella macchina virtuale di Agente di raccolta

Agente di raccolta include una licenza di valutazione di Windows Server 2012 R2 valida per 180 giorni. Se il periodo di valutazione della macchina virtuale di Agente di raccolta sta per scadere, è consigliabile scaricare una nuovo file OVA e creare una nuova appliance.

## <a name="updating-the-os-of-the-collector-vm"></a>Aggiornamento del sistema operativo della macchina virtuale di Agente di raccolta

Anche se l'appliance Agente di raccolta dispone di licenza di valutazione di 180 giorni, è necessario aggiornare costantemente il sistema operativo nell'appliance per evitare che quest'ultima si arresti automaticamente.

- Se Agente di raccolta non viene aggiornato per 60 giorni, avvia l'arresto automatico della macchina.
- Se è in esecuzione un'individuazione, è in esecuzione, la macchina non viene disattivata anche se sono trascorsi 60 giorni. La macchina viene disattivata dopo il completamento dell'individuazione.
- Se si usa l'agente di raccolta per più di 60 giorni, è consigliabile mantenere sempre aggiornato il computer eseguendo Windows Update.

## <a name="upgrading-the-collector-appliance-version"></a>Aggiornamento della versione dell'appliance Agente di raccolta

È possibile aggiornare Agente di raccolta alla versione più recente senza scaricare nuovamente il file OVA.

1. Scaricare il [pacchetto di aggiornamenti più recenti](concepts-collector-upgrade.md)
2. Per garantire la sicurezza dell'hotfix scaricato, aprire il prompt dei comandi come amministratore ed eseguire il comando seguente per generare l'hash del file ZIP. L'hash generato deve corrispondere con all'hash indicato nella versione specifica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (esempio d'utilizzo C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Copiare il file ZIP nella macchina virtuale dell'agente di raccolta di Azure Migrate (Agente di raccolta di Azure Migrate).
4. Fare clic con il pulsante destro del mouse sul file ZIP e selezionare Estrai tutto.
5. Fare clic con il pulsante destro del mouse sul file Setup.ps1, selezionare Run with PowerShell (Esegui con PowerShell) e quindi seguire le istruzioni visualizzate sullo schermo per installare l'aggiornamento.

## <a name="discovery-process"></a>Processo di individuazione

Dopo aver configurato l'appliance, è possibile eseguire l'individuazione il cui funzionamento è indicato di seguito.

- L'individuazione viene eseguita per ambito. Vengono individuate tutte le macchine virtuali nel percorso di inventario vCenter specificato.
    - Viene impostato un ambito alla volta.
    - L'ambito può includere un numero massimo di 1500 macchine virtuali.
    - L'ambito può essere un data center, una cartella o un host ESXi.
- Dopo la connessione al server vCenter, connettersi specificando un progetto di migrazione per la raccolta.
- Le macchine virtuali vengono individuate e i dati sulle prestazioni e i metadati vengono inviati ad Azure. Queste azioni fanno parte di un processo di raccolta.
    - All'appliance Agente di raccolta viene assegnato un ID specifico mantenuto per un determinato computer per tutte le individuazioni.
    - A un processo di raccolta in esecuzione viene assegnato un ID di sessione specifico. L'ID viene modificato per ogni processo di raccolta e può essere usato per la risoluzione dei problemi.

### <a name="collected-metadata"></a>Metadati raccolti

Per ogni macchina virtuale, l'appliance Agente di raccolta individua i metadati di configurazione seguenti. I dati di configurazione per le macchine virtuali sono disponibili un'ora dopo l'avvio dell'individuazione.

- Nome visualizzato della macchina virtuale (nel server vCenter)
- Percorso di inventario della macchina virtuale (host/cartella nel server vCenter)
- Indirizzo IP
- Indirizzo MAC
- Sistema operativo
- Numero di core, dischi, schede di interfaccia di rete
- Dimensione della memoria, dimensioni dei dischi
- Contatori delle prestazioni della macchina virtuale, del disco e della rete.

#### <a name="performance-counters"></a>Contatori delle prestazioni

 L'appliance Agente di raccolta raccoglie i contatori delle prestazioni seguenti per ogni macchina virtuale dall'host ESXi a intervalli di 20 secondi. Questi contatori sono i contatori di vCenter e, anche se la terminologia afferma Media, i campioni raccolti ogni 20 secondi sono di fatto contatori real-time. I dati sulle prestazioni per le macchine virtuali sono disponibili nel portale a partire da due ore dopo l'avvio dell'individuazione. Per ottenere elementi consigliati affidabili relativi alle dimensioni, è consigliabile attendere almeno un giorno prima di creare valutazioni basate sulle prestazioni. Per risultati immediati, è possibile creare valutazioni con un criterio di determinazione delle dimensioni *come in locale*, che non considera i dati sulle prestazioni per il dimensionamento corretto.

**Contatore** |  **Impatto sulla valutazione**
--- | ---
cpu.usage.average | Dimensione e costi consigliati della macchina virtuale  
mem.usage.average | Dimensione e costi consigliati della macchina virtuale  
virtualDisk.read.average | Calcola le dimensioni del disco, i costi di archiviazione e le dimensioni della macchina virtuale
virtualDisk.write.average | Calcola le dimensioni del disco, i costi di archiviazione e le dimensioni della macchina virtuale
virtualDisk.numberReadAveraged.average | Calcola le dimensioni del disco, i costi di archiviazione e le dimensioni della macchina virtuale
virtualDisk.numberWriteAveraged.average | Calcola le dimensioni del disco, i costi di archiviazione e le dimensioni della macchina virtuale
net.received.average | Calcola le dimensioni della macchina virtuale                          
net.transmitted.average | Calcola le dimensioni della macchina virtuale     

## <a name="next-steps"></a>Passaggi successivi

[Individuare e valutare le macchine virtuali VMware locali per la migrazione ad Azure](tutorial-assessment-vmware.md)
