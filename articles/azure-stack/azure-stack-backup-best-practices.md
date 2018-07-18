---
title: Infrastruttura del servizio Backup le procedure consigliate per Azure Stack | Microsoft Docs
description: È possibile seguire le procedure consigliate quando si distribuisce e Gestisci Azure Stack nel tuo Data Center per aiutare a ridurre la perdita di dati se si verifica un errore irreversibile.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 06a2d4ab12d2a7e03a538a98f5232a417fb39e4f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969470"
---
# <a name="infrastructure-backup-service-best-practices"></a>Procedure consigliate per il servizio Backup di infrastruttura

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile seguire le procedure consigliate quando si distribuisce e Gestisci Azure Stack nel tuo Data Center per aiutare a ridurre la perdita di dati si verifica un errore irreversibile.

È necessario esaminare le procedure consigliate a intervalli regolari per verificare che l'installazione è ancora conforme quando vengono apportate modifiche al flusso di operazione. Dovrebbero si verificano problemi durante l'implementazione di queste procedure consigliate, contattare il supporto tecnico Microsoft per assistenza.

## <a name="configuration-best-practices"></a>Procedure consigliate per la configurazione

### <a name="deployment"></a>Distribuzione

Abilitare il Backup dell'infrastruttura dopo la distribuzione di ogni Stack di Cloud di Azure. Con AzureStack-strumenti, è possibile pianificare i backup da qualsiasi client/server con accesso all'endpoint dell'API di gestione di operatore.

### <a name="networking"></a>Rete

La stringa di Universal Naming Convention (UNC) per il percorso deve usare un nome di dominio completo (FQDN). Indirizzo IP può verificarsi se la risoluzione dei nomi non è possibile. Una stringa UNC specifica il percorso delle risorse, ad esempio i file condivisi o i dispositivi.

### <a name="encryption"></a>Crittografia

La chiave di crittografia viene utilizzata per crittografare i dati di backup viene esportato in un archivio esterno. La chiave viene generata come parte della [abilitazione del backup per Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).

La chiave deve essere archiviata in un luogo sicuro (ad esempio, public Azure segreto di Key Vault). Questa chiave deve essere usata durante la ridistribuzione di Azure Stack. 

![Archiviata la chiave di una posizione sicura.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Procedure operative consigliate

### <a name="backups"></a>Backup

 - Controller di infrastruttura di Backup deve essere attivato su richiesta. Si consiglia di backup almeno due volte al giorno.
 - I processi di backup vengono eseguiti mentre il sistema è in esecuzione in modo che nessun tempo di inattività per le esperienze di gestione o le applicazioni utente. Prevedere i processi di backup 20-40 minuti per una soluzione che si trova sotto carico ragionevole.
 - Usa istruzioni OEM, commutatori di rete di eseguire manualmente il backup e l'host del ciclo di vita dell'hardware (HLH) devono essere archiviati nella condivisione di backup stesso in cui il controllo di archivi di Controller di infrastruttura Backup del piano dati di backup. È consigliabile archiviare commutatore e le configurazioni HLH nella cartella area. Se si dispone di più istanze di Azure Stack nella stessa area, prendere in considerazione utilizzando un identificatore per ogni configurazione a cui appartiene un'unità di scala.

### <a name="folder-names"></a>Nomi delle cartelle

 - Infrastruttura Crea cartella MASBACKUP automaticamente. Si tratta di una condivisione gestita da Microsoft. È possibile creare condivisioni allo stesso livello MASBACKUP. Non è consigliabile creare le cartelle o i dati di archiviazione all'interno di MASBACKUP che Azure Stack non crea. 
 -  Utente FQDN e area nel nome della cartella per differenziare i dati di backup dal cloud diversi. Il nome di dominio completo (FQDN) di distribuzione di Azure Stack e gli endpoint è la combinazione del parametro di area e il parametro di nome di dominio esterno. Per altre informazioni, vedere [integrazione di Data Center di Azure Stack - DNS](azure-stack-integrate-dns.md).

Ad esempio, la condivisione di backup è ospitato in fileserver01.contoso.com AzSBackups. In tale condivisione file può essere una cartella per ogni distribuzione di Azure Stack usando il nome di dominio esterno e una sottocartella che usa il nome dell'area. 

Nome di dominio completo: contoso.com  
Area: città di New York


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Cartella MASBackup è dove dello Stack di Azure archivia i dati di backup. È non devono utilizzare questa cartella per archiviare i propri dati. OEM non devono utilizzare questa cartella per archiviare sia i dati di backup. 

Gli OEM sono invitati a memorizzare i dati di backup per i componenti all'interno della cartella area. Ogni switch di rete, l'host del ciclo di vita dell'hardware (HLH) e così via possono essere archiviati nella propria sottocartella. Ad esempio: 

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Monitoraggio

Gli avvisi seguenti sono supportati dal sistema:

| Avviso                                                   | DESCRIZIONE                                                                                     | Correzione                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Backup non è riuscito perché la condivisione file è esaurisce la capacità | Condivisione file si esaurisce la capacità e controller di backup non è possibile esportare i file di backup nel percorso. | Aggiungere ulteriore capacità di archiviazione e ripetere l'operazione di backup. Eliminare i backup esistenti (a partire dal meno recente prima di tutto) per liberare spazio.                    |
| Backup non è riuscito a causa di problemi di connettività.             | Rete tra Azure Stack e il file share sta riscontrando problemi.                          | Risolvere il problema di rete e ripetere il backup.                                                                                            |
| Non è riuscito a causa di un errore nel percorso di backup                | Il percorso della condivisione file non può essere risolto                                                          | Connettere la condivisione da un altro computer per assicurarsi che la condivisione è accessibile. Si potrebbe essere necessario aggiornare il percorso, se non è più valido.       |
| Backup non è riuscito a causa di un problema di autenticazione               | Potrebbe esserci un problema con le credenziali o un problema di rete che influisce sull'autenticazione.    | Connettere la condivisione da un altro computer per assicurarsi che la condivisione è accessibile. Si potrebbe essere necessario aggiornare le credenziali se non sono più validi. |
| Backup non è riuscito a causa di un errore generale                    | La richiesta non riuscita potrebbe essere dovuto a un problema intermittente. Provare nuovamente a eseguire il backup.                    | Contattare il supporto tecnico                                                                                                                               |

## <a name="next-steps"></a>Passaggi successivi

 - Esaminare il materiale di riferimento per la [Infrastructure Backup Service](azure-stack-backup-reference.md).  
 - Abilitare la [servizio di Backup Infrastructure](azure-stack-backup-enable-backup-console.md).
