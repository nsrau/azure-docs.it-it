---
title: Panoramica della sicurezza in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Comprendere il motivo per cui Azure Data Lake Storage Gen1 è un archivio di Big Data più sicuro
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a310851819f70d138a4980b1ab61891fb0b2c311
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959613"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Sicurezza in Azure Data Lake Storage Gen1
Molte aziende sfruttano l'analisi di Big Data per una visione completa dell'attività e per prendere decisioni appropriate. Un'organizzazione potrebbe presentare un ambiente complesso e regolamentato, con un numero crescente di utenti diversi. Per un'azienda è essenziale assicurarsi che i dati critici vengano archiviati in modo più sicuro e con un corretto livello di accesso concesso ai singoli utenti. Azure Data Lake Storage Gen1 è progettato per contribuire a soddisfare questi requisiti di sicurezza. Questo articolo fornisce informazioni sulle funzionalità di sicurezza di Data Lake Storage Gen1, tra cui:

* Authentication
* Authorization
* Isolamento rete
* Protezione dati
* Controllo

## <a name="authentication-and-identity-management"></a>Autenticazione e gestione delle identità
L'autenticazione è il processo con il quale l'identità di un utente viene verificata durante l'interazione con Data Lake Storage Gen1 o con qualsiasi servizio che si connette a Data Lake Storage Gen1. Per la gestione delle identità e l'autenticazione, Data Lake Storage Gen1 usa [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), una soluzione cloud completa per la gestione delle identità e dell'accesso che semplifica la gestione di utenti e gruppi.

Ogni sottoscrizione Azure può essere associata a un'istanza di Azure Active Directory. Solo gli utenti e le identità del servizio definite nel servizio Azure Active Directory possono accedere all'account di Data Lake Storage Gen1 usando il portale di Azure, gli strumenti da riga di comando o le applicazioni client create dall'organizzazione con Data Lake Storage Gen1 SDK. I vantaggi principali dell'uso di Azure Active Directory come meccanismo di controllo di accesso centralizzato sono:

* Gestione semplificata del ciclo di vita dell'identità. L'identità di un utente o un servizio (un'identità dell'entità servizio) può essere creata e revocata in modo rapido semplicemente eliminando o disabilitando l'account nella directory.
* Autenticazione a più fattori [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) offre un livello di sicurezza aggiuntivo per l'accesso degli utenti e le transazioni.
* Autenticazione da qualsiasi client usando un protocollo aperto standard, ad esempio OAuth oppure OpenID.
* Federazione con provider di identità cloud e servizi di directory dell'organizzazione.

## <a name="authorization-and-access-control"></a>Autorizzazione e controllo di accesso
Dopo che Azure Active Directory ha autenticato un utente in modo che possa accedere a Data Lake Storage Gen1, l'autorizzazione controlla le autorizzazioni di accesso per Data Lake Storage Gen1. Data Lake Storage Gen1 separa l'autorizzazione per le attività relative all'account e relative ai dati nel modo seguente:

* [Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) (RBAC) fornito da Azure per la gestione degli account.
* POSIX ACL per l'accesso ai dati nell'archivio

### <a name="rbac-for-account-management"></a>Controllo degli accessi in base al ruolo per la gestione degli account
Quattro ruoli di base vengono definiti per Data Lake Storage Gen1 per impostazione predefinita. I ruoli permettono di eseguire diverse operazioni su un account Data Lake Storage Gen1 tramite il portale di Azure, i cmdlet di PowerShell e le API REST. I ruoli Proprietario e Collaboratore possono eseguire un'ampia gamma di funzioni di amministrazione sull'account. È possibile assegnare il ruolo Lettore agli utenti limitati alla sola visualizzazione dei dati di gestione degli account.

![Ruoli Controllo degli accessi in base al ruolo](./media/data-lake-store-security-overview/rbac-roles.png "Ruoli Controllo degli accessi in base al ruolo")

