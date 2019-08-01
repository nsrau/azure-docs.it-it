---
title: Sicurezza dei dati in Azure Australia
description: Configurazione di Azure nelle aree australiane per soddisfare i requisiti specifici dei criteri, delle normative e delle normative del governo australiano.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608340"
---
# <a name="data-security-in-azure-australia"></a>Sicurezza dei dati in Azure Australia

Ecco i principi globali per la protezione dei dati dei clienti:

* Protezione dei dati mediante la crittografia
* Gestione dei segreti
* Limitazione dell'accesso ai dati

## <a name="encrypting-your-data"></a>Crittografia dei dati

La crittografia dei dati può essere applicata a livello di disco (inattivo), in database (inattivi e in transito), in applicazioni (in transito) e in rete (in transito). Sono disponibili diversi modi per ottenere la crittografia in Azure:

|Servizio/funzionalità|Descrizione|
|---|---|
|Crittografia del servizio di archiviazione|La crittografia del servizio di archiviazione viene abilitata a livello di account di archiviazione, determinando la crittografia automatica di BLOB in blocchi e BLOB di pagine al momento della scrittura nella risorsa di archiviazione di Azure. Quando si leggono i dati dall'archiviazione di Azure, verranno decrittografati dal servizio di archiviazione prima di essere restituiti. Usare SSE per proteggere i dati senza dover modificare o aggiungere codice alle applicazioni.|
|Crittografia dischi di Azure|Usare Crittografia dischi di Azure per crittografare i dischi dati e del sistema operativo usati da una macchina virtuale di Azure. L'integrazione con l'insieme di credenziali delle chiavi di Azure consente il controllo e facilita la gestione delle chiavi di crittografia dei dischi.|
|Crittografia a livello di applicazione lato client|La crittografia lato client è incorporata nelle librerie client di archiviazione .NET e Java, che possono usare le API di Azure Key Vault, rendendola semplice da implementare. Usare Azure Key Vault per accedere ai segreti in Azure Key Vault per utenti specifici usando Azure Active Directory.|
|Crittografia in transito|La crittografia di base disponibile per la connettività ad Azure Australia supporta il protocollo TLS (Transport Level Security) 1,2 e i certificati X. 509. Gli algoritmi di crittografia Federal Information Processing Standard (FIPS) 140-2 livello 1 vengono usati anche per le connessioni di rete dell'infrastruttura tra i Data Center di Azure in Australia.  Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8.1 e condivisioni file di Azure possono usare SMB 3,0 per la crittografia tra la macchina virtuale e la condivisione file. Usare la crittografia lato client per crittografare i dati prima che vengano trasferiti nello spazio di archiviazione in un'applicazione client e per decrittografare i dati dopo che sono stati trasferiti fuori dallo spazio di archiviazione.|
|VM IaaS|Usare crittografia dischi di Azure. Attivare Crittografia del servizio di archiviazione per crittografare i file VHD usati per eseguire il backup di questi dischi nella risorsa di archiviazione di Azure, ma vengono crittografati solo dati appena scritti. Ciò significa che se si crea una macchina virtuale e quindi si abilita Crittografia del servizio di archiviazione nell'account di archiviazione che contiene il file VHD, verranno crittografate solo le modifiche, non il file VHD originale.|
|Crittografia lato client|Questo è il metodo più sicuro per crittografare i dati, perché li crittografa prima del transito e crittografa i dati inattivi. Richiede tuttavia l'aggiunta di codice alle applicazioni tramite l'archiviazione, un'operazione che si potrebbe non voler eseguire. In questi casi, è possibile usare HTTPS per i dati in transito e crittografia del servizio di archiviazione per crittografare i dati inattivi. La crittografia lato client implica anche un carico maggiore sul client: è necessario tenere conto di questo nei piani di scalabilità, specialmente se si esegue la crittografia e il trasferimento di grandi quantità di dati.|
|

Per ulteriori informazioni sulle opzioni di crittografia in Azure, vedere la [Guida alla sicurezza di archiviazione](https://docs.microsoft.com/azure/storage/storage-security-guide).

## <a name="protecting-data-by-managing-secrets"></a>Protezione dei dati tramite la gestione dei segreti

La gestione delle chiavi di protezione è fondamentale della protezione dei dati nel cloud. I clienti devono cercare di semplificare la gestione delle chiavi e mantenere il controllo delle chiavi usate dai servizi e dalle applicazioni cloud per crittografare i dati.

### <a name="managing-secrets"></a>Gestione dei segreti

* Usare l'insieme di credenziali delle chiavi per ridurre al minimo il rischio che i segreti vengano esposti tramite file di configurazione codificati, script o nel codice sorgente. L'insieme di credenziali delle chiavi di Azure consente di crittografare le chiavi, ad esempio le chiavi di crittografia per Crittografia dischi di Azure, e i segreti, ad esempio le password, archiviandoli nei moduli di protezione hardware convalidati per FIPS 140-2 livello 2. Per una maggiore sicurezza, è possibile importare o generare le chiavi in questi moduli di protezione hardware.
* Il codice dell'applicazione e i modelli devono contenere solo riferimenti URI ai segreti, ovvero i segreti effettivi non sono in repository di codice, configurazione o codice sorgente. In tal modo si impediscono gli attacchi di phishing chiave nei repository interni o esterni, ad esempio harvest-bot in GitHub.
* Utilizzare controlli degli accessi in base al ruolo sicuri all'interno dell'insieme di credenziali delle chiavi. Se un operatore attendibile lascia la società o si trasferisce in un nuovo gruppo all'interno della società, è consigliabile impedirgli di accedere ai segreti.  

Per ulteriori informazioni, vedere [Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>Isolamento per limitare l'accesso ai dati

L'isolamento riguarda l'uso di limiti, segmentazione e contenitori per limitare l'accesso ai dati solo a utenti, servizi e applicazioni autorizzati. Ad esempio, la separazione tra i tenant è un meccanismo di sicurezza essenziale per le piattaforme cloud multi-tenant, ad esempio Microsoft Azure. L'isolamento logico consente di evitare che un tenant interferisca con le operazioni di qualsiasi altro tenant.

#### <a name="per-customer-isolation"></a>Isolamento per cliente

Azure implementa il controllo di accesso alla rete e la separazione tramite l'isolamento VLAN di livello 2, gli elenchi di controllo di accesso, i bilanciamento del carico e i filtri IP.

I clienti possono isolare ulteriormente le proprie risorse tra sottoscrizioni, gruppi di risorse, reti virtuali e subnet.

Per altre informazioni sull'isolamento in Microsoft Azure, vedere l' [isolamento nel cloud pubblico di Azure](../security/fundamentals/isolation-choices.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo sul [gateway VPN di Azure](vpn-gateway.md)