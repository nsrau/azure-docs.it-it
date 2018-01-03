---
title: Riferimento al servizio di Backup di Stack dell'infrastruttura Azure | Documenti Microsoft
description: Questo articolo contiene materiale di riferimento per il servizio di Backup di Azure dello Stack dell'infrastruttura.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 4e6e0a52b2c55239e38757223f54e5e94dc98c42
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="infrastructure-backup-service-reference"></a>Riferimento al servizio di Backup di infrastruttura

## <a name="azure-backup-infrastructure"></a>Infrastruttura di backup di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Azure Stack è costituito da numerosi servizi che costituiscono il portale di gestione risorse di Azure, e l'esperienza di gestione dell'infrastruttura. L'esperienza di Gestione accessorio dello Stack di Azure viene illustrata la riduzione della complessità esposta all'operatore della soluzione.

Backup di infrastruttura è progettato per acquisire la complessità del backup e ripristino dei dati per i servizi di infrastruttura, assicurando operatori può concentrarsi sulla soluzione di gestione e manutenzione di un contratto di servizio agli utenti.

Esportazione dei dati di backup in una condivisione esterna è necessario evitare di archiviare backup nello stesso sistema. Richiedere una condivisione esterna consente all'amministratore la possibilità di determinare la posizione in cui archiviare i dati in base ai criteri di BC/ripristino di emergenza aziendale esistenti. 

### <a name="infrastructure-backup-components"></a>Componenti dell'infrastruttura di Backup

Backup di infrastruttura include i componenti seguenti:

 - **Controller di infrastruttura Backup**  
 Il Controller di infrastruttura di Backup viene creata un'istanza con e si trova in ogni Stack del Cloud di Azure.
 - **Provider di risorse di backup**  
 Il Provider di risorse di Backup (Backup RP) è costituito da applicazione e interfaccia programma interfacce (API) gli utenti esposizione di funzionalità di backup di base per l'infrastruttura di Azure Stack.

#### <a name="infrastructure-backup-controller"></a>Controller di infrastruttura Backup

Il Controller di infrastruttura di Backup è un'infrastruttura di servizio viene creata un'istanza di servizio per un Cloud di Azure dello Stack. Risorse di backup vengono create in un livello e acquisizione specifiche del servizio dati internazionali da Gestione risorse di Active Directory, CA, Azure, CRP, criteri di restrizione software, NRP, KeyVault, sui ruoli. 

### <a name="backup-resource-provider"></a>Provider di risorse di backup

Il Provider di risorse di Backup presenta l'interfaccia utente nel portale di Azure Stack per la configurazione di base e l'elenco delle risorse di backup. Operatore può eseguire le operazioni seguenti nell'interfaccia utente:

 - Abilitare il backup per la prima volta, fornendo il percorso di archiviazione esterna, credenziali e chiave di crittografia
 - Visualizzazione completato creato alle risorse di backup e lo stato creazione in corso
 - Modificare il percorso di archiviazione in cui Controller Backup inserisce i dati di backup
 - Modificare le credenziali utilizzate per accedere al percorso di archiviazione esterna Controller di Backup
 - Modificare la chiave di crittografia che utilizza il Controller di Backup per crittografare i backup 


## <a name="backup-controller-requirements"></a>Requisiti del Controller di backup

In questa sezione descrive i requisiti importanti per il Backup di infrastruttura. È consigliabile rivedere attentamente le informazioni prima di abilitare il backup per l'istanza dello Stack di Azure e quindi farvi riferimento nuovamente come necessarie durante la distribuzione e l'operazione successiva.

I requisiti includono:

  - **Requisiti software** -descrive i percorsi di archiviazione supportati e indicazioni relative al dimensionamento. 
  - **Requisiti di rete** -descrive i requisiti di rete per i percorsi di archiviazione diversi.  

### <a name="software-requirements"></a>Requisiti software

#### <a name="supported-storage-locations"></a>Percorsi di archiviazione supportati

