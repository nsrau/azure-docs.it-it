---
title: Ripristino da una grave perdita dei dati in Azure Stack tramite il servizio Backup di infrastruttura | Microsoft Docs
description: Quando un errore irreversibile fa in modo che Azure Stack per avere esito negativo, è possibile ripristinare i dati dell'infrastruttura quando che venga ristabilita la distribuzione di Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 326fd38f7cb738540a389c93de2a9b5be015e0d7
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968733"
---
# <a name="recover-from-catastrophic-data-loss"></a>Ripristino da una grave perdita dei dati

*Si applica a: i sistemi integrati di Azure Stack.*

Azure Stack viene eseguito Servizi di Azure nel tuo Data Center. Azure Stack eseguibili in ambienti piccoli fino a quattro nodi installati in un singolo rack. Al contrario, Azure è in esecuzione in più di 40 aree in più Data Center e più zone in ogni area. Le risorse utente possono estendersi su più server, rack, i Data Center e aree. Con Azure Stack, si dispone solo la possibilità di distribuire un singolo rack dell'intero cloud. Ciò espone il cloud per il rischio di eventi catastrofici presso il proprio Data Center o errori causati da bug del prodotto principale. Quando si verifica un'emergenza, l'istanza di Azure Stack viene portata offline. Tutti i dati è potenzialmente irreversibile.

A seconda della causa radice della perdita dei dati, potrebbe essere necessario ripristinare un singola infrastruttura servizio o ripristinare l'intera istanza di Azure Stack. Si potrebbe essere necessario anche ripristinare un hardware diverso nella stessa posizione o in una posizione diversa.

Indirizzi in questo scenario ripristinare l'intera installazione in caso di errori delle apparecchiature e la ridistribuzione del cloud privato.

| Scenario                                                           | Perdita di dati                            | Considerazioni                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Ripristino da una grave perdita dei dati a causa di un bug emergenza o del prodotto | Tutti i dati dell'infrastruttura e di utenti e app | Applicazione utente e i dati sono protetti separatamente dai dati dell'infrastruttura |

## <a name="workflows"></a>Flussi di lavoro

Il passaggio di protezione di Azure avviare inizia con il backup dei dati di infrastruttura e applicazione/tenant separatamente. Questo documento illustra come proteggere l'infrastruttura. 

![Distribuzione iniziale di Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow1.png)

Nel peggiore dei casi in cui tutti i dati viene perso, il ripristino di Azure Stack è il processo di ripristino dei dati di infrastruttura univoci alla distribuzione di Azure Stack e tutti i dati utente. 

![Ridistribuire Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restore

Se si verifica una perdita irreversibile dei dati, ma l'hardware è ancora utilizzabile, è necessaria la ridistribuzione di Azure Stack. Durante la ridistribuzione, è possibile specificare il percorso di archiviazione e le credenziali necessarie per accedere ai backup. In questa modalità, non è necessario specificare i servizi che devono essere ripristinati. Controller di infrastruttura di Backup viene inserito lo stato del piano di controllo come parte del flusso di lavoro di distribuzione.

Se si verifica una calamità che rende inutilizzabile l'hardware, è possibile su hardware nuovo solo la ridistribuzione. La ridistribuzione può richiedere diverse settimane sostituzione hardware viene ordinato e arriva nel Data Center. Ripristino dei dati di piano di controllo è possibile in qualsiasi momento. Tuttavia, il ripristino non è supportato se la versione dell'istanza ridistribuito è più di una versione superiore a quella usata nell'ultimo backup. 

| Modalità di distribuzione | Punto di partenza | Punto finale                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Installazione pulita   | Compilazione della linea di base | OEM consente di distribuire Azure Stack e aggiorna la versione supportata più recente.                                                                                                                                          |
| Modalità di ripristino   | Compilazione della linea di base | OEM consente di distribuire Azure Stack in modalità di ripristino e gestisce la versione corrispondente di requisiti in base al backup più recente disponibile. L'OEM viene completata la distribuzione mediante l'aggiornamento alla versione supportata più recente. |

## <a name="data-in-backups"></a>Dati di backup

Azure Stack supporta un tipo di distribuzione chiamato modalità di ripristino cloud. Questa modalità viene utilizzata solo se si sceglie di ripristinare Azure Stack dopo un'emergenza o bug del prodotto viene eseguito il rendering della soluzione non ripristinabile. Questa modalità di distribuzione non viene ripristinato i dati utente archiviati nella soluzione. L'ambito di questa modalità di distribuzione è limitato al ripristino di dati seguenti:

 - Input per la distribuzione
 - Sistemi delle identità interni
 - Federated identificare la configurazione (distribuzioni disconnesse)
 - Certificati radice usati da autorità di certificazione interna
 - Azure Resource Manager configurazione utente dei dati, ad esempio sottoscrizioni, i piani, offerte e quote per l'archiviazione, rete e le risorse di calcolo
 - Gli insiemi di credenziali e i segreti di Key Vault
 - Le assegnazioni dei criteri RBAC e le assegnazioni di ruolo 

Nessuno dell'infrastruttura distribuita come servizio (IaaS) di una piattaforma o di un utente con un servizio (PaaS) alle risorse vengono recuperati durante la distribuzione. Vale a dire le macchine virtuali IaaS, gli account di archiviazione, BLOB, tabelle, configurazione di rete e così via, vengono persi. Lo scopo di ripristino cloud consiste nell'assicurarsi che gli operatori e gli utenti possono accedere al portale dopo la distribuzione è stata completata. Riaccedendo non sarà possibile visualizzare tutte le risorse. Gli utenti dispongono di abbonamenti ottenuti ripristinati e di insieme che originale piani e offre criteri definiti dall'amministratore. Gli utenti che accedono al sistema viene eseguito con gli stessi vincoli imposti dalla soluzione originale prima dell'emergenza. Al termine del ripristino di cloud, l'operatore è possibile ripristinare manualmente a valore aggiunto e RPs di terze parti e dati associati.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle procedure consigliate per [tramite il servizio di Backup Infrastructure](azure-stack-backup-best-practices.md).
