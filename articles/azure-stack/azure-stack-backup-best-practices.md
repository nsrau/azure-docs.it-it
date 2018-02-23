---
title: Infrastruttura del servizio di Backup le procedure consigliate per lo Stack di Azure | Documenti Microsoft
description: "È possibile seguire i set di procedure consigliate quando si distribuisce e gestire Azure Stack nel Data Center per ridurre la perdita di dati, se si verifica un errore irreversibile."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2017
ms.author: mabrigg
ms.openlocfilehash: b9438f3bab92f40a5c79ce7b7a572195c182be45
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="infrastructure-backup-service-best-practices"></a>Procedure consigliate per il servizio di Backup di infrastruttura

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile seguire le procedure consigliate quando si distribuisce e gestire Azure Stack nel Data Center per ridurre la perdita di dati in caso di errore irreversibile.

È necessario rivedere le procedure consigliate a intervalli regolari per verificare che l'installazione è ancora conforme quando vengono apportate modifiche al flusso di operazione. Devono si verificano problemi durante l'implementazione di queste procedure consigliate, contattare il supporto Microsoft per assistenza.

## <a name="configuration-best-practices"></a>Procedure consigliate per la configurazione

### <a name="deployment"></a>Distribuzione

Abilitare il Backup dell'infrastruttura dopo la distribuzione ogni Stack di Cloud di Azure. Utilizzo di AzureStack-strumenti è possibile pianificare i backup da qualsiasi client/server l'accesso all'endpoint dell'API di gestione di operatore.

### <a name="networking"></a>Rete

La stringa di Universal Naming Convention (UNC) per il percorso deve essere un nome di dominio completo (FQDN). Indirizzo IP è possibile se la risoluzione dei nomi non è possibile. Una stringa UNC specifica il percorso delle risorse, ad esempio i dispositivi o file condivisi.

### <a name="encryption"></a>Crittografia

La chiave di crittografia utilizzata per crittografare i dati di backup viene esportato in un archivio esterno. La chiave può essere generata utilizzando gli strumenti di AzureStack. 

![AzureStack-Tools](media\azure-stack-backup\azure-stack-backup-encryption1.png)

La chiave deve essere archiviata in una posizione sicura (ad esempio, segreto insieme credenziali chiavi Azure pubblica). Questa chiave deve essere utilizzata durante la ridistribuzione dello Stack di Azure. 

![Archiviata la chiave di una posizione sicura.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Procedure operative consigliate

### <a name="backups"></a>Backup

 - Controller di infrastruttura di Backup deve essere attivato su richiesta. Si consiglia di eseguire il backup per almeno due volte al giorno.
 - I processi di backup eseguito mentre il sistema è in esecuzione in modo nessun tempo di inattività per le esperienze di gestione o le applicazioni utente. Prevedere che i processi di backup di 20-40 minuti per una soluzione che si trova sotto carico ragionevole.
 - Utilizza OEM fornito (istruzione), commutatori di rete manualmente backup e l'host del ciclo di vita di hardware (HLH) devono essere archiviati nella condivisione di backup stesso piano di dati di backup in cui il controllo di archivi di Backup Controller di infrastruttura. È consigliabile archiviare commutatore e le configurazioni HLH nella cartella dell'area. Se si dispone di più istanze dello Stack di Azure nella stessa area, considerare l'utilizzo di un identificatore per ogni configurazione che appartiene a un'unità di scala.

### <a name="folder-names"></a>Nomi delle cartelle

 - L'infrastruttura Crea cartella MASBACKUP automaticamente. Si tratta di una condivisione gestita da Microsoft. È possibile creare condivisioni allo stesso livello MASBACKUP. Non è consigliabile la creazione di cartelle o i dati all'interno di MASBACKUP che non è possibile creare Azure Stack di archiviazione. 
 -  Utente FQDN e area nel nome della cartella per differenziare i dati di backup di cloud diversi. Il nome di dominio completo (FQDN) della distribuzione di Azure Stack e l'endpoint è la combinazione del parametro regione e il parametro di nome di dominio esterno. Per ulteriori informazioni, vedere [integrazione con Data Center di Azure Stack - DNS](azure-stack-integrate-dns.md).

Ad esempio, la condivisione di backup è ospitato in fileserver01.contoso.com AzSBackups. In tale condivisione di file può essere una cartella per la distribuzione di Azure Stack utilizzando il nome di dominio esterno e una sottocartella che usa il nome dell'area. 

FQDN: contoso.com  
Regione: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Cartella MASBackup è dove Stack Azure archivia i dati di backup. Non utilizzare questa cartella per archiviare i propri dati. OEM non deve utilizzare questa cartella per archiviare sia i dati di backup. 

Gli OEM sono sconsigliati per archiviare i dati di backup per i componenti all'interno della cartella dell'area. Ogni switch di rete, l'host del ciclo di vita di hardware (HLH) e così via possono essere archiviati in una sottocartella. Ad esempio: 

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Monitoraggio

Gli avvisi seguenti sono supportati dal sistema:

| Avviso                                                   | DESCRIZIONE                                                                                     | Correzione                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Non è riuscito perché la condivisione file ha esaurito la capacità di backup | Condivisione file ha esaurito la capacità e controller di backup non è possibile esportare i file di backup nel percorso. | Aggiungere ulteriori capacità di archiviazione e ripetere l'operazione di backup. Eliminare i backup esistenti (a partire dal meno recente prima di tutto) per liberare spazio.                    |
| Backup non è riuscito a causa di problemi di connettività.             | Rete tra Stack di Azure e il file condivisione si è verificati problemi.                          | Risolvere il problema di rete e riprovare a eseguire il backup.                                                                                            |
| Non è riuscito a causa di un errore nel percorso di backup                | Il percorso della condivisione file non può essere risolto                                                          | Connettere la condivisione da un altro computer per verificare che la condivisione sia accessibile. Si potrebbe essere necessario aggiornare il percorso, se non è più valido.       |
| Backup non è riuscito a causa di un problema di autenticazione               | Potrebbe esserci un problema con le credenziali o un problema di rete che influisce sull'autenticazione.    | Connettere la condivisione da un altro computer per verificare che la condivisione sia accessibile. Si potrebbe essere necessario aggiornare le credenziali se non sono più validi. |
| Backup non è riuscito a causa di un errore generale                    | La richiesta non riuscita potrebbe essere dovuto a un problema saltuario. Ripetere l'operazione di backup.                    | chiamare il supporto tecnico                                                                                                                               |

## <a name="next-steps"></a>Passaggi successivi

 - Esaminare il materiale di riferimento per il [servizio di Backup di infrastruttura](azure-stack-backup-reference.md).  
 - Abilitare il [servizio di Backup infrastruttura](azure-stack-backup-enable-backup-console.md).