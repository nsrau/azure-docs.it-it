---
title: Gestire Azure Cosmos DB in Azure Storage Explorer
description: Informazioni su come gestire Azure Cosmos DB in Azure Storage Explorer.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Guida alla risoluzione dei problemi di Azure Cosmos DB in Storage Explorer | Microsoft Docs

[Azure Cosmos DB in Azure Storage Explorer](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) è un'app autonoma che consente di connettersi ad account di Azure Cosmos DB ospitati in Azure e a cloud sovrani forniti da Windows, macOS o Linux. Consente inoltre agli utenti di gestire le entità di Azure Cosmos DB, modificare i dati, aggiornare le stored procedure e attivarle assieme ad altre entità come code e BLOB di archiviazione di Azure.

In questa guida sono riepilogate le soluzioni per gli errori comuni riscontrati in Azure Cosmos DB in Storage Explorer.

- [Problemi relativi all'accesso](#Sign-in-issues)
  - [Certificato autofirmato nella catena di certificati](#Self-signed-certificate-in-certificate-chain)
  - [Impossibile recuperare le sottoscrizioni](#Unable-to-retrieve-subscriptions)
  - [Impossibile visualizzare la pagina di autenticazione](#Unable-to-see-the-authentication-page)
  - [Impossibile rimuovere l'account](#Cannot-remove-account)
- [Problema del proxy Http/Https](#Http/Https-proxy-issue)
- [Problema relativo al nodo "Sviluppo" in "Local and Attached" (Locale e collegato)](#Development-node-under-Local-and-Attached-node-issue)
- [Errore di collegamento dell'account di Azure Cosmos DB nel nodo "Local and Attached" (Locale e collegato)](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Errore di espansione del nodo di Azure Cosmos DB](#Expand-Azure-Cosmos-DB-node-error)
- [Passaggi successivi](#Next-steps)

<h2 id="Sign-in-issues">Problemi relativi all'accesso</h2>

Prima di continuare, provare a riavviare l'applicazione per vedere se i problemi si risolvono.

<h2 id="Self-signed-certificate-in-certificate-chain">Certificato autofirmato nella catena di certificati</h2>

Questo errore può essere visualizzato per una serie di motivi, tra cui i due descritti di seguito:

1. Si è protetti da un "proxy trasparente", ovvero qualcuno, ad esempio il reparto IT, intercetta il traffico HTTPS, ne esegue la decrittografia e quindi la crittografia con un certificato autofirmato.

2. Viene eseguito un software, ad esempio un'applicazione antivirus, che inserisce un certificato SSL autofirmato nei messaggi HTTPS ricevuti.

Quando Storage Explorer rileva uno di questi "certificati autofirmati", è possibile che l'informazione relativa alla possibile manomissione del messaggio HTTPS ricevuto non sia nota. Se tuttavia si dispone di una copia del certificato autofirmato, è possibile indicare a Storage Explorer di considerarlo attendibile. Se non si conosce l'autore dell'inserimento del certificato, è possibile provare a trovarlo seguendo questa procedura:

1. Installare Open SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html), è sufficiente una delle versioni light
     - Mac e Linux: devono essere inclusi nel sistema operativo
2. Eseguire Open SSL
    - Windows: passare alla directory di installazione, alla directory **/bin/** e quindi fare doppio clic su **openssl.exe**.
    - Mac e Linux: eseguire **openssl** da un terminale
3. Eseguire `s_client -showcerts -connect microsoft.com:443`
4. Cercare i certificati autofirmati. Se non si sa con certezza quali certificati sono autofirmati, verificare ovunque che l'oggetto ("s") e l'autorità emittente ("i:") siano uguali.
5.  Dopo aver trovato i certificati autofirmati, per ognuno di essi copiare e incollare tutto il testo da **-----BEGIN CERTIFICATE-----** a **-----END CERTIFICATE-----** (inclusi) in un nuovo file con estensione CER.
6.  Aprire Storage Explorer e quindi passare a **Modifica** > **Certificati SSL** > **Importa certificati**. Usando lo strumento di selezione file, trovare, selezionare e aprire i file con estensione CER creati.

Se non è possibile trovare alcun certificato autofirmato seguendo la procedura precedente, usare lo strumento di feedback per ricevere assistenza.

<h2 id="Unable-to-retrieve-subscriptions">Impossibile recuperare le sottoscrizioni</h2>

Se non è possibile recuperare le sottoscrizioni dopo aver effettuato l'accesso:

- Verificare che l'account abbia accesso alle sottoscrizioni effettuando l'accesso al [portale di Azure](http://portal.azure.com/)
- Assicurarsi di aver effettuato l'accesso usando l'ambiente corretto, ad esempio [Azure](http://portal.azure.com/), [Azure Cina](https://portal.azure.cn/), [Azure Germania](https://portal.microsoftazure.de/), [Azure Governo degli Stati Uniti](http://portal.azure.us/) o Ambiente personalizzato/Azure Stack
- Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia stato configurato correttamente
- Provare a rimuovere e a aggiungere nuovamente l'account
- Provare a eliminare i file seguenti dalla home directory, ovvero C:\Utenti\ContosoUser, e quindi aggiungere nuovamente l'account:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Controllare la console degli strumenti per sviluppatori (F12) mentre si esegue l'accesso per i messaggi di errore

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">Impossibile visualizzare la pagina di autenticazione</h2>  

Se non è possibile visualizzare la pagina di autenticazione:

- A seconda della velocità di connessione, è possibile che il caricamento della pagina di accesso richieda un po' di tempo: attendere almeno un minuto prima di chiudere la finestra di dialogo di autenticazione
- Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia stato configurato correttamente
- Visualizzare la console per sviluppatori premendo il tasto F12. Controllare le risposte nella console per sviluppatori per trovare eventuali informazioni sul mancato funzionamento dell'autenticazione

<h2 id="Cannot-remove-account">Impossibile rimuovere l'account</h2>

Se non è possibile rimuovere un account o se il collegamento per eseguire nuovamente l'autenticazione non esegue alcuna operazione

- Provare a eliminare i file seguenti dalla home directory e quindi aggiungere nuovamente l'account:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Se si desidera rimuovere le risorse di archiviazione associate alla firma di accesso condiviso, eliminare:
  - la cartella %AppData%/StorageExplorer per Windows
  - /Utenti/<your_name>/Library/Applicaiton SUpport/StorageExplorer per Mac
  - ~/.config/StorageExplorer per Linux
  - Se si eliminano questi file, **è necessario immettere nuovamente tutte le credenziali**


<h2 id="Http/Https-proxy-issue">Problema del proxy Http/Https</h2>

Non è possibile elencare i nodi di Azure Cosmos DB nella struttura ad albero a sinistra quando si configura il proxy http/https in un ambiente del servizio app. È un problema noto e verrà risolto nella prossima versione. Per il momento, come soluzione alternativa è possibile usare Esplora dati di Azure Cosmos DB nel portale di Azure. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">Problema relativo al nodo "Sviluppo" in "Local and Attached" (Locale e collegato)</h2>

Non viene ricevuta alcuna risposta dopo aver fatto clic sul nodo "Sviluppo" all'interno del nodo "Local and Attached" (Locale e collegato) nella struttura ad albero a sinistra.  Si tratta di un comportamento previsto. L'emulatore locale di Azure Cosmos DB verrà supportato nella prossima versione.

![Nodo Sviluppo](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">Errore di collegamento dell'account di Azure Cosmos DB nel nodo "Local and Attached" (Locale e collegato)</h2>

Se dopo aver collegato l'account di Azure Cosmos DB nel nodo "Local and Attached" (Locale e collegato) viene visualizzato l'errore seguente, verificare che si stia usando la stringa di connessione corretta.

![Errore di collegamento di Azure Cosmos DB in "Local and Attached" (Locale e collegato)](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Errore di espansione del nodo di Azure Cosmos DB</h2>

Questo errore può essere visualizzato mentre si tenta di espandere i nodi della struttura ad albero a sinistra. 

![Errore di espansione](./media/troubleshoot-cosmosdb/expand-error.png)

Attenersi ai suggerimenti seguenti:

- Controllare che non sia in corso il provisioning dell'account di Azure Cosmos DB e riprovare dopo aver creato correttamente l'account.
- Se l'account si trova sotto il nodo "Accesso rapido" o "Local and Attached" (Locale e collegato), controllare che l'account non sia stato eliminato. In questo caso, è necessario rimuovere manualmente il nodo.

<h2 id="Next-steps">Passaggi successivi</h2>

Se nessuna delle soluzioni proposte ha funzionato, inviare un'e-mail all'Azure Cosmos DB Dev Tooling Team ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) specificando i dettagli del problema per favorirne la risoluzione.





