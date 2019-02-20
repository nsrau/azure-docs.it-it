---
title: Riferimento al servizio di Backup di Stack dell'infrastruttura Azure | Microsoft Docs
description: Questo articolo contiene materiale di riferimento per il servizio Backup di Azure Stack dell'infrastruttura.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: a7930ea86f7972a6e4abb939fb148d519ca924e9
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416718"
---
# <a name="infrastructure-backup-service-reference"></a>Riferimento al servizio Backup di infrastruttura

## <a name="azure-backup-infrastructure"></a>Infrastruttura di backup di Azure

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Esperienza di gestione dell'infrastruttura e Azure Stack è costituito da molti servizi che costituiscono il portale di Azure Resource Manager. L'esperienza di gestione simile a appliance di Azure Stack è incentrato sulla riduzione della complessità esposta all'operatore della soluzione.

Backup dell'infrastruttura è progettato per acquisire la complessità dell'esecuzione del backup e il ripristino dei dati per i servizi di infrastruttura, assicurando operatori possibile concentrarsi sulla soluzione di gestione e manutenzione di un contratto di servizio agli utenti.

Esportazione dei dati di backup in una condivisione esterna è necessario per evitare di archiviare backup nello stesso sistema. Richiedere una condivisione esterna consente all'amministratore la possibilità di determinare la posizione in cui archiviare i dati in base ai criteri di BC e ripristino di emergenza aziendale esistenti. 

### <a name="infrastructure-backup-components"></a>Componenti dell'infrastruttura di Backup

Backup dell'infrastruttura include i componenti seguenti:

 - **Controller di infrastruttura Backup**  
 Il Controller di infrastruttura di Backup viene creata un'istanza con e si trova in ogni Cloud di Azure Stack.
 - **Provider di risorse di backup**  
 Provider di risorse di Backup (Backup RP) è costituito da interfaccia e applicazione programma interfacce (API) gli utenti che espone funzionalità di backup di base per l'infrastruttura di Azure Stack.

#### <a name="infrastructure-backup-controller"></a>Controller di infrastruttura Backup

Il Controller di infrastruttura di Backup è un servizio viene creata l'istanza per un Cloud di Azure Stack di Service Fabric. Risorse di backup vengono create a un livello e acquisizione specifiche del servizio dati internazionali da autorità di certificazione, AD Azure Resource Manager, CRP, criteri di restrizione software, NRP, Key Vault, RBAC. 

### <a name="backup-resource-provider"></a>Provider di risorse di backup

Il Provider di risorse Backup presenta l'interfaccia utente nel portale di Azure Stack per la configurazione di base e l'elenco delle risorse di backup. Operatore può eseguire le operazioni seguenti nell'interfaccia utente:

 - Abilitare il backup per la prima volta, fornendo il percorso di archiviazione esterna, credenziali e chiave di crittografia
 - Creare vista completata alle risorse di backup e lo stato creazione in corso
 - Modificare il percorso di archiviazione in cui Backup Controller inserisce i dati di backup
 - Modificare le credenziali usate dal Controller di Backup per accedere al percorso di archiviazione esterna
 - Modificare la chiave di crittografia usate dal Controller di Backup per crittografare i backup 


## <a name="backup-controller-requirements"></a>Requisiti del Controller di backup

In questa sezione vengono descritti i requisiti importanti per il Backup dell'infrastruttura. È consigliabile esaminare attentamente le informazioni prima di abilitare il backup per l'istanza di Azure Stack, quindi fare riferimento ad esso in base alle esigenze durante la distribuzione e il funzionamento successivo.

I requisiti includono:

  - **Requisiti software** – descrive indicazioni relative al dimensionamento e posizioni di archiviazione supportate. 
  - **Requisiti di rete** : vengono descritti i requisiti di rete per i percorsi di archiviazione diverso.  

### <a name="software-requirements"></a>Requisiti software

#### <a name="supported-storage-locations"></a>Posizioni di archiviazione supportate

| Posizione di archiviazione                                                                 | Dettagli                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Condivisione file SMB ospitata in un dispositivo di archiviazione all'interno dell'ambiente di rete attendibile | Condivisione SMB dello stesso Data Center in cui viene distribuito Azure Stack o in un altro Data Center. Più istanze di Azure Stack è possono usare la stessa condivisione file. |
| Condivisione file SMB in Azure                                                          | Attualmente non è supportata.                                                                                                                                 |
| Archiviazione BLOB di Azure                                                            | Attualmente non è supportata.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versioni supportate di SMB

| SMB | Versione |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Ridimensionamento di percorso di archiviazione 

Controller di infrastruttura di Backup eseguirà il backup dei dati su richiesta. Consiglia di eseguire il backup per l'ultima due volte un giorno e mantenere al massimo di sette giorni di backup. 

**1811 e oltre**
| Scalabilità di ambiente | Dimensioni previste del backup | Quantità totale di spazio necessaria |
|-------------------|--------------------------|--------------------------------|
| 4-16 nodi        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**Pre-1811**
| Scalabilità di ambiente | Dimensioni previste del backup | Quantità totale di spazio necessaria |
|-------------------|--------------------------|--------------------------------|
| 4-16 nodi, ASDK  | 10 GB                     | 140 GB                        |

