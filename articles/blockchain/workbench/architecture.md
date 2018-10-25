---
title: Architettura di Azure Blockchain Workbench
description: Panoramica dell'architettura di Azure Blockchain Workbench e dei relativi componenti.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 91dd0f262d1a7e661a6f9e0c4974087503dde3e1
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48241536"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architettura di Azure Blockchain Workbench

Azure Blockchain Workbench semplifica lo sviluppo di applicazioni blockchain fornendo una soluzione che usa diversi componenti di Azure. Blockchain Workbench può essere distribuito tramite un modello di soluzione disponibile in Azure Marketplace. Il modello permette di scegliere i moduli e i componenti da distribuire, ad esempio lo stack di blockchain, il tipo di applicazione client e il supporto per l'integrazione IoT. Una volta distribuito, Blockchain Workbench fornisce l'accesso a un'app Web, un'app per iOS e un'app per Android.

![Architettura di Blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identità e autenticazione

Usando Blockchain Workbench, un consorzio può federare le proprie identità aziendali tramite Azure Active Directory (Azure AD). Workbench genera nuovi account utente per identità on-chain, mentre le identità aziendali vengono archiviate in Azure AD. Il mapping delle identità semplifica l'accesso autenticato ad applicazioni e API client e usa i criteri di autenticazione delle organizzazioni. Workbench permette anche di associare identità aziendali a ruoli specifici all'interno di un contratto intelligente specifico. Inoltre, Workbench fornisce anche un meccanismo per identificare le azioni che questi ruoli possono eseguire e in quali momenti.

Una volta distribuito, gli utenti interagiscono con Blockchain Workbench tramite le applicazioni client, l'API client basata su REST o l'API di messaggistica. In tutti i casi, le interazioni devono essere autenticate, tramite Azure Active Directory (Azure AD) o le credenziali specifiche del dispositivo.

Gli utenti federano le proprie identità in Azure AD per il consorzio inviando un invito tramite posta elettronica agli indirizzi dei partecipanti. Durante l'accesso, questi utenti vengono autenticati tramite nome, password e criteri. Ad esempio, l'autenticazione a due fattori della propria organizzazione.

Azure AD viene usato per gestire tutti gli utenti che hanno accesso a Blockchain Workbench. Ogni dispositivo che si connette a un contratto intelligente è anche associato ad Azure AD.

Azure AD viene usato anche per assegnare utenti a un gruppo di amministratori speciale. Gli utenti associati al gruppo di amministratori ottengono accesso a diritti/azioni in Blockchain Workbench, ad esempio la distribuzione di contratti e la concessione di autorizzazioni a un utente per accedere a un contratto. Gli utenti esterni a questo gruppo non hanno accesso alle azioni degli amministratori.

## <a name="client-applications"></a>Applicazioni client

Workbench fornisce applicazioni client generate automaticamente per il Web e per dispositivi mobili (iOS, Android), che possono essere usate per convalidare, testare e visualizzare le applicazioni blockchain. L'interfaccia delle applicazioni viene generata dinamicamente in base ai metadati dei contratti intelligenti ed è in grado di adattarsi a qualsiasi caso d'uso. Le applicazioni client forniscono un front-end destinato agli utenti per le applicazioni blockchain complete generate da Blockchain Workbench. Le applicazioni client autenticano gli utenti tramite Azure Active Directory (Azure AD) e quindi presentano un'esperienza utente ottimizzata per il contesto aziendale del contratto intelligente. L'esperienza utente permette la creazione di nuove istanze di contratti intelligenti da parte di persone autorizzate e quindi offre la possibilità di eseguire determinati tipi di transazioni in punti appropriati del processo aziendale rappresentato dal contratto intelligente.

Nell'applicazione Web gli utenti autorizzati possono accedere alla Console di amministrazione. La console è disponibile per gli utenti del gruppo di amministratori in Azure AD e permette di eseguire le operazioni seguenti:

* Distribuire contratti intelligenti forniti da Microsoft per scenari comuni. Ad esempio, uno scenario di trasferimento di asset.
* Caricare e distribuire i propri contratti intelligenti.
* Assegnare a un utente l'accesso al contratto intelligente nel contesto di un ruolo specifico.

## <a name="gateway-service-api"></a>API del servizio gateway

Blockchain Workbench include un'API del servizio gateway basata su REST. Quando si scrive in una blockchain, l'API genera e invia messaggi a un gestore eventi. Quando vengono richiesti dati dall'API, le query vengono inviate al database SQL off-chain. Il database SQL contiene una replica di dati e metadati on-chain che fornisce le informazioni su contesto e configurazione per i contratti intelligenti supportati. Le query restituiscono i dati necessari dalla replica off-chain in un formato determinato dai metadati per il contratto.

Gli sviluppatori possono accedere all'API del servizio gateway per compilare o integrare soluzioni blockchain senza ricorrere ad app client Blockchain Workbench.

> [!NOTE]
> Per abilitare l'accesso autenticato all'API, vengono registrate due applicazioni client in Azure Active Directory. Azure Active Directory richiede la registrazione distinta di ogni tipo di applicazione, nativa e Web. 

## <a name="message-broker-for-incoming-messages"></a>Broker di messaggi per i messaggi in arrivo

Gli sviluppatori che vogliono inviare messaggi direttamente a Blockchain Workbench possono inviarli direttamente al bus di servizio. Ad esempio, è possibile usare l'API dei messaggi per l'integrazione da sistema a sistema o per dispositivi IoT.

## <a name="message-broker-for-downstream-consumers"></a>Broker di messaggi per i consumer a valle

Durante il ciclo di vita dell'applicazione, si verificano eventi. Gli eventi possono essere attivati dall'API Gateway o nel libro mastro. Le notifiche degli eventi possono avviare il codice a valle in base all'evento.

Blockchain Workbench distribuisce automaticamente due tipi di consumer di eventi. Uno viene attivato da eventi della blockchain per popolare l'archivio SQL off-chain. L'altro corrisponde all'acquisizione di metadati per gli eventi generati dall'API relativamente al caricamento e all'archiviazione di documenti.

## <a name="message-consumers"></a>Consumer di messaggi

 I consumer di messaggi accettano messaggi dal bus di servizio. Il modello di gestione degli eventi sottostante per i consumer di messaggi permette estensioni di sistemi e servizi aggiuntivi. Ad esempio, è possibile aggiungere il supporto per popolare Cosmos DB o valutare i messaggi tramite Azure Streaming Analytics. Le sezioni seguenti descrivono i consumer di messaggi inclusi in Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Consumer di libri mastri distribuiti

I messaggi DLT (Distributed Ledger Technology) contengono i metadati per la scrittura delle transazioni nella blockchain. Il consumer recupera i messaggi e inserisce i dati in un generatore, un firmatario e un router di transazioni.

### <a name="database-consumer"></a>Consumer di database

Il consumer di database riceve messaggi dal bus di servizio e inserisce i dati in un database collegato, ad esempio il database SQL.

### <a name="storage-consumer"></a>Consumer di archiviazione

Il consumer di archiviazione riceve messaggi dal bus di servizio e inserisce i dati in una risorsa di archiviazione collegata. Ad esempio, l'archiviazione di documenti con hash in Archiviazione di Azure.

## <a name="transaction-builder-and-signer"></a>Generatore e firmatario di transazioni

Se un messaggio nel broker di messaggi in ingresso deve essere scritto nella blockchain, il messaggio verrà elaborato dal consumer DLT. Il consumer DLT è un servizio che recupera il messaggio contenente i metadati per una transazione desiderata da eseguire e quindi invia le informazioni al *generatore e firmatario di transazioni*. Il *generatore e firmatario di transazioni* assembla una transazione blockchain in base ai dati e alla destinazione blockchain desiderata. Una volta assemblata, la transazione viene firmata. Le chiavi private vengono archiviate in Azure Key Vault.

 Blockchain Workbench recupera la chiave privata appropriata da Key Vault e firma la transazione esternamente a Key Vault. Una volta firmata, la transazione viene inviata ai libri mastri e ai router di transazioni.

## <a name="transaction-routers-and-ledgers"></a>Libri mastri e router di transazioni

I libri mastri e i router di transazioni instradano le transazioni firmate alla blockchain appropriata. Attualmente Blockchain Workbench supporta Ethereum come blockchain di destinazione.

## <a name="dlt-watcher"></a>Watcher DLT

Un watcher DLT (Distributed Ledger Technology) monitora gli eventi che si verificano nelle blockchain collegate a Blockchain Workbench.
Gli eventi riflettono informazioni rilevanti per persone e sistemi. Ad esempio, la creazione di nuove istanze di contratto, l'esecuzione di transazioni e le modifiche dello stato. Gli eventi vengono acquisiti e inviati al broker di messaggi in uscita, in modo da essere utilizzati dai consumer a valle.

Ad esempio, il consumer SQL monitora gli eventi, li utilizza e popola il database SQL con i valori inclusi. La copia permette la ricreazione di una replica di dati on-chain in un archivio off-chain.

## <a name="azure-sql-database"></a>Database SQL di Azure

Il database SQL di Azure collegato a Blockchain Workbench archivia le definizioni dei contratti, i metadati di configurazione e una replica accessibile da SQL dei dati archiviati nella blockchain. È possibile visualizzare, analizzare ed eseguire query su questi dati in tutta semplicità accedendo direttamente al database. Gli sviluppatori e altri utenti possono usare il database per la creazione di report, l'analisi o altre integrazioni basate sui dati. Ad esempio, gli utenti possono visualizzare i dati delle transazioni tramite Power BI.

Questa risorsa di archiviazione off chain permette alle organizzazioni di eseguire query sui dati in SQL anziché in un libro mastro della blockchain. Inoltre, standardizzando in base a uno schema standard agnostico gli stack della tecnologia blockchain, la risorsa di archiviazione off-chain permette il riutilizzo dei report e di altri elementi in più progetti, scenari e organizzazioni.

## <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure viene usato per archiviare contratti e metadati associati ai contratti.

Dagli ordini di acquisto alle bolle di accompagnamento, dalle immagini usate nei settori medico e delle notizie ai video derivati da un flusso continuo, tra cui le fotocamere degli organismi di polizia e i film delle grandi case di produzione, i documenti hanno un ruolo in molti scenari basati su blockchain. I documenti non sono elementi appropriati da inserire direttamente nella blockchain.

Blockchain Workbench supporta la possibilità di aggiungere documenti o altro contenuto multimediale con logica di business basata su blockchain. Un hash del documento o del contenuto multimediale viene archiviato nella blockchain e l'effettivo documento o contenuto viene archiviato in Archiviazione di Azure. Le informazioni sulle transazioni associate vengono inviate al broker di messaggi in ingresso, incluse in un pacchetto, firmate e instradate alla blockchain. Questo processo attiva eventi, che vengono condivisi tramite il broker di messaggi in uscita. Il database SQL utilizza queste informazioni e le invia al database per la successiva esecuzione di query. Anche i sistemi a valle possono utilizzare questi eventi per operare nel modo appropriato.

## <a name="monitoring"></a>Monitoraggio

Workbench offre la registrazione delle applicazioni tramite Application Insights e Monitoraggio di Azure. Application Insights viene usato per archiviare tutte le informazioni registrate da Blockchain Workbench e include errori, avvisi e operazioni riuscite. Application Insights può essere usato dagli sviluppatori per eseguire il debug di problemi relativi a Blockchain Workbench. 

Monitoraggio di Azure fornisce informazioni sull'integrità della rete blockchain. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)