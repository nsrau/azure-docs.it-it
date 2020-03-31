---
title: Sicurezza del servizio Blockchain di AzureAzure Blockchain Service security
description: Concetti di sicurezza e accesso ai dati del servizio Blockchain di AzureAzure Blockchain Service data access and security concepts
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982235"
---
# <a name="azure-blockchain-service-security"></a>Sicurezza del servizio Blockchain di AzureAzure Blockchain Service security

Il servizio Blockchain di Azure usa diverse funzionalità di Azure per mantenere i dati protetti e disponibili. I dati sono protetti tramite isolamento, crittografia e autenticazione.

## <a name="isolation"></a>Isolamento

Le risorse del servizio Blockchain di Azure sono isolate in una rete virtuale privata. Ogni nodo di transazione e convalida è una macchina virtuale (VM). Le macchine virtuali in una rete virtuale non possono comunicare direttamente con le macchine virtuali in una rete virtuale diversa. L'isolamento garantisce che la comunicazione rimanga privata all'interno della rete virtuale. Per altre informazioni sull'isolamento della rete virtuale di Azure, vedere isolamento nel cloud pubblico di Azure.For more information on Azure virtual network isolation, see [isolation in the Azure Public Cloud](../../security/fundamentals/isolation-choices.md#networking-isolation).

![Diagramma VNET](./media/data-security/vnet.png)

## <a name="encryption"></a>Crittografia

I dati utente vengono archiviati in Archiviazione di Azure.User data is stored in Azure storage. I dati dell'utente vengono crittografati in movimento e inattivi per motivi di sicurezza e riservatezza. Per altre informazioni, vedere: Guida alla [sicurezza di Archiviazione di Azure](../../storage/blobs/security-recommendations.md).For more information, see: Azure Storage security guide .

## <a name="authentication"></a>Authentication

Le transazioni possono essere inviate ai nodi blockchain tramite un endpoint RPC. I client comunicano con un nodo di transazione utilizzando un server proxy inverso che gestisce l'autenticazione utente e crittografa i dati su SSL.

![Diagramma di autenticazione](./media/data-security/authentication.png)

Sono disponibili tre modalità di autenticazione per l'accesso RPC.

### <a name="basic-authentication"></a>Autenticazione di base

L'autenticazione di base utilizza un'intestazione di autenticazione HTTP contenente il nome utente e la password. Nome utente è il nome del nodo blockchain. La password viene impostata durante il provisioning di un membro o di un nodo. La password può essere modificata usando il portale di Azure o l'interfaccia della riga di comando.

### <a name="access-keys"></a>Chiavi di accesso

Le chiavi di accesso usano una stringa generata in modo casuale inclusa nell'URL dell'endpoint. Due tasti di scelta consentono la rotazione dei tasti. Le chiavi possono essere rigenerate dal portale di Azure e dall'interfaccia della riga di comando.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) usa un meccanismo di autenticazione basata sulle attestazioni in cui l'utente viene autenticato da Azure AD usando le credenziali utente di Azure AD. Azure AD offre la gestione delle identità basata su cloud e consente ai clienti di usare una singola identità in un'intera azienda e di accedere alle applicazioni nel cloud. Il servizio Blockchain di Azure si integra con Azure AD abilitando la federazione degli ID, l'accesso Single Sign-On e l'autenticazione a più fattori. È possibile assegnare utenti, gruppi e ruoli applicazione nell'organizzazione per l'accesso a membri e nodi blockchain.

Il proxy client di Azure AD è disponibile in [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Il proxy client indirizza l'utente alla pagina di accesso di Azure AD e ottiene un token di connessione al termine dell'autenticazione. Successivamente, l'utente connette un'applicazione client Ethereum, ad esempio Geth o Truffle, all'endpoint del proxy client. Infine, quando viene inviata una transazione, il proxy client inserisce il token di connessione nell'intestazione http e il proxy inverso convalida il token utilizzando il protocollo OAuth.

## <a name="keys-and-ethereum-accounts"></a>Chiavi e conti Ethereum

Quando si esegue il provisioning di un membro del servizio Blockchain di Azure, vengono generati un account Ethereum e una coppia di chiavi pubblica e privata. La chiave privata viene utilizzata per inviare transazioni alla blockchain. L'account Ethereum è costituito dagli ultimi 20 byte dell'hash della chiave pubblica. L'account Ethereum è anche chiamato un portafoglio.

La coppia di chiavi pubblica e privata viene archiviata come file di chiave in formato JSON. La chiave privata viene crittografata utilizzando la password immessa quando viene creato il servizio di contabilità blockchain.

Le chiavi private vengono utilizzate per firmare digitalmente le transazioni. Nelle blockchain private, un contratto intelligente firmato da una chiave privata rappresenta l'identità del firmatario. Per verificare la validità della firma, il destinatario può confrontare la chiave pubblica del firmatario con l'indirizzo calcolato dalla firma.

Le chiavi di costellazione vengono utilizzate per identificare in modo univoco un nodo Quorum. Le chiavi di costellazione vengono generate al momento del provisioning del nodo e vengono specificate nel parametro privateFor di una transazione privata in Quorum.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Come configurare l'accesso ad Azure Active Directory per](configure-aad.md)il servizio Blockchain di Azure.