### <a name="network-requirements"></a>Requisiti di rete
| Posizione di archiviazione                                                                 | Dettagli                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Condivisione file SMB ospitata in un dispositivo di archiviazione all'interno dell'ambiente di rete attendibile | La porta 445 è obbligatorio se l'istanza di Azure Stack si trova in un ambiente protetto da firewall. Controller di infrastruttura di Backup verrà avviata una connessione al file server SMB sulla porta 445. |
| Per usare il FQDN del server di file, il nome deve essere risolvibile di PEP             |                                                                                                                                                                                         |

> [!Note]  
> Nessuna porta in ingresso deve essere aperta.

### <a name="encryption-requirements"></a>Requisiti di crittografia

A partire da 1901, il servizio backup di infrastruttura userà un certificato con una chiave pubblica (. Area a esecuzione vincolata) per crittografare i dati di backup e un certificato con la chiave privata (. File PFX) per la decrittografia dei dati di backup durante il ripristino di cloud.   
 - Il certificato viene usato per il trasporto delle chiavi e non viene usato per stabilire una comunicazione sicura autenticata. Per questo motivo il certificato può essere un certificato autofirmato. Azure Stack non è necessario verificare radice o relazione di trust per questo certificato in modo che l'accesso a internet esterno non è necessaria.
 
Il certificato autofirmato è disponibile in due parti, una con la chiave pubblica e una con la chiave privata:
 - Crittografare i dati di backup: Certificato con la chiave pubblica (esportata. File CER) viene usato per crittografare i dati di backup
 - Decrittografare i dati di backup: Certificato con la chiave privata (esportata. File PFX) viene usato per decrittografare i dati di backup

Il certificato con la chiave pubblica (. Area a esecuzione vincolata) non è gestito da rotazione segreta interna. Per ruotare il certificato, è necessario creare un nuovo certificato autofirmato e aggiornare le impostazioni di backup con il nuovo file (. AREA A ESECUZIONE VINCOLATA).  
 - Tutti i backup esistenti rimangono crittografati usando la chiave pubblica precedente. I nuovi backup userà la nuova chiave pubblica. 
 
Il certificato usato durante il ripristino di cloud con la chiave privata (. Per Azure Stack, PFX) non viene mantenuto per motivi di sicurezza. Questo file dovranno essere specificati in modo esplicito durante il ripristino di cloud.  

**Con le versioni precedenti la modalità di compatibilità** partire 1901, supporto di chiavi di crittografia è deprecato e verrà rimossa in una versione futura. Se è stato aggiornato da 1811 con backup già abilitato con una chiave di crittografia, Azure Stack continuerà a usare la chiave di crittografia. Modalità di compatibilità con le versioni precedenti sarà supportata per versione almeno 3. Dopo tale periodo, verrà richiesto un certificato. 
 * L'aggiornamento dalla chiave di crittografia al certificato è un'operazione irreversibile.  
 * Tutti i backup esistenti rimarranno crittografati usando la chiave di crittografia. I nuovi backup userà il certificato. 

## <a name="infrastructure-backup-limits"></a>Limiti relativi a Backup di infrastruttura

Tenere in considerazione questi limiti si pianificare, distribuire e utilizzare istanze di Microsoft Azure Stack. Nella tabella seguente vengono descritti questi limiti.

### <a name="infrastructure-backup-limits"></a>Limiti di Backup dell'infrastruttura
| Identificatore limite                                                 | Limite        | Commenti                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Tipo di backup                                                      | Solo full    | Controller di infrastruttura di Backup supporta solo backup completi. I backup incrementali non sono supportati.                                          |
| Backup pianificati                                                | Pianificati e manuali  | Il controller di backup supporta i backup pianificati e su richiesta                                                                                 |
| Numero massimo di processi simultaneo backup                                   | 1            | Per ogni istanza del Controller di Backup è supportato un solo processo di backup attivo.                                                                  |
| Configurazione di commutatore di rete                                     | Non nell'ambito | Amministratore deve eseguire il backup configurazione di commutatore di rete utilizzando strumenti OEM. Fare riferimento alla documentazione per Azure Stack fornite da ogni fornitore OEM. |
| Host del ciclo di vita dell'hardware                                          | Non nell'ambito | Amministratore deve eseguire il backup Host del ciclo di vita dell'Hardware utilizzando strumenti OEM. Fare riferimento alla documentazione per Azure Stack fornite da ogni fornitore OEM.      |
| Numero massimo di condivisioni file                                    | 1            | Solo una condivisione file può essere utilizzata per archiviare i dati di backup                                                                                        |
| Backup di servizi App, funzione, SQL, dati del provider di risorse mysql | Non nell'ambito | Fare riferimento alla Guida pubblicata per la distribuzione e la gestione a valore aggiunto RPs creato da Microsoft.                                                  |
| Provider di risorse di terze parti backup                              | Non nell'ambito | Fare riferimento alla Guida pubblicata per la distribuzione e la gestione a valore aggiunto RPs create da fornitori di terze parti.                                          |

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni sul servizio Backup di infrastruttura, vedere [Backup e ripristino dei dati per Azure Stack con il servizio di Backup Infrastructure](azure-stack-backup-infrastructure-backup.md).