Si noti che anche se per la gestione degli account vengono assegnati i ruoli, alcuni ruoli incidono sull'accesso ai dati. È necessario usare gli elenchi di controllo di accesso (ACL) alle operazioni che un utente può eseguire nel file system. La tabella seguente mostra un riepilogo dei diritti di gestione e dei diritti di accesso ai dati per questi ruoli predefiniti.

| Ruoli | Diritti di gestione | Diritti di accesso ai dati | Spiegazione |
| --- | --- | --- | --- |
| Nessun ruolo assegnato |Nessuna |Regolato da ACL |Gli utenti non possono usare il portale di Azure o i cmdlet di Azure PowerShell per esplorare Data Lake Storage Gen1. L'utente può usare solo gli strumenti da riga di comando. |
| Proprietario |Tutti |Tutti |Il ruolo di Proprietario è un utente avanzato. Questo ruolo può gestire tutto e ha l'accesso completo ai dati. |
| Reader |Sola lettura |Regolato da ACL |Il ruolo Lettore può visualizzare tutti gli elementi riguardanti la gestione degli account, ad esempio l'utente assegnato a un determinato ruolo, ma non prevede la possibilità di apportare modifiche. |
| Collaboratore |Tutti tranne quelli di aggiunta e rimozione dei ruoli |Regolato da ACL |Il ruolo Collaboratore può gestire alcuni aspetti di un account, ad esempio le distribuzioni e la creazione e la gestione di avvisi, ma non prevede la possibilità di aggiungere o rimuovere ruoli. |
| Amministratore accessi utente |Aggiunta e rimozione dei ruoli |Regolato da ACL |Il ruolo Amministratore accessi utente può gestire l'accesso degli utenti agli account. |

