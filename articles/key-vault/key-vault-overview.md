---
title: Panoramica di Azure Key Vault| Microsoft Docs
description: Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 05/08/2018
ms.author: barclayn
ms.openlocfilehash: f9648e15c720c076a65e84a95f4160f27eec598d
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
---
# <a name="what-is-azure-key-vault"></a>Cos'è l'insieme di credenziali chiave di Azure?

Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti.

L'utente ha password, stringhe di connessione e altri tipi di informazioni necessari per l'uso delle applicazioni e intende assicurarsi che queste informazioni siano disponibili, ma che siano protette. È in questa situazione che Azure Key Vault può essere di aiuto. Azure Key Vault consente di archiviare e gestire i segreti delle applicazioni in modo sicuro.

Key Vault consente di creare più contenitori sicuri denominati insiemi di credenziali. Questi insiemi di credenziali sono supportati da moduli di protezione hardware. Gli insiemi di credenziali consentono di ridurre le probabilità di perdita accidentale di informazioni di sicurezza centralizzando l'archiviazione dei segreti delle applicazioni. Gli insiemi di credenziali delle chiavi controllano e registrano anche l'accesso a tutti gli elementi archiviati al loro interno. Azure Key Vault può gestire la richiesta e rinnovo dei certificati TLS (Transport Layer Security), offrendo le funzionalità necessarie per una soluzione affidabile di gestione del ciclo di vita dei certificati.

 Azure Key Vault è progettato per supportare chiavi e segreti delle applicazioni. Key Vault non è progettato per essere usato come archivio per le password utente.

## <a name="why-use-azure-key-vault"></a>Vantaggi di Azure Key Vault

### <a name="centralize-application-secrets"></a>Centralizzare i segreti delle applicazioni

Centralizzando l'archiviazione dei segreti delle applicazioni in Azure Key Vault è possibile controllare la distribuzione dei segreti. Si riduce così la probabilità che i segreti vengano divulgati accidentalmente. Quando si usa Key Vault, gli sviluppatori di applicazioni non devono più archiviare le informazioni di sicurezza nell'applicazione. Non è quindi necessario integrare queste informazioni nel codice. Ad esempio, per un'applicazione potrebbe essere necessario connettersi a un database. Invece di archiviare la stringa di connessione nei codici delle app, archiviarla in modo sicuro in Key Vault.

Le applicazioni possono accedere in modo sicuro alle informazioni necessarie usando URI che consentono di recuperare versioni specifiche di un segreto dopo aver archiviato la chiave o il segreto dell'applicazione in Azure Key Vault. Non è necessario scrivere codice personalizzato per proteggere le informazioni segrete.

### <a name="securely-store-secrets"></a>Archiviare segreti con la massima sicurezza

Le chiavi vengono protette da Azure con algoritmi, lunghezze delle chiavi e moduli di protezione hardware standard del settore. I moduli di protezione hardware usati sono convalidati per Federal Information Processing Standards (FIPS) 140-2 livello 2.

L'accesso di un chiamante (utente o applicazione) a un'istanza di Key Vault richiede opportune procedure di autenticazione e autorizzazione. L'autenticazione stabilisce l'identità del chiamante, mentre l'autorizzazione determina le operazioni che il chiamante è autorizzato a eseguire.

L'autenticazione avviene tramite Azure Active Directory. L'autorizzazione può essere eseguita tramite il controllo degli accessi in base al ruolo o i criteri di accesso di Key Vault. Il controllo degli accessi in base al ruolo viene usato per la gestione degli insiemi di credenziali e vengono usati criteri di accesso di Key Vault quando si tenta di accedere ai dati archiviati in un insieme di credenziali.

Le istanze di Azure Key Vault possono essere protette da moduli di protezione software o hardware. Per i casi in cui si richiede maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Microsoft usa i moduli di protezione hardware Thales. È possibile usare gli strumenti Thales per spostare una chiave dal modulo di protezione hardware ad Azure Key Vault.

Azure Key Vault è infine progettato in modo che Microsoft non possa vedere o estrarre le chiavi.

### <a name="monitor-access-and-use"></a>Monitorare l'accesso e l'uso

Dopo aver creato un paio di istanze di Key Vault, può essere utile monitorare come e quando si accede a chiavi e segreti. A tale scopo, abilitare la registrazione per Key Vault. È possibile configurare Azure Key Vault per eseguire queste operazioni:

- Archiviare in un account di archiviazione.
- Streaming in un hub eventi.
- Inviare log a Log Analytics.

L'utente ha il controllo dei registri e può proteggerli limitando l'accesso. È anche possibile eliminare i log non più necessari.

### <a name="simplified-administration-of-application-secrets"></a>Amministrazione semplificata dei segreti delle applicazioni

Quando si archiviano dati importanti, è necessario eseguire diversi passaggi. Le informazioni di sicurezza devono essere protette, seguire un ciclo di vita e avere disponibilità elevata. Azure Key Vault semplifica molte di queste operazioni come segue:

- Eliminando la necessità di avere competenze in materia di moduli di protezione hardware all'interno dell'azienda.
- Aumentando le prestazioni con breve preavviso per soddisfare i picchi d'uso dell'organizzazione.
- Replicando i contenuti di un'area di Key Vault in un'area secondaria. Ciò garantisce la disponibilità elevata ed elimina la necessità di azioni da parte dell'amministratore per l'attivazione del failover.
- Fornendo opzioni standard di amministrazione di Azure tramite il portale, l'interfaccia della riga di comando di Azure e PowerShell.
- Automatizzando determinate attività sui certificati acquistati da autorità di certificazione pubbliche, ad esempio la registrazione e il rinnovo.

Azure Key Vault consente anche di separare i segreti delle applicazioni. Le applicazioni possono accedere solo all'insieme di credenziali per il quale hanno l'autorizzazione ed essere limitate alla sola esecuzione di alcune operazioni specifiche. È possibile creare un'istanza di Azure Key Vault per ogni applicazione e limitare i segreti archiviati in un'istanza a un'applicazione e un team di sviluppatori specifici.

### <a name="integrate-with-other-azure-services"></a>Integrare il servizio con altri servizi di Azure

Essendo un archivio sicuro in Azure, Key Vault è stato usato per semplificare scenari quali [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md), la funzionalità [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) in SQL server e Azure SQL, [Web app di Azure]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). Key Vault può integrarsi con account di archiviazione, hub eventi e Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: Creare un'istanza di Azure Key Vault usando l'interfaccia della riga di comando](quick-create-cli.md)
- [Configurare un'applicazione Web di Azure per la lettura di un segreto da Key Vault ](tutorial-web-application-keyvault.md)