---
title: Protezione di database PaaS in Azure | Microsoft Docs
description: 'Informazioni sulle procedure consigliate per la protezione delle applicazioni Web e per dispositivi mobili in PaaS tramite il database SQL di Azure e SQL Data Warehouse. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 22db43413b5c752decf6785a75dff22ff4a68039
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596664"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Procedure consigliate per la protezione di database PaaS in Azure

In questo articolo vengono illustrate varie procedure consigliate per la protezione delle applicazioni Web e per dispositivi mobili in PaaS (piattaforma distribuita come servizio) mediante il [database SQL di Azure](../sql-database/sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Le procedure consigliate si basano sull'esperienza di tecnici e clienti con Azure.

Il database SQL di Azure e SQL Data Warehouse forniscono un servizio di database relazionale per le applicazioni basate su Internet. Verranno ora illustrati i servizi che aiutano a proteggere applicazioni e dati quando si usano il database SQL di Azure e SQL Data Warehouse in una distribuzione PaaS:

- Autenticazione di Azure Active Directory (invece di autenticazione di SQL Server)
- Firewall SQL di Azure
- Transparent data encryption (TDE)

## <a name="use-a-centralized-identity-repository"></a>Usare un repository delle identità centralizzato
È possibile configurare i database SQL di Azure affinché usino uno tra due tipi di autenticazione:

- **Autenticazione SQL** usa nome utente e password. Durante la creazione del server logico per il database, è stato specificato un account di accesso "amministratore del server" con un nome utente e una password. Usando queste credenziali, è possibile essere autenticati in qualsiasi database di tale server in qualità di proprietario del database.

- **Autenticazione di Azure Active Directory** usa identità gestite da Azure Active Directory ed è supportata per domini gestiti e integrati. Per usare l'autenticazione di Azure Active Directory, è necessario creare un altro amministratore del server denominato "admin Azure AD," che è autorizzato ad amministrare utenti e gruppi di Azure AD. Questo amministratore può inoltre eseguire tutte le operazioni che un amministratore del server regolare può fare.

L'[autenticazione di Azure Active Directory](../active-directory/develop/authentication-scenarios.md) è un meccanismo di connessione al database SQL di Azure e a SQL Data Warehouse tramite le identità di Azure Active Directory (AD). Azure AD rappresenta un'alternativa all'autenticazione di SQL Server, anche per evitare la proliferazione di identità utente in più server di database. Con l'autenticazione di Azure AD è possibile gestire centralmente le identità degli utenti del database e di altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Vantaggi dell'uso di Azure AD al posto dell'autenticazione SQL
- Consente la rotazione delle password in un'unica posizione.
- Consente di gestire le autorizzazioni del database tramite gruppi Azure AD esterni.
- Consente di eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure AD.
- Usa gli utenti di database indipendente per autenticare le identità a livello di database.
- Supporta l'autenticazione basata su token per le applicazioni che si connettono al database SQL.
- Supporta la federazione dei domini con Active Directory Federation Services (AD FS) o l'autenticazione utente/password nativa per un'istanza locale di Azure AD senza la sincronizzazione del dominio.
- Supporta le connessioni da SQL Server Management Studio che utilizzano l'autenticazione universale di Active Directory, che include l'autenticazione [MFA (Multi-Factor Authentication)](../active-directory/authentication/multi-factor-authentication.md). L'MFA include funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui: chiamata telefonica, SMS, smart card con pin o notifica tramite app per dispositivi mobili. Per altre informazioni, vedere [Autenticazione universale con database SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Per altre informazioni sull'autenticazione di Azure AD, vedere:

- [Usare Azure Active Directory per l'autenticazione al database SQL, a Istanza gestita e a SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md)
- [Autenticazione in Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Supporto per l'autenticazione basata su token per il database di SQL Azure mediante l'autenticazione di Azure AD](../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Per assicurarsi che Azure Active Directory sia ideale per l'ambiente di riferimento, vedere [le funzionalità e le limitazioni di Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Limitare l'accesso in base all'indirizzo IP
È possibile creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. Queste regole possono essere destinate a livello di server e database. È consigliabile usare le regole del firewall a livello di database quando è possibile, allo scopo di migliorare la sicurezza e la portabilità del database. Le regole del firewall a livello di server sono utili per gli amministratori e in presenza di molti molti database che presentano gli stessi requisiti di accesso ma non si vuole dedicare tempo alla configurazione di ogni singolo database.

Le restrizioni predefinite agli indirizzi IP di origine del database SQL consentono l'accesso da qualsiasi indirizzo di Azure, inclusi altri tenant e sottoscrizioni. È possibile modificare le restrizioni per consentire l'accesso all'istanza solo ai propri indirizzi IP. Anche in presenza del firewall SQL e delle restrizioni per gli indirizzi IP, è comunque necessaria un'autenticazione avanzata. Vedere le indicazioni riportate in precedenza in questo articolo.

Per altre informazioni sul firewall SQL di Azure e sulle restrizioni per gli indirizzi IP, vedere:

- [Controllo di accesso per il database SQL di Azure e SQL Data Warehouse](../sql-database/sql-database-control-access.md)
- [Regole del firewall per il database SQL di Azure e SQL Data Warehouse](../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Crittografare i dati inattivi
La funzionalità [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) è abilitata per impostazione predefinita. Crittografa in modo trasparente i file di log e dati di SQL Server, del database SQL di Azure e Microsoft Azure SQL Data Warehouse. Questa crittografia consente di proteggere da una violazione di accesso diretto ai file o ai backup. Ciò consente di crittografare i dati inattivi senza modificare le applicazioni esistenti. La funzionalità Transparent Data Encryption deve essere sempre attivata; un malintenzionato potrà comunque eseguire un attacco tramite il percorso di accesso normale. Offre inoltre la possibilità di conformarsi a diverse leggi, normative e linee guida stabilite in vari settori.

Azure SQL gestisce i principali problemi correlati per TDE. Come con TDE, è necessario prestare particolare attenzione al livello locale per garantire la recuperabilità e lo spostamento di database. In scenari più sofisticati, le chiavi possono essere gestite in modo esplicito in Azure Key Vault tramite Extensible Key Management. Vedere [Abilitare TDE in SQL Server con EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Ciò consente anche l'uso di Bring Your Own Key (BYOK) tramite la capacità BYOK di Azure Key Vault.

Azure SQL fornisce la crittografia per le colonne tramite [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Questa crittografia consente solo alle applicazioni autorizzate l'accesso alle colonne sensibili. L'uso di questo tipo di crittografia limita le query SQL per le colonne crittografate a valori basati sull'uguaglianza.

La crittografia a livello di applicazione deve essere usata anche per dati selettivi. È possibile talvolta limitare le problematiche legate alla sovranità dei dati crittografandoli con una chiave da conservare nel paese appropriato. In questo modo si impedisce anche che un trasferimento accidentale dei dati possa rappresentare un problema, perché è comunque impossibile decrittografarli senza la chiave, presupponendo che venga usato un algoritmo avanzato come AES 256.

È possibile usare diverse precauzioni per proteggere il database, ad esempio la progettazione di un sistema sicuro, la crittografia di risorse riservate e la creazione di un firewall che protegga i server di database.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono state illustrate varie procedure consigliate per la protezione delle applicazioni Web e per dispositivi mobili in PaaS mediante il database SQL e SQL Data Warehouse. Per altre informazioni sulla protezione delle distribuzioni PaaS, vedere:

- [Protezione delle distribuzioni PaaS](security-paas-deployments.md)
- [Protezione delle applicazioni Web e per dispositivi mobili in PaaS mediante i Servizi app di Azure](security-paas-applications-using-app-services.md)
