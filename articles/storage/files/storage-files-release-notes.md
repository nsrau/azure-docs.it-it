---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Note sulla versione dell'agente Sincronizzazione file di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 1/14/2019
ms.author: wgries
ms.component: files
ms.openlocfilehash: 006a8172faae529ce9943773552da325bfae3f4b
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321535"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
L'agente Sincronizzazione file di Azure supporta le versioni seguenti:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Status |
|----|----------------------|--------------|------------------|
| Gennaio 2019 aggiornamento cumulativo - [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 gennaio 2019 | Supportato (versione consigliata) |
| Aggiornamento cumulativo di dicembre 2018 - [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 dicembre 2018 | Supportato |
| Aggiornamento cumulativo di dicembre 2018 | 4.1.0.0 | 4 dicembre 2018 | Supportato |
| Versione v4 | 4.0.1.0 | 13 novembre 2018 | Supportato |
| Aggiornamento cumulativo di settembre 2018 | 3.3.0.0 | 24 settembre 2018 | Supportato |
| Aggiornamento cumulativo di agosto 2018 | 3.2.0.0 | 15 agosto 2018 | Supportato |
| Disponibilità generale | 3.1.0.0 | 19 Luglio 2018 | Supportato |
| Agenti scaduti | 1.1.0.0 - 3.0.13.0 | N/D | Non supportato: versione dell'agente scaduta il 1° ottobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-4300"></a>Agente versione 4.3.0.0
Le note seguenti sulla versione si riferiscono alla versione 4.3.0.0 dell'agente Sincronizzazione file di Azure con data di rilascio il 14 luglio 2019. Queste note si aggiungono a quelle elencate per la versione 4.0.1.0.

Elenco dei problemi risolti in questa versione:  
- I file non vengono suddivisi in livelli dopo l'aggiornamento dell'agente Sincronizzazione file di Azure alla versione 4.x.
- AfsUpdater.exe è ora supportato in Windows Server 2019.
- Vari miglioramenti dell'affidabilità per la sincronizzazione. 

## <a name="agent-version-4200"></a>Agente versione 4.2.0.0
Le note sulla versione seguenti si riferiscono alla versione 4.2.0.0 dell'agente Sincronizzazione file di Azure rilasciata il 10 dicembre 2018. Queste note si aggiungono a quelle elencate per la versione 4.0.1.0.

Elenco dei problemi risolti in questa versione:  
- Quando si crea uno snapshot VSS, può verificarsi un errore irreversibile 0x3B o 0x1E.  
- Potrebbe verificarsi una perdita di memoria cloud quando è abilitato il cloud a livelli  

## <a name="agent-version-4100"></a>Agente versione 4.1.0.0
Le note sulla versione seguenti si riferiscono alla versione 4.1.0.0 dell'agente Sincronizzazione file di Azure rilasciata il 4 dicembre 2018. Queste note si aggiungono a quelle elencate per la versione 4.0.1.0.

Elenco dei problemi risolti in questa versione:  
- Il server potrebbe smettere di rispondere a causa di una perdita di memoria del cloud a livelli.  
- L'installazione dell'agente ha esito negativo con l'errore seguente: Errore 1921. Impossibile arrestare il servizio 'Agente di sincronizzazione archiviazione' (FileSyncSvc).  Verificare di disporre di privilegi sufficienti per l'arresto dei servizi di sistema.  
- Potrebbe verificarsi un arresto anomalo del servizio Agente di sincronizzazione archiviazione (FileSyncSvc) quando l'utilizzo della memoria è elevato.  
- Vari miglioramenti dell'affidabilità per cloud a livelli e sincronizzazione.

## <a name="agent-version-4010"></a>Agente versione 4.0.1.0
Le note sulla versione seguenti si riferiscono alla versione 4.0.1.0 dell'agente Sincronizzazione file di Azure rilasciata il 13 novembre 2018.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.
- Quando si crea uno snapshot VSS, può verificarsi un errore irreversibile 0x3B o 0x1E.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato. Per aggiornare il nome del server nel portale, annullare la registrazione e registrare nuovamente il server.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- L'impostazione dei i criteri di suddivisione in livelli nel cloud in base alla data viene usata per specificare i file che devono essere memorizzati nella cache se aperti in un numero specificato di giorni. Per altre informazioni, vedere [Suddivisione in livelli cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.

## <a name="agent-version-3300"></a>Agente versione 3.3.0.0
Le note seguenti si riferiscono alla versione 3.3.0.0 dell'agente Sincronizzazione file di Azure rilasciata il 24 settembre 2018. Queste note si aggiungono a quelle elencate per la versione 3.1.0.0.

Elenco dei problemi risolti in questa versione:
- Dopo che l'agente Sincronizzazione file di Azure è stato aggiornato alla versione 3.1 o 3.2, lo stato del server registrato è "Risulta offline".
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) si arresta in modo anomalo a causa di file con percorsi lunghi.
- La registrazione del server non riesce con l'errore: Impossibile caricare il file o l'assembly Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Agente versione 3.2.0.0
Le note seguenti sulla versione si riferiscono alla versione 3.2.0.0 dell'agente Sincronizzazione file di Azure rilasciata il 15 agosto 2018. Queste note si aggiungono a quelle elencate per la versione 3.1.0.0.

Questa versione include la correzione seguente:
- La sincronizzazione ha esito negativo con un errore di memoria insufficiente (0x8007000e) a causa della perdita di memoria

## <a name="agent-version-3100"></a>Agente versione 3.1.0.0
Le note seguenti sulla versione si riferiscono alla versione 3.1.0.0 dell'agente Sincronizzazione file di Azure (data di rilascio 19 luglio 2018).

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GB di memoria fisica.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Non usare Gestione risorse file server o altri screening dei file. Gli screening dei file possono generare errori di sincronizzazione infinita se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'installazione dell'agente e la registrazione del server devono avvenire dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.
- La deduplicazione dei dati e la suddivisione in livelli cloud non sono supportate nello stesso volume.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inutilizzabili se non vengono richiamati prima di eliminare l'endpoint server.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare il file di paging di un sistema operativo o di un'applicazione in un endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato. Per aggiornare il nome del server nel portale, annullare la registrazione e registrare nuovamente il server.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