| Posizione di archiviazione                                                                 | Dettagli                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Condivisione file SMB ospitata in un dispositivo di archiviazione all'interno dell'ambiente di rete attendibile | Condivisione SMB dello stesso Data Center in cui è distribuito Azure Stack o in un altro Data Center. Più istanze dello Stack di Azure è possono utilizzare la stessa condivisione file. |
| Condivisione file SMB in Azure                                                          | Attualmente non è supportata.                                                                                                                                 |
| Archiviazione BLOB di Azure                                                            | Attualmente non è supportata.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versioni supportate di SMB

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Ridimensionamento di percorso di archiviazione 

Controller di infrastruttura di Backup verrà eseguito il backup dei dati su richiesta. Consiglia di eseguire il backup per l'ultima due volte un giorno e vengono mantenuti al massimo di sette giorni di backup. 

| Scala di ambiente | Dimensioni previste del backup | Quantità totale di spazio necessaria |
|-------------------|--------------------------|--------------------------------|
| nodi 4-12        | 10 GB                     | 140 GB                          |

### <a name="network-requirements"></a>Requisiti di rete
| Posizione di archiviazione                                                                 | Dettagli                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Condivisione file SMB ospitata in un dispositivo di archiviazione all'interno dell'ambiente di rete attendibile | La porta 445 è necessaria se l'istanza di Azure Stack si trova in un ambiente protetto da firewall. Tramite la porta 445, Controller di infrastruttura di Backup verrà avviata una connessione al file server SMB. |
| Per usare il FQDN del server di file, il nome deve essere risolto dal PEP             |                                                                                                                                                                                         |

> [!Note]  
> È necessario aprire alcuna porta in ingresso.


## <a name="infrastructure-backup-limits"></a>Limiti di Backup di infrastruttura

È importante considerare questi limiti si pianificare, distribuire e utilizzare le istanze di Microsoft Azure Stack. Nella tabella seguente vengono descritti questi limiti.

### <a name="infrastructure-backup-limits"></a>Limiti di Backup di infrastruttura
| Identificatore limite                                                 | Limite        | Commenti                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Tipo di backup                                                      | Solo full    | Controller di infrastruttura di Backup supporta solo backup completi. I backup incrementali non sono supportati.                                          |
| Backup pianificati                                                | Solo manuale  | Controller di backup attualmente supporta solo backup su richiesta                                                                                 |
| Numero massimi processi di backup simultanei                                   | 1            | Un solo processo di backup attivo è supportato per ogni istanza del Controller di Backup.                                                                  |
| Configurazione di commutatore di rete                                     | Non nell'ambito | Amministratore deve eseguire il backup configurazione di commutatore di rete utilizzando strumenti OEM. Consultare la documentazione per lo Stack di Azure da ogni fornitore OEM. |
| Hardware del ciclo di vita Host                                          | Non nell'ambito | Amministratore deve eseguire il backup dell'Hardware del ciclo di vita Host utilizzando gli strumenti OEM. Consultare la documentazione per lo Stack di Azure da ogni fornitore OEM.      |
| Numero massimo di condivisioni di file                                    | 1            | Solo una condivisione di file può essere utilizzata per archiviare dati di backup                                                                                        |
| Backup di servizi di App, funzione, SQL, dati del provider di risorse mysql | Non nell'ambito | Fare riferimento alle linee guida pubblicata per la distribuzione e la gestione a valore aggiunto RPs creato da Microsoft.                                                  |
| Provider di risorse di terze parti backup                              | Non nell'ambito | Fare riferimento alle linee guida pubblicata per la distribuzione e la gestione a valore aggiunto RPs create dai fornitori di terze parti.                                          |

## <a name="next-steps"></a>Passaggi successivi

 - Per ulteriori informazioni sul servizio di Backup di infrastruttura, vedere [Backup e ripristino dei dati per lo Stack di Azure con il servizio di Backup di infrastruttura](azure-stack-backup-infrastructure-backup.md).