---
title: Recupero dalla perdita di dati irreversibile nello Stack di Azure tramite il servizio di Backup infrastruttura | Documenti Microsoft
description: "Quando un errore irreversibile determina dello Stack Azure hanno esito negativo, è possibile ripristinare i dati di infrastruttura durante la distribuzione di Azure Stack ristabilita."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: mabrigg
ms.openlocfilehash: 141641b01b338e3426861dad7424a1de1bd2c63c
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="recover-from-catastrophic-data-loss"></a>Recupero dalla perdita di dati

*Si applica a: Azure Stack integrati sistemi.*

Stack di Azure esegue i servizi di Azure nel Data Center. Stack di Azure è possibile eseguire in ambienti piccoli fino a quattro nodi installati in un singolo rack. Al contrario, Azure viene eseguito in più di 40 aree in più Data Center e più aree in ogni area. Risorse dell'utente possono estendersi su più server, rack, Data Center e le aree. Con lo Stack di Azure, si dispone solo la scelta di distribuire un singolo rack dell'intero cloud. Espone il cloud al rischio di eventi catastrofici presso il proprio Data Center o errori causati da bug del prodotto principale. Quando un'eventuale emergenza, l'istanza di Azure Stack passa alla modalità offline. Tutti i dati è potenzialmente irreversibile.

A seconda della causa radice di perdita di dati, è necessario ripristinare un servizio di infrastruttura singolo o ripristinare l'intera istanza dello Stack di Azure. Potrebbe anche essere necessario ripristinare su hardware diverso nella stessa posizione o in un percorso diverso.

Indirizzi questo scenario il ripristino di tutta l'installazione in caso di errore delle apparecchiature e la ridistribuzione del cloud privato.

| Scenario                                                           | Perdita di dati                            | Considerazioni                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Recupero dalla perdita di dati a causa di bug del prodotto o di emergenza | Tutti i dati dell'infrastruttura e l'utente e app | Applicazione utente e i dati sono protetti separatamente dai dati di infrastruttura |

## <a name="workflows"></a>Flussi di lavoro

Il proprio processo di protezione di Azure avvia inizia con separatamente il backup dell'infrastruttura e tenant app i dati. Questo documento descrive come proteggere l'infrastruttura. 

![Distribuzione iniziale dello Stack di Azure](media\azure-stack-backup\azure-stack-backup-workflow1.png)

Nel peggiore dei casi in cui tutti i dati viene perso, il ripristino di Azure Stack è il processo di ripristino dei dati di infrastruttura univoci in tale distribuzione dello Stack di Azure e tutti i dati utente. 

![Ridistribuire Azure Stack](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restore

Se si verifica una perdita di dati, ma l'hardware è ancora utilizzabile, è necessario ridistribuire dello Stack di Azure. Durante la ridistribuzione, è possibile specificare il percorso di archiviazione e le credenziali necessarie per accedere ai backup. In questa modalità, non è necessario specificare i servizi che devono essere ripristinati. Controller di infrastruttura di Backup inserisce lo stato del piano di controllo come parte del flusso di lavoro di distribuzione.

In caso di emergenza che rende inutilizzabile l'hardware, è possibile su hardware nuovo solo la ridistribuzione. La ridistribuzione può richiedere alcune settimane, mentre i componenti hardware sostitutivi verrà ordinati e arriva nel Data Center. Ripristino dei dati di controllo piano è possibile in qualsiasi momento. Tuttavia, ripristino non è supportato se la versione dell'istanza ridistribuito più di una versione superiore alla versione utilizzata con l'ultimo backup. 

| Modalità di distribuzione | Punto di partenza | Punto finale                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Installazione pulita   | Compilazione di base | OEM distribuisce Azure Stack e aggiorna alla versione più recente supportata.                                                                                                                                          |
| Modalità di ripristino   | Compilazione di base | OEM distribuisce Azure Stack in modalità di ripristino e gestisce la versione corrispondente di requisiti in base all'ultimo backup disponibile. OEM completa la distribuzione di aggiornamento alla versione più recente. |

## <a name="data-in-backups"></a>Dati di backup

Stack di Azure supporta un tipo di distribuzione denominato modalità di ripristino cloud. Questa modalità viene utilizzata solo se si sceglie di ripristinare Stack Azure dopo un'emergenza o dei bug del prodotto della soluzione non ripristinabile. Questa modalità di distribuzione non viene ripristinato i dati utente archiviati nella soluzione. L'ambito di questa modalità di distribuzione è limitato al ripristino di dati seguenti:

 - Input di distribuzione
 - Sistemi di identità interne
 - Federati identificare configurazione (distribuzioni disconnesse)
 - Certificati radice utilizzati da autorità di certificazione interna
 - Azure dati di gestione risorse configurazione utente, ad esempio le sottoscrizioni, dei piani, offerte e le quote per l'archiviazione, rete e le risorse di calcolo
 - Gli insiemi di credenziali e i segreti KeyVault
 - Le assegnazioni dei criteri sui ruoli e le assegnazioni di ruolo 

Nessuno dell'infrastruttura come servizio (IaaS) o piattaforma utente come una risorsa di servizio (PaaS) vengono ripristinati durante la distribuzione. Vale a dire le macchine virtuali IaaS, gli account di archiviazione, BLOB, tabelle, la configurazione di rete e così via, vengono persi. Lo scopo di ripristino cloud consiste nel verificare che gli operatori e gli utenti possono accedere di nuovo il portale dopo la distribuzione è stata completata. Gli utenti che accedono in non verranno visualizzato le proprie risorse. Gli utenti dispongono le relative sottoscrizioni ripristinati e che originale piani e offre criteri definiti dall'amministratore. Gli utenti che accedono al sistema viene eseguito con gli stessi vincoli imposti dalla soluzione originale prima dell'emergenza. Al termine del ripristino di cloud, l'operatore è possibile ripristinare manualmente a valore aggiunto e RPs di terze parti e i dati associati.

## <a name="next-steps"></a>Passaggi successivi

 - Informazioni sulle procedure consigliate per [tramite il servizio di Backup di infrastruttura](azure-stack-backup-best-practices.md).
