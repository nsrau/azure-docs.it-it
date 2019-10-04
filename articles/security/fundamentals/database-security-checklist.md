---
title: Elenco di controllo della sicurezza del database di Azure | Microsoft Docs
description: Questo articolo fornisce un set di elenco di controllo per la sicurezza del database di Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 4a6796c0a9a64bc40255de64b39ce881e1b8855c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934735"
---
# <a name="azure-database-security-checklist"></a>Elenco di controllo per la sicurezza del database di Azure

Per contribuire al miglioramento della sicurezza, il database di Azure include una serie di controlli di sicurezza predefiniti che è possibile usare per limite e controllare l'accesso.

Sono inclusi:

-   Un firewall che consente di creare [regole del firewall](../../sql-database/sql-database-firewall-configure.md) limitando la connettività tramite un indirizzo IP,
-   Regola del firewall a livello di server accessibile nel portale di Azure
-   Regole del firewall a livello di database accessibili da SSMS
-   Connettività sicura al database tramite delle stringhe di connessione protetta
-   Usare la gestione degli accessi
-   Crittografia dati
-   Controllo del database SQL
-   Rilevamento delle minacce nel database SQL

## <a name="introduction"></a>Introduzione
Il cloud computing richiede nuovi paradigmi di sicurezza che sono diversi per molti utenti dell'applicazione, amministratori del database e programmatori. Di conseguenza, alcune organizzazioni esitano a implementare un'infrastruttura cloud per la gestione dei dati a causa dei rischi di sicurezza percepiti. Tuttavia, la maggior parte di questo problema può essere mitigato tramite una migliore comprensione della funzionalità di sicurezza incorporate in Microsoft Azure e nel database SQL di Microsoft Azure.

## <a name="checklist"></a>Elenco di controllo
Si consiglia di leggere l'articolo [Azure Database Security Best Practices](database-best-practices.md) (Best practice di sicurezza per il database di Azure) prima di esaminare questo elenco di controllo. Si sarà in grado di sfruttare al meglio questo elenco di controllo dopo aver compreso le procedure consigliate. È quindi possibile usare questo elenco di controllo per assicurarsi di aver trattato gli aspetti importanti nella sicurezza del database di Azure.


|Categoria dell'elenco di controllo| Descrizione|
| ------------ | -------- |
|**Proteggere i dati**||
| <br> Crittografia in movimento/transito| <ul><li>[Transport Layer Security](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol), per la crittografia dei dati durante lo spostamento dei dati nelle reti.</li><li>Il database richiede una comunicazione sicura da client basati sul protocollo [TDS (Tabular Data Stream)](https://msdn.microsoft.com/library/dd357628.aspx) in TLS (Transport Layer Security).</li></ul> |
|<br>Crittografia di dati inattivi| <ul><li>[Transparent Data Encryption](https://go.microsoft.com/fwlink/?LinkId=526242), quando i dati inattivi vengono archiviati fisicamente in qualsiasi forma digitale.</li></ul>|
|**Controllare l'accesso**||  
|<br> Accesso al database | <ul><li>[Autenticazione](../../sql-database/sql-database-control-access.md) l'autenticazione AD (Autenticazione di Azure Active Directory) usa identità gestite da Azure Active Directory.</li><li>[Autorizzazione](../../sql-database/sql-database-control-access.md) concede agli utenti i privilegi minimi necessari.</li></ul> |
|<br>Accesso all'applicazione| <ul><li>[Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131) (tramite i criteri di sicurezza, al tempo stesso limitando l'accesso a livello di riga in base all'identità dell'utente, al ruolo o al contesto di esecuzione).</li><li>[Dynamic Data Masking](../../sql-database/sql-database-dynamic-data-masking-get-started.md) (tramite Autorizzazione e Politica, limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi)</li></ul>|
|**Monitoraggio proattivo**||  
| <br>Monitoraggio e rilevamento| <ul><li>Il [Controllo](../../sql-database/sql-database-auditing.md) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo/log di attività nell'[account di Archiviazione di Azure](../../storage/common/storage-create-storage-account.md).</li><li>Tenere traccia dell'integrità del database di Azure tramite i [log attività di Monitoraggio di Azure](../../azure-monitor/platform/activity-logs-overview.md).</li><li>La [funzionalità di rilevamento delle minacce](../../sql-database/sql-database-threat-detection.md) individua le attività di database che indicano la presenza di potenziali minacce alla sicurezza nel database. </li></ul> |
|<br>Centro sicurezza di Azure| <ul><li>Il [Monitoraggio dati](../../security-center/security-center-enable-auditing-on-sql-databases.md) usa il centro sicurezza di Azure come soluzione di monitoraggio di sicurezza centralizzato per SQL e altri servizi di Azure.</li></ul>|       

## <a name="conclusion"></a>Conclusione
Il database di Azure è una piattaforma di database affidabile, con una gamma completa di funzionalità di sicurezza che soddisfano molti requisiti normativi e aziendali. È possibile proteggere facilmente i dati controllando l'accesso fisico agli stessi, e tramite un'ampia gamma di opzioni per la protezione dei dati a livello di file, di colonna o di riga con Transparent Data Encryption, Cell-Level Encryption o Row-Level Encryption. Always Encrypted consente anche operazioni sui dati crittografati, semplificando il processo di aggiornamenti dell'applicazione. A sua volta, l'accesso ai log di controllo dell'attività del database SQL fornisce le informazioni necessarie, consentendo di sapere come e quando viene eseguito l'accesso ai dati.

## <a name="next-steps"></a>Passaggi successivi
È possibile migliorare la protezione del database contro utenti malintenzionati o accessi non autorizzati con pochi semplici passaggi. In questa esercitazione si apprenderà come:

- Configurare [regole del firewall](../../sql-database/sql-database-firewall-configure.md) per il server e/o il database.
- Proteggere i dati con la [crittografia](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption).
- Abilitare il [controllo del database SQL](../../sql-database/sql-database-auditing.md).

