---
title: Log di Azure Stack e la gestione dei dati | Microsoft Docs
description: Informazioni su come Azure Stack raccoglie le informazioni.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 7beb9dc31e8987d69c2cf9cf64be36caf0e0e308
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56318938"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack log e dei clienti la gestione dei dati 
*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*  

Nella misura massima Microsoft è un processore o subprocessor dei dati personali in relazione a Azure Stack, Microsoft consente a tutti i clienti, efficaci 25 maggio 2018, gli impegni in (a) durante l'elaborazione"di dati personali. Effettuare il provisioning al GDPR"della sezione"Condizioni di protezione dati"del [condizioni per Online Services](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0) e (b) dell'Unione europea generale dati Protection Regulation termini 4 allegato del [condizioni per Online Services](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0). 

Come Azure Stack si trova nel Data Center dei clienti, Microsoft è il trattamento dei dati esclusivamente di dati condivisi con Microsoft tramite [Diagnostics](azure-stack-diagnostics.md), [telemetria](azure-stack-telemetry.md), e [difatturazione](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Controlli di accesso 
I dipendenti Microsoft, che vengono assegnati a esaminare un caso di supporto specifico, verranno concesso l'accesso di sola lettura ai dati crittografati. I dipendenti Microsoft possono anche accedere agli strumenti che può essere usato per eliminare i dati se necessario. Tutti gli accessi ai dati dei clienti sono controllato e registrato.  

I controlli di accesso ai dati:
1.  I dati viene mantenuti solo per un massimo di 90 giorni dopo la chiusura di case.
2.  Il cliente ha sempre la scelta in modo che i dati rimossi in qualsiasi momento nel periodo di 90 giorni.
3.  I dipendenti Microsoft vengono concesso l'accesso ai dati nel caso per caso e solo se necessario per risolvere il problema di supporto. 
4.  Nell'evento in cui Microsoft devono condividere i dati del cliente con i partner OEM, è obbligatorio il consenso dei clienti.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Ai clienti di eseguire controlli quali le richieste di soggetto dei dati (DSR) hanno?
Come accennato in precedenza, Microsoft supporta l'eliminazione dei dati su richiesta per ogni richiesta del cliente. I clienti possono richiedere che il tecnico del supporto eliminare tutti i log per un case specificato in qualsiasi momento della scelta del cliente, prima che i dati vengono cancellati in modo permanente.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Microsoft informare i clienti quando i dati vengono eliminati?
Per l'azione di eliminazione automatica dei dati (90 giorni dopo la chiusura caso), Microsoft non in modo proattivo raggiungere i clienti e inviare una notifica sull'operazione di eliminazione. 

Per l'azione di eliminazione dei dati on demand, tecnico del supporto Microsoft può accedere allo strumento dove può avviare l'eliminazione dei dati su richiesta e possono garantire conferma sul telefono con il cliente al termine.

## <a name="diagnostic-data"></a>Dati di diagnostica
Come parte del processo di supporto, gli operatori di Azure Stack possono [condividere i log di diagnostica](azure-stack-diagnostics.md) con i team di progettazione e supporto per Azure Stack per facilitare la risoluzione dei problemi.

Microsoft fornisce uno strumento e richiesta di script per i clienti di raccogliere e caricare i file di log di diagnostica. Al termine della raccolta, i file di log vengono trasferiti tramite HTTPS protetto connessione crittografata a Microsoft. Poiché HTTPS fornisce la crittografia in transito, non vi è alcuna password necessari per la crittografia in transito. Dopo che sono stati ricevuti, i log vengono crittografati e archiviati fino a quando non vengono automaticamente eliminati 90 giorni dopo la chiusura di caso di supporto.

## <a name="telemetry-data"></a>Dati di telemetria
[I dati di telemetria di Azure Stack](azure-stack-telemetry.md) carica automaticamente i dati di sistema a Microsoft tramite l'esperienza dell'utente connesso. Operatori di Azure Stack sono controlli per personalizzare le funzionalità di telemetria e le impostazioni di privacy in qualsiasi momento.

Microsoft non intende raccogliere i dati sensibili, ad esempio numeri di carta di credito, nomi utente e password, indirizzi di posta elettronica o informazioni riservate simile. Se si determina che le informazioni riservate sono state ricevute inavvertitamente, vengono eliminati. 

## <a name="billing-data"></a>Dati di fatturazione
[La fatturazione di Azure Stack](azure-stack-usage-reporting.md) si basa su fatturazione e utilizzo di Azure globale della pipeline ed è pertanto in allineamento con le indicazioni di conformità di Microsoft.

Operatori di Azure Stack è possibile configurare Azure Stack per inoltrare le informazioni sull'utilizzo di Azure per la fatturazione. È necessario per i clienti a più nodi Azure Stack che scelgono di modello di fatturazione di come è a pagamento. Report sull'utilizzo viene controllato in modo indipendente dai dati di telemetria e non è necessaria per i clienti a nodi multipli che scegliere il modello di capacità o per gli utenti di Azure Stack Development Kit. Per questi scenari, segnalazione utilizzo può essere disattivata usando [lo script di registrazione](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Passaggi successivi 
[Altre informazioni sulla sicurezza di Azure Stack](azure-stack-security-foundations.md) 