Per istruzioni, vedere [Assegnare utenti o gruppi di sicurezza agli account Data Lake Storage Gen1](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Uso degli ACL per le operazioni sui file system
Data Lake Storage Gen1 è un file system gerarchico come HDFS (Hadoop Distributed File System) e supporta gli [elenchi di controllo di accesso POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controlla le autorizzazioni di lettura (r), scrittura (w) ed esecuzione (w) per le risorse per il ruolo Proprietario, per il gruppo Proprietari e per altri utenti e gruppi. In Data Lake Storage Gen1 gli elenchi di controllo di accesso possono essere abilitati nella cartella radice, nelle sottocartelle e nei singoli file. Per altre informazioni sul funzionamento degli elenchi di controllo di accesso nel contesto di Data Lake Storage Gen1, vedere [Controllo di accesso in Data Lake Storage Gen1](data-lake-store-access-control.md).

È consigliabile definire gli elenchi di controllo di accesso per più utenti usando [gruppi di sicurezza](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Aggiungere gli utenti a un gruppo di sicurezza, quindi assegnare gli elenchi di controllo di accesso per il file o una cartella a tale gruppo di sicurezza. Ciò è utile quando si intende fornire autorizzazioni assegnate, perché non è possibile superare il limite massimo di 28 voci per le autorizzazioni assegnate. Per altre informazioni su come proteggere meglio i dati archiviati in Data Lake Storage Gen1 usando i gruppi di sicurezza di Azure Active Directory, vedere [Assegnare utenti o gruppi di sicurezza come elenchi di controllo di accesso al file system Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Elencare le autorizzazioni di accesso](./media/data-lake-store-security-overview/adl.acl.2.png "Elencare le autorizzazioni di accesso")

## <a name="network-isolation"></a>Isolamento rete
Usare Data Lake Storage Gen1 per aiutare a controllare l'accesso all'archivio dati a livello di rete. È possibile stabilire firewall e definire un intervallo di indirizzi IP per i client attendibili. Con un intervallo di indirizzi IP, solo i client con indirizzo IP compreso nell'intervallo definito possono connettersi a Data Lake Storage Gen1.

![Impostazioni del firewall e accesso IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Impostazioni del firewall e indirizzo IP")

## <a name="data-protection"></a>Protezione dati
Data Lake Storage Gen1 protegge i dati durante l'intero ciclo di vita. Per i dati in transito, Data Lake Storage Gen1 usa il protocollo standard TLS 1.2 (Transport Layer Security) per proteggere i dati trasmessi in rete.

![Crittografia in Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Crittografia in Data Lake Storage Gen1")

Data Lake Storage Gen1 offre anche la crittografia per i dati archiviati nell'account. È possibile scegliere di crittografare i dati oppure di fare a meno della crittografia. Se si opta per la crittografia, i dati archiviati in Data Lake Storage Gen1 vengono crittografati prima essere archiviati in supporti persistenti. In questo caso, Data Lake Storage Gen1 crittografa automaticamente i dati prima di renderli persistenti e li decrittografa prima di recuperarli. L'accesso ai dati, quindi, risulta totalmente trasparente per il client. Per la crittografia/decrittografia dei dati, infatti, non è necessaria alcuna modifica di codice sul lato client.

In merito alla gestione delle chiavi, Data Lake Storage Gen1 offre due modalità per la gestione delle chiavi di crittografia master (MEK) necessarie per decrittografare i dati archiviati in Data Lake Storage Gen1. È possibile affidare la gestione delle chiavi MEK a Data Lake Storage Gen1 oppure mantenere la proprietà delle chiavi MEK usando l'account di Azure Key Vault. La modalità di gestione delle chiavi viene specificata durante la creazione dell'account Data Lake Storage Gen1. Per altre informazioni su come specificare la configurazione relativa alla crittografia, vedere [Introduzione ad Azure Data Lake Storage Gen1 tramite il portale di Azure](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Log attività e di diagnostica
È possibile usare i log attività o di diagnostica a seconda che si stiano cercando log relativi alle attività correlate alla gestione degli account o alle attività correlate ai dati.

* Le attività correlate alla gestione degli account usano le API di Azure Resource Manager e vengono rilevate nel portale di Azure con i log attività.
* Le attività correlate ai dati usano le API REST WebHDFS e vengono rilevate nel portale di Azure con i log di diagnostica.

### <a name="activity-log"></a>Log attività
Per conformità alle normative, un'organizzazione potrebbe richiedere audit trail adeguati delle attività di gestione degli account nel caso fosse necessario approfondire specifici eventi imprevisti. Data Lake Storage Gen1 integra funzionalità di monitoraggio e registra tutte le attività di gestione dell'account.

Per gli audit trail di gestione account, visualizzare e scegliere le colonne che si vogliono registrare. È anche possibile esportare i log attività in Archiviazione di Azure.

![Log attività](./media/data-lake-store-security-overview/activity-logs.png "Log attività")

Per altre informazioni sull'utilizzo dei log attività, vedere [Visualizzare i log attività per controllare le azioni sulle risorse](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Log di diagnostica
È possibile abilitare il controllo di accesso ai dati e la registrazione diagnostica nel portale di Azure e inviare i log a un account di archiviazione Blob di Azure, un hub eventi o log di monitoraggio di Azure.

![Log di diagnostica](./media/data-lake-store-security-overview/diagnostic-logs.png "Log di diagnostica")

Per altre informazioni sull'uso dei log di diagnostica con Data Lake Storage Gen1, vedere [Accesso ai log di diagnostica per Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Summary
I clienti aziendali richiedono una piattaforma cloud di analisi dei dati protetta e facile da usare. Data Lake Storage Gen1 è progettato per aiutare a soddisfare questi requisiti attraverso la gestione delle identità e l'autenticazione con l'integrazione di Azure Active Directory, l'autorizzazione basata su elenchi di controllo di accesso, l'isolamento della rete, la crittografia dei dati in transito e inattivi e il controllo.

Per vedere nuove funzionalità in Data Lake Storage Gen1, inviare commenti e suggerimenti al [forum UserVoice di Data Lake Storage Gen1](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Vedere anche 
* [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Iniziare a usare Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)

