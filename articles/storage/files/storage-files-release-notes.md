---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Leggere le note sulla versione per l'agente di Sincronizzazione file di Azure, che consente di centralizzare le condivisioni file dell'organizzazione in File di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 11/5/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: e60c23ce07969a2c1f031e1981970ceffad1864e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330275"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
Sono supportate le seguenti versioni di Sincronizzazione file di Azure Agent:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Versione 11.1- [KB4539951](https://support.microsoft.com/en-us/help/4539951)| 11.1.0.0 | 4 novembre 2020 | Supporto per il volo |
| Versione 10.1- [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 5 giugno 2020 | Supportato |
| Aggiornamento cumulativo di maggio 2020 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maggio 2020 | Supportato |
| Versione 10 - [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 9 aprile 2020 | Supportato |
| Aggiornamento cumulativo di dicembre 2019 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 dicembre 2019 | Supportato-la versione dell'agente scadrà il 16 febbraio 2021 |
| Versione 9 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 dicembre 2019 | Supportato-la versione dell'agente scadrà il 16 febbraio 2021 |
| Versione 8 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 ottobre 2019 | Supportato-la versione dell'agente scadrà il 12 gennaio 2021 |

## <a name="unsupported-versions"></a>Versioni non supportate
Le seguenti versioni di Sincronizzazione file di Azure Agent sono scadute e non sono più supportate:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Versione v7 | 7.0.0.0-7.2.0.0 | N/D | Non supportato-le versioni dell'agente sono scadute il 1 ° settembre 2020 |
| Versione 6 | 6.0.0.0 - 6.3.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 21 aprile 2020 |
| Versione 5 | 5.0.2.0 - 5.2.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 18 marzo 2020 |
| Versione v4 | 4.0.1.0 - 4.3.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 6 novembre 2019 |
| Versione 3 | 3.1.0.0 - 3.4.0.0 | N/D | Non supportato: la versione dell'agente è scaduta il 19 agosto 2019 |
| Agenti pre-disponibilità generale | 1.1.0.0 - 3.0.13.0 | N/D | Non supportato: versione dell'agente scaduta il 1° ottobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11100"></a>Versione dell'agente 11.1.0.0
Le note sulla versione seguenti sono relative alla versione 11.1.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 4 novembre 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti
- Nuove modalità di suddivisione in livelli nel cloud per controllare il download iniziale e il richiamo proattivo
    - Modalità di download iniziale: ora è possibile scegliere come si desidera che i file vengano scaricati inizialmente nel nuovo endpoint server. Si desidera che tutti i file siano archiviati a livelli o il maggior numero possibile di file scaricati nel server dal timestamp dell'Ultima modifica? Questa operazione può essere eseguita. Non è possibile usare la suddivisione in livelli nel cloud? È ora possibile scegliere di evitare i file a livelli nel sistema. Per ulteriori informazioni, vedere la sezione [creare un endpoint server](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal%2Cproactive-portal#create-a-server-endpoint) nella documentazione relativa alla distribuzione di sincronizzazione file di Azure.
    - Modalità di richiamo proattivo: ogni volta che un file viene creato o modificato, è possibile richiamarlo in modo proattivo nei server specificati nello stesso gruppo di sincronizzazione. In questo modo il file sarà immediatamente disponibile per l'utilizzo in ogni server specificato. I team in tutto il mondo lavorano sugli stessi dati? Abilitare il richiamo proattivo, in modo che quando il team arriva al mattino successivo, tutti i file aggiornati da un team in un fuso orario diverso vengono scaricati e pronti per l'uso. Per altre informazioni, vedere [richiamare in modo proattivo i file nuovi e modificati da una condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal%2Cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) nella documentazione relativa alla distribuzione di sincronizzazione file di Azure.

- Escludi le applicazioni dal rilevamento dell'ora dell'ultimo accesso al cloud. è ora possibile escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso. Quando un'applicazione accede a un file, l'ora dell'ultimo accesso per il file viene aggiornata nel database di suddivisione in livelli cloud. Le applicazioni che analizzano il file system come anti-virus fanno sì che tutti i file abbiano la stessa ora dell'ultimo accesso, che influisca sul momento in cui i file sono a livelli.

    Per escludere le applicazioni dal rilevamento dell'ora dell'ultimo accesso, aggiungere il nome del processo all'impostazione del registro di sistema HeatTrackingProcessNameExclusionList che si trova in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Esempio: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > I processi di deduplicazione dati e file server Gestione risorse (FSRM) sono esclusi per impostazione predefinita (hardcoded) e l'elenco di esclusione del processo viene aggiornato ogni 5 minuti.

- Miglioramenti vari delle prestazioni e dell'affidabilità
    - Miglioramento delle prestazioni di rilevamento delle modifiche per rilevare i file che sono stati modificati nella condivisione file di Azure.
    - Miglioramento delle prestazioni di caricamento della sincronizzazione.
    - Il caricamento iniziale viene ora eseguito da uno snapshot VSS che riduce gli errori per singolo elemento e gli errori della sessione di sincronizzazione.
    - Miglioramenti dell'affidabilità di sincronizzazione per determinati modelli di I/O.
    - Correzione di un bug per impedire al database di sincronizzazione di tornare indietro nel tempo nei cluster di failover quando si verifica un failover.
    - Miglioramento delle prestazioni di richiamo durante l'accesso a un file a livelli.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria. Per altre informazioni, vedere [Risorse di sistema consigliate](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#recommended-system-resources).
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
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
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection), in modo da avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- Il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in un gruppo di risorse, una sottoscrizione o un tenant di Azure AD diversi. Dopo lo spostamento dell'account di archiviazione o del servizio di sincronizzazione archiviazione, è necessario concedere all'applicazione Microsoft. StorageSync l'accesso all'account di archiviazione (vedere [verificare che sincronizzazione file di Azure abbia accesso all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-10100"></a>Versione dell'agente 10.1.0.0
Le note sulla versione seguenti sono relative alla versione 10.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 5 giugno 2020. Queste note si aggiungono alle note sulla versione elencate per la versione 10.0.0.0 e 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per endpoint privati di Azure
    - È ora possibile inviare il traffico di sincronizzazione al servizio di sincronizzazione archiviazione a un endpoint privato. Questo consente il tunneling su una connessione VPN o ExpressRoute. Per altre informazioni, vedere [Configuring sincronizzazione file di Azure Network Endpoints](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints).
- Metrica file sincronizzati ora visualizzerà lo stato di avanzamento mentre è in esecuzione una sincronizzazione di grandi dimensioni, anziché alla fine.
- Vari miglioramenti dell'affidabilità per l'installazione dell'agente, la suddivisione in livelli cloud, la sincronizzazione e la telemetria

## <a name="agent-version-10020"></a>Versione dell'agente 10.0.2.0
Le note sulla versione seguenti si riferiscono alla versione 10.0.2.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 19 maggio 2020). Queste note si aggiungono a quelle elencate per la versione 10.0.0.0.

Problema risolto in questa versione:  
- L'agente di sincronizzazione archiviazione (FileSyncSvc) si interrompe di frequente dopo l'installazione dell'agente Sincronizzazione file di Azure versione 10.

> [!Note]  
>Questa versione non è stata distribuita in anteprima ai server configurati per l'aggiornamento automatico quando è stata resa disponibile una nuova versione. Per installare questo aggiornamento, usare Microsoft Update o Microsoft Update Catalog. Per istruzioni di installazione, vedere [KB4522412](https://support.microsoft.com/help/4522412).

## <a name="agent-version-10000"></a>Versione dell'agente 10.0.0.0
Le note sulla versione seguenti si riferiscono alla versione 10.0.0.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 9 aprile 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Miglioramento dello stato di sincronizzazione nel portale
    - Con la versione 10 dell'agente, il portale di Azure inizierà a visualizzare il tipo di sessione di sincronizzazione in esecuzione: ad esempio download iniziale, download normale, richiamo in background (casi di ripristino di emergenza rapido) e simili. 

- Miglioramento dell'esperienza del portale del cloud a livelli
    - Se si dispone di file che non riescono a eseguire la suddivisione in livelli o il richiamo, è ora possibile visualizzare gli errori di suddivisione in livelli nelle proprietà dell'endpoint server.
    - Per un endpoint server sono disponibili informazioni aggiuntive sul cloud a livelli:
        - Dimensione cache locale
        - Efficienza di utilizzo della cache
        - Dettagli dei criteri del cloud a livelli: dimensioni del volume, spazio disponibile corrente o data e ora dell'ultimo accesso del file meno recente nella cache locale.
    - Queste modifiche verranno trasferite nel portale di Azure subito dopo il rilascio della versione 10 dell'agente iniziale.

- Supporto per lo trasferimento del servizio di sincronizzazione archiviazione e/o dell'account di archiviazione in un tenant di Azure Active Directory diverso
    - Sincronizzazione file di Azure supporta ora lo spostamento del servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse, in una sottoscrizione o in un tenant di Azure AD differente.
    
- Miglioramenti vari delle prestazioni e dell'affidabilità
    - Il rilevamento delle modifiche nella condivisione file di Azure potrebbe non riuscire se le regole della rete virtuale (VNET) e del firewall sono configurate nell'account di archiviazione.
    - Consumo di memoria ridotto associato al richiamo. 
    - Miglioramento delle prestazioni quando si usa il cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).
    - Altri miglioramenti vari dell'affidabilità. 
    
### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
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
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection), in modo da avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- Il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in un gruppo di risorse, una sottoscrizione o un tenant di Azure AD diversi. Dopo lo spostamento dell'account di archiviazione o del servizio di sincronizzazione archiviazione, è necessario concedere all'applicazione Microsoft. StorageSync l'accesso all'account di archiviazione (vedere [verificare che sincronizzazione file di Azure abbia accesso all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant di Azure AD. Dopo aver creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant di Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-9100"></a>Versione dell'agente 9.1.0.0
Le note sulla versione seguenti si riferiscono alla versione 9.1.0.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 12 dicembre 2019). Queste note si aggiungono a quelle elencate per la versione 9.0.0.0.

Problema risolto in questa versione:  
- La sincronizzazione ha esito negativo e si verifica uno degli errori seguenti dopo l'aggiornamento alla versione dell'agente di Sincronizzazione file di Azure 9.0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Versione dell'agente 9.0.0.0
Le note sulla versione seguenti si riferiscono alla versione 9.0.0.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 2 dicembre 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per il ripristino self-service
    - Gli utenti possono ora ripristinare i file usando la funzionalità della versione precedente. Prima della versione 9, la funzionalità della versione precedente non era supportata nei volumi con abilitazione per il cloud a livelli. Questa funzionalità deve essere abilitata separatamente per ogni volume in cui è presente un endpoint con abilitazione per cloud a livelli. Per altre informazioni, vedere  
[Ripristino self-service tramite versioni precedenti e VSS (Servizio Copia Shadow del volume)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Supporto per dimensioni di condivisione file più grandi 
    - Sincronizzazione file di Azure supporta ora fino a 64TiB e 100 milioni di file in un singolo spazio dei nomi di sincronizzazione.  
 
- Supporto della deduplicazione dei dati in Windows Server 2019 
    - La deduplicazione dei dati è supportata con cloud a livelli abilitato in Windows Server 2019. Per supportare la deduplicazione dei dati nei volumi con cloud a livelli, è necessario installare Windows Update [KB4520062](https://support.microsoft.com/help/4520062). 
 
- Miglioramento della dimensione minima per un file da suddividere in livelli 
    - La dimensione minima per un file da suddividere in livelli si basa sulle dimensioni del cluster del file system (raddoppia le dimensioni del cluster del file system). Per impostazione predefinita, ad esempio, la dimensione del cluster del file system NTFS è 4 KB, la dimensione minima risultante per un file da suddividere in livelli è 8 KB. 
 
- Cmdlet per il test di connettività di rete 
    - Come parte della configurazione di Sincronizzazione file di Azure, è necessario contattare più endpoint di servizio. Ognuno ha il proprio nome DNS che deve essere accessibile al server. Questi URL sono specifici anche dell'area in cui il server è registrato. Dopo la registrazione del server, è possibile usare il cmdlet per il test di connettività (PowerShell e utilità di registrazione del server) per testare le comunicazioni con tutti gli URL specifici del server. Questo cmdlet consente di risolvere i problemi quando la comunicazione incompleta impedisce al server di funzionare correttamente con Sincronizzazione file di Azure e può essere usato per ottimizzare le configurazioni del proxy e del firewall.  
 
        Per avviare il test di connettività di rete, eseguire i comandi di PowerShell seguenti: 
 
        Import-Module "C:\Programmi\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Rimuovere il miglioramento dell'endpoint server quando è abilitata la funzionalità del cloud a livelli 
    - Come prima, la rimozione di un endpoint server non comporta la rimozione di file nella condivisione file di Azure. Il comportamento per i punti di analisi nel server locale è, tuttavia, stato modificato. I punti di analisi (puntatori a file che non sono locali sul server) vengono ora eliminati quando si rimuove un endpoint server. I file completamente memorizzati nella cache rimarranno sul server. Questo miglioramento è stato fatto per impedire la generazione di [file a livelli orfani](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) quando si rimuove un endpoint server. Se l'endpoint server viene ricreato, i punti di analisi per i file a livelli verranno ricreati nel server.  
 
- Miglioramenti dell'affidabilità e delle prestazioni 
    - Riduzione degli errori di richiamo. La dimensione di richiamo viene ora regolata automaticamente in base alla larghezza di banda della rete. 
    - Miglioramento delle prestazioni di download quando si aggiunge un nuovo server a un gruppo di sincronizzazione. 
    - Riduzione dei file non sincronizzati a causa di conflitti di vincoli. 
    - La suddivisione dei file in livelli ha esito negativo oppure i file vengono richiamati in modo imprevisto in determinati scenari se il percorso dell'endpoint server è un punto di montaggio del volume.
    
### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

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
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili. Per altre informazioni, vedere [I file a livelli non sono accessibili sul server dopo l'eliminazione di un endpoint server](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection), in modo da avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Se il file pagefile.sys si trova in un volume in cui è abilitata la funzionalità cloud a livelli, potrebbe non essere possibile suddividere i file in livelli. Il file pagefile.sys deve trovarsi in un volume in cui la funzionalità cloud a livelli è disabilitata.

## <a name="agent-version-8000"></a>Versione dell'agente 8.0.0.0
Le note sulla versione seguenti si riferiscono alla versione 8.0.0.0 dell'agente di Sincronizzazione file di Azure (data di rilascio 8 ottobre 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Miglioramenti delle prestazioni di ripristino
    - Tempi di ripristino più rapidi per il ripristino eseguito tramite Backup di Azure. I file ripristinati verranno nuovamente sincronizzati con i server di Sincronizzazione file di Azure in modo molto più rapido. 
- Miglioramento dell'esperienza del portale del cloud a livelli  
    - Se sono presenti file a livelli che non possono essere richiamati, è ora possibile visualizzare gli errori di richiamo nelle proprietà dell'endpoint server. Inoltre, l'integrità dell'endpoint server visualizza ora un errore e i passaggi di mitigazione se il driver del filtro per il cloud a livelli non è caricato nel server.
- Installazione dell'agente semplificata
    - Il modulo Az\AzureRM di PowerShell non è più necessario per registrare il server rendendo più semplice e veloce l'installazione.
- Miglioramenti vari delle prestazioni e dell'affidabilità

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

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
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili. Per altre informazioni, vedere [I file a livelli non sono accessibili sul server dopo l'eliminazione di un endpoint server](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection), in modo da avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
