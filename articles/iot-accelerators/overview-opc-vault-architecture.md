---
title: Architettura di OPC Vault - Azure | Microsoft Docs
description: Architettura del servizio di gestione certificati OPC Vault
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200153"
---
# <a name="opc-vault-architecture"></a>Architettura OPC Vault

Questo articolo offre una panoramica del microservizio OPC Vault e del modulo OPC Vault IoT Edge.

Le applicazioni OPC UA usano i certificati di istanza dell'applicazione per fornire la sicurezza a livello di applicazione. Viene stabilita una connessione sicura tramite crittografia asimmetrica, per cui i certificati delle applicazioni forniscono la coppia di chiavi pubblica e privata. I certificati possono essere autofirmati oppure firmati da un'autorità di certificazione (CA).

Un'applicazione OPC UA include un elenco di certificati attendibili che rappresenta le applicazioni considerate attendibili. Questi certificati possono essere autofirmati o firmati da una CA oppure possono essere essi stessi un'autorità di certificazione radice o un'autorità di certificazione secondaria. Se un certificato attendibile fa parte di una catena di certificati più ampia, l'applicazione considera attendibili tutti i certificati concatenati al certificato nell'elenco di attendibilità, purché sia possibile convalidare la catena di certificati completa.

La differenza principale tra l'attendibilità dei certificati autofirmati e l'attendibilità di un certificato della CA consiste nell'attività di installazione necessaria per distribuire e mantenere l'attendibilità e nell'attività aggiuntiva per ospitare una CA specifica della società. 

Per distribuire l'attendibilità per i certificati autofirmati per più server con una singola applicazione client, è necessario installare tutti i certificati dell'applicazione server nell'elenco di attendibilità dell'applicazione client. È anche necessario installare il certificato dell'applicazione client in tutti gli elenchi di attendibilità dell'applicazione server. Questa attività amministrativa è piuttosto complessa e lo diventa ancora di più quando è necessario prendere in considerazione la durata dei certificati e rinnovarli.

L'uso di una CA specifica della società può semplificare notevolmente la gestione dell'attendibilità con più server e client. In questo caso, l'amministratore genera un certificato di istanza dell'applicazione firmato dalla CA una sola volta per ogni client e server usati. Inoltre, il certificato della CA viene installato in ogni elenco di attendibilità delle applicazioni, in tutti i server e i client. Con questo approccio è necessario rinnovare solo i certificati scaduti e sostituirli per le applicazioni interessate.

Il servizio di gestione dei certificati OPC UA di Azure Industrial IoT consente di gestire una CA specifica della società per le applicazioni OPC UA. Questo servizio è basato sul microservizio OPC Vault. OPC Vault fornisce un microservizio per ospitare una CA specifica della società in un cloud protetto. Questa soluzione è supportata da servizi protetti da Azure Active Directory (Azure AD), Azure Key Vault con i moduli di protezione hardware (HSM), Azure Cosmos DB e, facoltativamente, l'hub IoT come archivio applicazioni.

Il microservizio OPC Vault è progettato per supportare il flusso di lavoro basato sui ruoli, in cui gli amministratori e i responsabili approvazione della sicurezza con diritti di firma in Azure Key Vault approvano o rifiutano le richieste.

Per garantire la compatibilità con le soluzioni OPC UA esistenti, i servizi includono il supporto per un modulo Edge basato su un microservizio OPC Vault. In questo modo viene implementata l'interfaccia del **server di individuazione globale e gestione certificati di OPC UA** per la distribuzione di certificati ed elenchi di attendibilità conformemente alla parte 12 della specifica. 


## <a name="architecture"></a>Architettura

L'architettura è basata sul microservizio OPC Vault con un modulo IoT Edge OPC Vault per la rete factory e un'esperienza utente Web di esempio per controllare il flusso di lavoro:

![Diagramma dell'architettura di OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Microservizio OPC Vault

Il microservizio OPC Vault è costituito dalle interfacce seguenti per implementare il flusso di lavoro che consente di distribuire e gestire una CA specifica della società per le applicazioni OPC UA.

### <a name="application"></a>Applicazione 
- Un'applicazione OPC UA può essere un server, un client o entrambi. OPC Vault funge in questo caso da autorità di registrazione delle applicazioni. 
- Oltre alle operazioni di base per la registrazione, l'aggiornamento e l'annullamento della registrazione delle applicazioni, sono disponibili anche interfacce per trovare ed eseguire query per le applicazioni con espressioni di ricerca. 
- Le richieste di certificati devono fare riferimento a un'applicazione valida per essere elaborate e perché venga emesso un certificato firmato con tutte le estensioni specifiche di OPC UA. 
- Il servizio dell'applicazione è supportato da un database in Azure Cosmos DB.

### <a name="certificate-group"></a>Gruppo di certificati
- Un gruppo di certificati è un'entità che archivia un certificato di CA radice o di CA secondaria, inclusa la chiave privata per la firma dei certificati. 
- La lunghezza della chiave RSA, la lunghezza dell'hash SHA-2 e le durate sono configurabili sia per la CA emittente che per i certificati firmati delle applicazioni. 
- I certificati della CA vengono archiviati in Azure Key Vault, supportato da moduli di protezione hardware FIPS 140-2 livello 2. La chiave privata non lascia mai l'archivio protetto perché la firma viene eseguita tramite un'operazione di Key Vault protetta da Azure AD. 
- È possibile rinnovare i certificati della CA nel tempo e fare in modo che rimangano nell'archivio sicuro a causa della cronologia di Key Vault. 
- Anche l'elenco di revoche per ogni certificato della CA viene archiviato in Key Vault come segreto. Quando la registrazione di un'applicazione viene annullata, un amministratore revoca anche il certificato dell'applicazione nell'elenco di revoche di certificati.
- È possibile revocare singoli certificati, nonché certificati in batch.

### <a name="certificate-request"></a>Richiesta di certificato
Una richiesta di certificato implementa il flusso di lavoro per generare una nuova coppia di chiavi o un certificato firmato usando una richiesta di firma del certificato per un'applicazione OPC UA. 
- La richiesta viene archiviata in un database unitamente a informazioni associate, tra cui il soggetto o una richiesta di firma del certificato, oltre a un riferimento all'applicazione OPC UA. 
- La logica di business nel servizio convalida la richiesta in base alle informazioni archiviate nel database dell'applicazione. Ad esempio, l'URI dell'applicazione nel database deve corrispondere all'URI dell'applicazione nella richiesta di firma del certificato.
- Un amministratore della sicurezza con diritti di firma (ovvero il ruolo responsabile approvazione) approva o rifiuta la richiesta. Se la richiesta viene approvata, vengono generati una nuova coppia di chiavi e/o un certificato firmato. La nuova chiave privata viene archiviata in modo sicuro in Key Vault e il nuovo certificato pubblico firmato viene archiviato nel database di richieste di certificato.
- Il richiedente può eseguire il polling dello stato della richiesta fino a quando non viene approvata o revocata. Se la richiesta è stata approvata, la chiave privata e il certificato possono essere scaricati e installati nell'archivio certificati dell'applicazione OPC UA.
- Il richiedente può a quel punto accettare la richiesta per eliminare le informazioni non necessarie dal database di richieste. 

Per tutta la durata di un certificato firmato, è possibile che un'applicazione venga eliminata o che una chiave venga compromessa. In tal caso, un responsabile della CA può:
- Eliminare un'applicazione, eliminando anche tutte le richieste di certificato in sospeso e approvate dell'app. 
- Eliminare solo una singola richiesta di certificato se viene rinnovata o compromessa una sola chiave.

A quel punto le richieste di certificato approvate e accettate vengono contrassegnate come eliminate.

Un responsabile può rinnovare regolarmente l'elenco di revoche di certificati della CA emittente. Al momento del rinnovo, tutte le richieste di certificato eliminate vengono revocate e i numeri di serie del certificato vengono aggiunti all'elenco di revoche di certificati. Le richieste di certificato revocate vengono contrassegnate come revocate. In caso di urgenza, è possibile revocare anche richieste di certificati singoli.

Infine, gli elenchi di revoche di certificati aggiornati sono disponibili per la distribuzione ai client e ai server OPC UA partecipanti.

## <a name="opc-vault-iot-edge-module"></a>Modulo OPC Vault IoT Edge
Per supportare un server di individuazione globale della rete factory, è possibile distribuire il modulo OPC Vault nella rete perimetrale. Eseguirlo come un'applicazione .NET Core locale oppure avviarlo in un contenitore Docker. Dal momento che il supporto per l'autenticazione Auth2 nello stack .NET Standard corrente di OPC UA non è disponibile, la funzionalità del modulo Edge OPC Vault è limitata a un ruolo di lettore. Un utente non può essere rappresentato dal modulo Edge al microservizio con l'interfaccia GDS standard di OPC UA.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito informazioni sull'architettura di OPC Vault, è possibile:

> [!div class="nextstepaction"]
> [Creare e distribuire OPC Vault](howto-opc-vault-deploy.md)
