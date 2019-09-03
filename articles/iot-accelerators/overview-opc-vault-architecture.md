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
ms.openlocfilehash: 9331473402ddd22180df3b404824969360d48164
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995824"
---
# <a name="opc-vault-architecture"></a>Architettura OPC Vault

Questo articolo fornisce una panoramica del **microservizio OPC Vault** e del modulo **OPC Vault IoT Edge**.

## <a name="overview"></a>Panoramica

Le applicazioni OPC UA usano gli Application Instance Certificate per fornire la sicurezza a livello di applicazione. Viene stabilita una connessione sicura tramite crittografia asimmetrica, per cui i certificati delle applicazioni forniscono la coppia di chiavi pubblica e privata. I certificati possono essere autofirmati oppure firmati da un'autorità di certificazione (CA).

Le applicazioni OPC UA includono un elenco di certificati attendibili che rappresentano le applicazioni considerate attendibili. Questi certificati possono essere autofirmati o firmati da una CA oppure possono essere essi stessi un'autorità di certificazione radice o un'autorità di certificazione secondaria. Se un certificato attendibile fa parte di una catena di certificati più ampia, l'applicazione considera attendibili tutti i certificati concatenati al certificato nell'elenco di attendibilità, purché sia possibile convalidare la catena di certificati completa.

La differenza principale tra l'attendibilità dei certificati autofirmati e l'attendibilità di un certificato della CA consiste nell'attività di installazione necessaria per distribuire e mantenere l'attendibilità e nell'attività aggiuntiva per ospitare una CA specifica della società. 

Per distribuire l'attendibilità per i certificati autofirmati per n server con una singola applicazione client, è necessario installare tutti gli n certificati di applicazioni server nell'elenco di attendibilità di applicazioni client. Inoltre, il certificato di applicazioni client deve essere installato in tutti gli elenchi di attendibilità di applicazioni server. Questa attività amministrativa è piuttosto complessa e lo diventa ancora di più quando è necessario prendere in considerazione la durata dei certificati e rinnovarli.

L'uso di una CA specifica della società può semplificare notevolmente la gestione dell'attendibilità con più server e client. In questo caso, l'amministratore genera un Application Instance Certificate firmato dalla CA una sola volta per ogni client e server usato. Inoltre, il certificato della CA viene installato in ogni elenco di attendibilità delle applicazioni, in tutti i server e i client. Con questo approccio è necessario rinnovare solo i certificati scaduti e sostituirli per le applicazioni interessate.

Il servizio di gestione dei certificati OPC UA di Azure Industrial IoT è la soluzione ideale per gestire una CA specifica dell'azienda per le applicazioni OPC UA, basata sul microservizio OPC Vault.

OPC Vault fornisce un microservizio per ospitare una CA specifica della società in un cloud protetto, supportato da servizi protetti di Azure AD con Azure Key Vault con moduli di sicurezza hardware, Cosmos DB e, facoltativamente, anche l'hub IoT come archivio applicazioni.

Il microservizio OPC Vault è progettato per supportare il flusso di lavoro basato sui ruoli, in cui il personale OT richiede certificati di applicazioni firmati, mentre gli amministratori e i responsabili approvazione della sicurezza con diritti di firma in Azure Key Vault approvano o rifiutano tali richieste.

