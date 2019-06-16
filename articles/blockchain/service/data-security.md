---
title: Sicurezza di Azure Blockchain Service
description: Azure Blockchain Service protezione e accesso ai concetti di data
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028201"
---
# <a name="azure-blockchain-service-security"></a>Sicurezza di Azure Blockchain Service

Azure Blockchain Service Usa diverse funzionalità di Azure per mantenere i dati protetti e disponibile. Dati sono protetti tramite l'isolamento, la crittografia e autenticazione.

## <a name="isolation"></a>Isolamento

Le risorse di Azure Blockchain Service vengono isolate in una rete virtuale privata. Ogni nodo di transazione e la convalida è una macchina virtuale (VM). Macchine virtuali in una rete virtuale può comunicare direttamente con le macchine virtuali in una rete virtuale diversa. L'isolamento garantisce la comunicazione rimane privata all'interno della rete virtuale. Per altre informazioni sull'isolamento di rete virtuale di Azure, vedere [isolamento nel Cloud pubblico di Azure](../../security/azure-isolation.md#networking-isolation).

![Diagramma tra reti VIRTUALI](./media/data-security/vnet.png)

## <a name="encryption"></a>Crittografia

Dati utente vengono archiviati in archiviazione di Azure. I dati utente vengono crittografati in transito e inattivi per la sicurezza e la riservatezza. Per altre informazioni, vedere: [Guida alla sicurezza di archiviazione Azure](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Authentication

Le transazioni possono essere inviate ai nodi di blockchain tramite un endpoint RPC. I client comunicano con un nodo transazione utilizzando un server proxy inverso che gestisce l'autenticazione utente e crittografa i dati tramite SSL.

![Diagramma di autenticazione](./media/data-security/authentication.png)

Sono disponibili tre modalità di autenticazione per l'accesso RPC.

### <a name="basic-authentication"></a>Autenticazione di base

L'autenticazione di base usa un'intestazione di autenticazione HTTP che contiene il nome utente e password. Nome utente è il nome del nodo di blockchain. Password viene impostata durante il provisioning di un membro o un nodo. La password può essere modificata tramite il portale di Azure o della riga di comando.

### <a name="access-keys"></a>Chiavi di accesso

Le chiavi di accesso usano una stringa generata casualmente inclusa nell'URL dell'endpoint. Due chiavi di accesso come abilitare la rotazione della chiave. Le chiavi possono essere rigenerate dal portale di Azure e della riga di comando.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) usa un basata sulle attestazioni meccanismo di autenticazione in cui l'utente è autenticato da Azure AD usando le credenziali utente di Azure AD. Azure AD fornisce la gestione delle identità basato sul cloud e consente ai clienti di usare un'unica identità attraverso un intere applicazioni dell'organizzazione e accesso nel cloud. Azure Blockchain Service si integra con Azure AD, l'abilitazione della federazione di ID, l'autenticazione single sign-on e a più fattori. È possibile assegnare gli utenti, gruppi e ruoli dell'applicazione all'interno dell'organizzazione per l'accesso di membro e nodo blockchain.

Il proxy client di Azure AD è disponibile nel [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Il proxy client indirizza l'utente alla pagina di accesso AD Azure e ottiene un token di connessione al termine dell'autenticazione. Successivamente, l'utente si connette un'applicazione client Ethereum, ad esempio Geth o Truffle all'endpoint del proxy client. Infine, quando viene inviata una transazione, il proxy client viene inserito il token di connessione nell'intestazione http e il proxy inverso convalida il token usando il protocollo OAuth.

## <a name="keys-and-ethereum-accounts"></a>Le chiavi e gli account Ethereum

Durante il provisioning di un membro del servizio di Azure Blockchain, viene generato un account Ethereum e una coppia di chiavi pubblica e privata. La chiave privata viene utilizzata per inviare le transazioni per la blockchain. L'account Ethereum corrisponde agli ultimi 20 byte dell'algoritmo hash della chiave pubblica. L'account Ethereum viene chiamato anche un portafoglio.

La coppia di chiavi pubblica e privata verrà archiviata come un file di chiave in formato JSON. La chiave privata viene crittografata usando la password immessa quando viene creato il servizio di contabilità blockchain.

Le chiavi private vengono utilizzate per firmare digitalmente transazioni. In privato blockchain, un contratto smart firmato da una chiave privata rappresenta l'identità del firmatario. Per verificare la validità della firma, il ricevitore può confrontare la chiave pubblica del firmatario con l'indirizzo calcolato dalla firma.

Constellation chiavi vengono usate per identificare in modo univoco un nodo di Quorum. Le chiavi di constellation generate al momento del provisioning di nodi e vengono specificate nel parametro privateFor di una transazione privata di quorum.

## <a name="next-steps"></a>Passaggi successivi

[Configurare i nodi delle transazioni di servizio di Azure Blockchain](configure-transaction-nodes.md)
