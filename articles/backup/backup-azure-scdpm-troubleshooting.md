---
title: Risolvere i problemi relativi a System Center Data Protection Manager con Backup di Azure | Microsoft Docs
description: Risolvere i problemi in System Center Data Protection Manager.
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: bf4ea676c5309bb732f6a4ce71849606b4d2e4df
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Risolvere i problemi relativi a System Center Data Protection Manager

Questo articolo descrive alcune soluzioni ai problemi che potrebbero verificarsi durante l'uso di Data Protection Manager.

Per le ultime note sulla versione per System Center Data Protection Manager, vedere la [documentazione di System Center](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Altre informazioni sul supporto per Data Protection Manager sono disponibili in [questa matrice](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Errore: La replica è incoerente

Una replica può essere incoerente per i motivi seguenti:
- Il processo di creazione della replica non è riuscito.
- Ci sono problemi con il journal delle modifiche.
- La bitmap del filtro a livello di volume contiene errori.
- Il computer di origine si arresta in modo imprevisto.
- Nel log di sincronizzazione si verifica un overflow.
- La replica è realmente incoerente.

Per risolvere il problema, eseguire le azioni seguenti:
- Per rimuovere lo stato di incoerenza, eseguire la verifica della coerenza manualmente o pianificare una verifica della coerenza giornaliera.
- Assicurarsi di usare la versione più recente di Server di Backup di Microsoft Azure e Data Protection Manager.
- Verificare che l'impostazione **Coerenza automatica** sia abilitata.
- Provare a riavviare i servizi dal prompt dei comandi. Usare il comando `net stop dpmra` seguito da `net start dpmra`.
- Assicurarsi che siano soddisfatti i requisiti di connettività e larghezza di banda di rete.
- Controllare se il computer di origine si è arrestato in modo imprevisto.
- Verificare che il disco sia integro e che vi sia spazio sufficiente per la replica.
- Assicurarsi che non vengano eseguiti contemporaneamente processi di backup duplicati.

## <a name="error-online-recovery-point-creation-failed"></a>Errore: Creazione del punto di ripristino online non riuscita

Per risolvere il problema, eseguire le azioni seguenti:
- Accertarsi di usare l'ultima versione dell'agente di Backup di Azure.
- Provare a creare manualmente un punto di recupero nell'area dell'attività di protezione.
- Assicurarsi di eseguire la verifica della coerenza nell'origine dati.
- Assicurarsi che siano soddisfatti i requisiti di connettività e larghezza di banda di rete.
- Quando i dati di replica sono in uno stato incoerente, è possibile creare un punto di recupero del disco per questa origine dati.
- Verificare che la replica sia presente e non mancante.
- Verificare che la replica disponga di spazio sufficiente per creare il journal del numero di sequenza di aggiornamento (USN).

## <a name="error-unable-to-configure-protection"></a>Errore: Impossibile configurare la protezione

Questo errore si verifica quando il server di Data Protection Manager non riesce a contattare il server protetto. 

Per risolvere il problema, eseguire le azioni seguenti:
- Accertarsi di usare l'ultima versione dell'agente di Backup di Azure.
- Assicurarsi che vi sia connettività (rete/firewall/proxy) tra il server Data Protection Manager e il server protetto.
- Per proteggere un server SQL, assicurarsi che, nella voce **Proprietà di accesso** > **NT AUTHORITY\SYSTEM**, sia abilitata l'impostazione **sysadmin**.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Errore: Il server non è registrato come specificato nel file delle credenziali dell'insieme di credenziali

Questo errore si verifica nel processo di ripristino dei dati del server Data Protection Manager/Azure Backup. Il file delle credenziali dell'insieme di credenziali usato nel processo di ripristino non appartiene all'insieme di credenziali di Servizi di ripristino per il server di Data Protection Manager/Azure Backup.

Per risolvere il problema, seguire questa procedura:
1. Scaricare il file delle credenziali dall'insieme di credenziali di Servizi di ripristino in cui è registrato il server di Data Protection Manager/Azure Backup.
2. Provare a registrare il server con l'insieme di credenziali usando il file delle credenziali dell'insieme di credenziali scaricato più recentemente.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Errore: Nessun dato ripristinabile, oppure il server selezionato non è un server di Data Protection Manager

Questo errore si verifica per i motivi seguenti:
- Nessun altro server di Data Protection Manager/Azure Backup è registrato nell'insieme di credenziali di servizi di ripristino.
- I server non hanno ancora caricato i metadati.
- Il server selezionato non è un server di Data Protection Manager/Azure Backup.

Quando altri server di Data Protection Manager/Azure Backup sono registrati nell'insieme di credenziali di servizi di ripristino, eseguire i passaggi seguenti per risolvere il problema:
1. Assicurarsi che sia installato l'agente Backup di Azure più aggiornato.
2. Dopo avere verificato che sia installato l'agente più aggiornato, attendere un giorno prima di avviare il processo di ripristino. I processi di backup notturni caricano i metadati per tutti i backup protetti nel cloud. I dati di backup sono quindi disponibili per il ripristino.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Errore: Una passphrase di crittografia fornita non corrisponde alla passphrase per il server

Questo errore si verifica nel processo di crittografia durante il recupero di dati del server Data Protection Manager/Azure Backup. La passphrase di crittografia che viene usata nel processo di ripristino non corrisponde alla passphrase di crittografia del server. Di conseguenza, l'agente non può decrittografare i dati e il ripristino non riesce.

> [!IMPORTANT]
> Se si dimentica o si smarrisce la passphrase di crittografia, non esistono altri metodi per ripristinare i dati. L'unica opzione è quella di rigenerare la passphrase. Usare la nuova passphrase per crittografare i dati di backup futuri.
>
> Quando si esegue il ripristino dei dati, è necessario specificare sempre la stessa passphrase di crittografia associata con il server di Data Protection Manager/Azure Backup. 
>