Per la compatibilità con le attuali soluzioni OT basate su GDS di OPC UA, i servizi includono il supporto per un modulo perimetrale basato supportato dal microservizio OPC Vault, che implementa l'interfaccia *OPC UA Global Discovery Server and Certificate Management* per distribuire certificati ed elenchi di attendibilità in base alla parte 12 della specifica. Tuttavia, per quanto ne sappiamo, questa interfaccia del server GDS non è ancora ampiamente diffusa e ha ancora funzionalità limitate (ruolo lettore). [Su richiesta dei clienti, miglioreremo l'esperienza (*)](#yet-unsupported-features).

## <a name="architecture"></a>Architettura

L'architettura è basata sul microservizio OPC Vault con un modulo IoT Edge OPC Vault per la rete di factory e un'esperienza utente Web di esempio per controllare il flusso di lavoro:

![Architettura di OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Microservizio OPC Vault

Il microservizio OPC Vault è costituito dalle interfacce seguenti per implementare il flusso di lavoro per distribuire e gestire una CA specifica della società per le applicazioni OPC UA:

### <a name="application"></a>Applicazione 
- Un'"applicazione OPC UA" può essere un server, un client o entrambi. OPC Vault funge in questo caso da autorità di registrazione delle applicazioni. 
- Oltre alle operazioni di base per la registrazione, l'aggiornamento e l'annullamento della registrazione delle applicazioni, sono disponibili anche interfacce per trovare ed eseguire query per le applicazioni con espressioni di ricerca. 
- Le richieste di certificati devono fare riferimento a un'applicazione valida per essere elaborate e perché venga emesso un certificato firmato con tutte le estensioni specifiche di OPC UA. 
- Il servizio dell'applicazione è supportato da un database CosmosDB o dal [registro dispositivi OpcTwin (*)](#yet-unsupported-features), a seconda della configurazione del cliente.

### <a name="certificate-group"></a>Gruppo di certificati
- Un gruppo di certificati è un'entità che archivia un certificato di CA radice o di CA secondaria, inclusa la chiave privata per la firma dei certificati. 
- La lunghezza della chiave RSA, la lunghezza dell'hash SHA-2 e le durate sono configurabili sia per la CA emittente che per i certificati firmati delle applicazioni. 
- In un singolo servizio possono essere ospitati più gruppi per consentire estensioni future con i gruppi di certificati dell'algoritmo HTTPS, User o ECC [(*)](#yet-unsupported-features). 
- I certificati della CA vengono archiviati in Azure Key Vault, supportato da moduli di protezione hardware FIPS 140-2 livello 2. La chiave privata non lascia mai l'archivio protetto perché la firma viene eseguita tramite un'operazione protetta di Key Vault di Azure AD. 
- I certificati della CA possono essere rinnovati nel corso del tempo e rimangono comunque nell'archivio sicuro a causa della cronologia di Key Vault. 
- Anche l'elenco di revoche per ogni certificato della CA viene archiviato in Key Vault come segreto. Una volta annullata la registrazione di un'applicazione, un amministratore revoca anche il certificato nell'elenco di revoche di certificati.
- È supportata la revoca di certificati singoli e in batch.

### <a name="certificate-request"></a>Richiesta di certificato
Una richiesta di certificato implementa il flusso di lavoro per generare una nuova coppia di chiavi o un certificato firmato usando una richiesta di firma del certificato per un'applicazione OPC UA. 
- La richiesta viene archiviata in un database con informazioni associate come soggetto o richiesta di firma del certificato e un riferimento all'applicazione OPC UA. 
- La logica di business nel servizio convalida la richiesta in base alle informazioni archiviate nel database dell'applicazione. Ad esempio, l'URI dell'applicazione nel database deve corrispondere all'URI dell'applicazione nella richiesta di firma del certificato.
- Un amministratore della sicurezza con diritti di firma (ruolo responsabile approvazione) approva o rifiuta la richiesta. Se la richiesta viene approvata, vengono generati una nuova coppia di chiavi e/o un certificato firmato. La nuova chiave privata viene archiviata in modo sicuro in Key Vault, mentre il nuovo certificato pubblico firmato viene archiviato nel database di richieste di certificato.
- Il richiedente può eseguire il polling dello stato della richiesta fino a quando non viene approvata o revocata. Se la richiesta è stata approvata, la chiave privata e il certificato possono essere scaricati e installati nell'archivio certificati dell'applicazione OPC UA.
- Il richiedente può a quel punto accettare la richiesta per eliminare le informazioni non necessarie dal database di richieste. 

Per tutta la durata di un certificato firmato, è possibile che un'applicazione venga eliminata o che una chiave venga compromessa. In tal caso, un responsabile della CA può:
- Eliminare un'applicazione, eliminando allo stesso tempo anche tutte le richieste di certificato in sospeso e approvate dell'app. 
- Un'altra opzione consiste nell'eliminare solo una singola richiesta di certificato se viene rinnovata o compromessa una sola chiave.
- A quel punto le richieste di certificato approvate e accettate vengono contrassegnate come eliminate.
- Un responsabile può eseguire regolarmente un rinnovo dell'elenco di revoche di certificati della CA emittente. Al momento del rinnovo, tutte le richieste di certificato eliminate vengono revocate e i numeri di serie del certificato vengono aggiunti all'elenco di revoche di certificati. Le richieste di certificato revocate vengono contrassegnate come revocate.
- In caso di urgenza, è possibile revocare anche richieste di certificati singoli.
- Infine, gli elenchi di revoche di certificati aggiornati sono disponibili per la distribuzione ai client e ai server OPC UA partecipanti.

Per altre informazioni sull'API del microservizio OPC Vault, vedere la documentazione Swagger associata.

## <a name="opc-vault-iot-edge-module"></a>Modulo OPC Vault IoT Edge
Per supportare un Global Discovery Server della rete di factory, il modulo OPC Vault può essere distribuito nella rete perimetrale, eseguito come applicazione .NET Core locale o avviato in un contenitore Docker. A causa della mancanza di supporto per l'autenticazione Auth2 nello stack OPC UA .NET Standard corrente, la funzionalità del modulo Edge OPC Vault è limitata a un ruolo di lettore, perché un utente non può essere rappresentato dal modulo Edge al microservizio con l'interfaccia GDS standard di OPC UA. A questo punto sono consentite solo le operazioni che non richiedono il ruolo di writer, amministratore o responsabile approvazione[(*)](#yet-unsupported-features). 

## <a name="yet-unsupported-features"></a>Funzionalità tuttora non supportate

**(*)** non ancora supportato.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito informazioni sull'architettura di OPC Vault, ecco il prossimo passaggio suggerito:

> [!div class="nextstepaction"]
> [Creare e distribuire OPC Vault](howto-opc-vault-deploy.md)
