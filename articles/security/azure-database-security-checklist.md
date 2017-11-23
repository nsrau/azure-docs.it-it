---
title: Elenco di controllo della sicurezza del database di Azure | Microsoft Docs
description: Questo articolo fornisce un set di elenco di controllo per la sicurezza del database di Azure.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 11fa0d01463e398ae4106e1a70dc4c51b8698ff4
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-database-security-checklist"></a>Elenco di controllo per la sicurezza del database di Azure

Per contribuire al miglioramento della sicurezza, il database di Azure include una serie di controlli di sicurezza predefiniti che è possibile usare per limite e controllare l'accesso.

incluse le seguenti:

-   Un firewall che consente di creare [regole del firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) limitando la connettività tramite un indirizzo IP,
-   Regola del firewall a livello di server accessibile nel portale di Azure
-   Regole del firewall a livello di database accessibili da SSMS
-   Connettività sicura al database tramite delle stringhe di connessione protetta
-   Usare la gestione degli accessi
-   Crittografia dei dati
-   Controllo del database SQL
-   Rilevamento delle minacce nel database SQL

## <a name="introduction"></a>Introduzione
Il cloud computing richiede nuovi paradigmi di sicurezza che sono diversi per molti utenti dell'applicazione, amministratori del database e programmatori. Di conseguenza, alcune organizzazioni esitano a implementare un'infrastruttura cloud per la gestione dei dati a causa dei rischi di sicurezza percepiti. Tuttavia, la maggior parte di questo problema può essere mitigato tramite una migliore comprensione della funzionalità di sicurezza incorporate in Microsoft Azure e nel Database SQL di Microsoft Azure.

## <a name="checklist"></a>Elenco di controllo
Si consiglia di leggere l'articolo [Azure Database Security Best Practices](https://docs.microsoft.com/en-us/azure/security/azure-database-security-best-practices) (Best practice di sicurezza per il database di Azure) prima di esaminare questo elenco di controllo. Si sarà in grado di sfruttare al meglio questo elenco di controllo dopo aver compreso le procedure consigliate. È quindi possibile usare questo elenco di controllo per assicurarsi di aver trattato gli aspetti importanti nella sicurezza del database di Azure.


|Categoria dell'elenco di controllo| Descrizione|
| ------------ | -------- |
|**Proteggere i dati**||
| <br> Crittografia in movimento/transito| <ul><li>[Transport Layer Security](https://docs.microsoft.com/en-us/windows-server/security/tls/transport-layer-security-protocol), per la crittografia dei dati durante lo spostamento dei dati nelle reti.</li><li>Il database richiede una comunicazione sicura da client basati sul protocollo [TDS (Tabular Data Stream)](https://msdn.microsoft.com/en-in/library/dd357628.aspx) in TLS (Transport Layer Security).</li></ul> |
|<br>Crittografia di dati inattivi| <ul><li>[Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242), quando i dati inattivi vengono archiviati fisicamente in qualsiasi forma digitale.</li></ul>|
|**Controllare l'accesso**||  
|<br> Accesso al database | <ul><li>[Autenticazione](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) l'autenticazione AD (Autenticazione di Azure Active Directory) usa identità gestite da Azure Active Directory.</li><li>[Autorizzazione](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) concede agli utenti i privilegi minimi necessari.</li></ul> |
|<br>Accesso all'applicazione| <ul><li>[Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131) (tramite i criteri di sicurezza, al tempo stesso limitando l'accesso a livello di riga in base all'identità dell'utente, al ruolo o al contesto di esecuzione).</li><li>[Dynamic Data Masking](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) (tramite Autorizzazione e Politica, limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi)</li></ul>|
|**Monitoraggio proattivo**||  
| <br>Monitoraggio e rilevamento| <ul><li>Il [Controllo](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo/log di attività nell'[account di Archiviazione di Azure](https://docs.microsoft.com/en-us/azure/storage/storage-create-storage-account).</li><li>Tenere traccia dell'integrità del database di Azure tramite i [log attività di Monitoraggio di Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>La [funzionalità di rilevamento delle minacce](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection) individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. </li></ul> |
|<br>Centro sicurezza di Azure| <ul><li>Il [Monitoraggio dati](https://docs.microsoft.com/en-us/azure/security-center/security-center-enable-auditing-on-sql-databases) usa il centro sicurezza di Azure come soluzione di monitoraggio di sicurezza centralizzato per SQL e altri servizi di Azure.</li></ul>|     

## <a name="conclusion"></a>Conclusione
Il database di Azure è una piattaforma di database affidabile, con una gamma completa di funzionalità di sicurezza che soddisfano molti requisiti normativi e aziendali. È possibile proteggere facilmente i dati controllando l'accesso fisico agli stessi, e tramite un'ampia gamma di opzioni per la protezione dei dati a livello di file, di colonna o di riga con Transparent Data Encryption, Cell-Level Encryption o Row-Level Encryption. Always Encrypted consente anche operazioni sui dati crittografati, semplificando il processo di aggiornamenti dell'applicazione. A sua volta, l'accesso ai log di controllo dell'attività del database SQL fornisce le informazioni necessarie, consentendo di sapere come e quando viene eseguito l'accesso ai dati.

## <a name="next-steps"></a>Passaggi successivi
È possibile migliorare la protezione del database contro utenti malintenzionati o accessi non autorizzati con pochi semplici passaggi. In questa esercitazione si apprenderà come:

- Configurare le [regole del firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) per il server e/o il database.
- Proteggere i dati con la [crittografia](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/sql-server-encryption).
- Abilitare il [controllo del database SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing).

