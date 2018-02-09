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
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Risolvere i problemi relativi a System Center Data Protection Manager

Le note sulla versione più recenti per System Center Data Protection Manager sono disponibili [qui](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016).
La matrice di supporto della protezione è disponibile [qui](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="replica-is-inconsistent"></a>La replica è incoerente

Questo errore può verificarsi per diversi motivi, ad esempio un processo di creazione della replica non riuscito, problemi relativi al journal delle modifiche, errori del file della mappa di bit del filtro a livello di volume, un arresto imprevisto del computer di origine, un overflow del log di sincronizzazione o una replica completamente incoerente. Per risolvere il problema, seguire questa procedura:
- Per rimuovere lo stato incoerente, eseguire la verifica della coerenza manualmente o pianificare una verifica della coerenza giornaliera.
- Assicurarsi di usare la versione più recente del server MAB o di System Center DPM
- Assicurarsi che la verifica automatica della coerenza sia abilitata
- Provare a riavviare i servizi dal prompt dei comandi ("net stop dpmra" seguito da "net start dpmra")
- Assicurarsi che siano soddisfatti i requisiti di connettività e larghezza di banda di rete
- Controllare se il computer di origine si è arrestato in modo imprevisto
- Verificare che il disco sia integro e che vi sia spazio sufficiente per la replica
- Assicurarsi che non vengano eseguiti contemporaneamente processi di backup duplicati

## <a name="online-recovery-point-creation-failed"></a>Creazione del punto di ripristino online non riuscita

Per risolvere il problema, seguire questa procedura:
- Assicurarsi di usare l'ultima versione dell'agente di Backup di Azure
- Provare a creare manualmente un punto di recupero nell'area dell'attività di protezione
- Assicurarsi di eseguire la verifica della coerenza nell'origine dati
- Assicurarsi che siano soddisfatti i requisiti di connettività e larghezza di banda di rete
- I dati di replica hanno uno stato incoerente. Creare un punto di recupero del disco per questa origine dati
- Verificare che la replica sia presente e non sia mancante
- Verificare che la replica abbia spazio sufficiente per creare il journal USN

## <a name="unable-to-configure-protection"></a>Impossibile configurare la protezione

Questo errore viene visualizzato quando il server DPM non riesce a contattare il server protetto. Per risolvere il problema, seguire questa procedura:
- Assicurarsi di usare l'ultima versione dell'agente di Backup di Azure
- Assicurarsi che vi sia connettività (rete/firewall/proxy) tra il server DPM e il server protetto
- Se si protegge SQL Server, assicurarsi che per NT AUTHORITY\SYSTEM sia abilitato sysadmin in Proprietà account di accesso

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>Questo server non è registrato nell'insieme di credenziali specificato dalla credenziale dell'insieme di credenziali

Questo errore viene visualizzato quando il file delle credenziali dell'insieme di credenziali selezionato non appartiene a un insieme di credenziali di Servizi di ripristino associato al server di Backup di Azure/System Center DPM in cui viene tentato il ripristino. Per risolvere il problema, seguire questa procedura:
- Scaricare il file delle credenziali dall'insieme di credenziali di Servizi di ripristino in cui è registrato il server di Backup di Azure/System Center DPM.
- Provare a registrare il server con l'insieme di credenziali usando il file delle credenziali dell'insieme di credenziali scaricato più recente.

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>I dati ripristinabili non sono disponibili o il server selezionato non è un server DPM
Questo errore viene visualizzato quando non sono presenti altri server di Backup di Azure/System Center DPM registrati nell'insieme di credenziali di Servizi di ripristino, i server non hanno ancora caricato i metadati oppure il server selezionato non è un server di Backup di Azure/System Center DPM.
- Se sono presenti altri server di Backup di Azure/System Center DPM registrati nell'insieme di credenziali di Servizi di ripristino, assicurarsi che sia installato l'agente di Backup di Azure più recente.
- Se sono presenti altri server di Backup di Azure/System Center DPM registrati nell'insieme di credenziali di Servizi di ripristino, attendere un giorno dopo l'installazione prima di avviare il processo di ripristino. I processi notturni caricano i metadati per tutti i backup protetti nel cloud. I dati sono disponibili per il ripristino.

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>La passphrase di crittografia fornita non corrisponde alla passphrase associata al server seguente

> [!NOTE]
>Se la passphrase di crittografia è stata dimenticata o smarrita, non è disponibile alcuna opzione per ripristinare i dati. L'unica opzione consiste nel rigenerare la passphrase e usarla per crittografare i dati di backup futuri.
>
>

Questo errore viene visualizzato quando la passphrase di crittografia usata nel processo di crittografia dei dati in fase di ripristino dal server di Backup di Azure/System Center DPM non corrisponde alla passphrase di crittografia specificata. L'agente non è in grado di decrittografare i dati. Di conseguenza il ripristino non riesce. Per risolvere il problema, seguire questa procedura:
- Specificare esattamente la stessa passphrase di crittografia associata al server di Backup di Azure/System Center DPM i cui dati sono in fase di ripristino. 
