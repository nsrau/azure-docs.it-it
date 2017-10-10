---
title: Guida alla risoluzione dei problemi di Azure Storage Explorer | Microsoft Docs
description: "Panoramica delle due funzionalità di debug di Azure"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: e06c73c2c00b27178f8431b83b5c5a42110b6b1e
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---

# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Azure Storage Explorer

Microsoft Azure Storage Explorer (anteprima) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux. L'app può connettersi ad account di archiviazione ospitati in Azure, Sovereign Clouds e Azure Stack.

In questa guida sono riepilogate le soluzioni per gli errori comuni riscontrati in Storage Explorer.

## <a name="sign-in-issues"></a>Problemi relativi all'accesso

Sono supportati solo account AAD (Azure Active Directory). Se si usa un account ADFS, è probabile che l'accesso a Storage Explorer non funzioni. Prima di continuare, provare a riavviare l'applicazione per vedere se i problemi si risolvono.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>Errore: certificato autofirmato nella catena di certificati

Esistono diversi motivi per cui è possibile riscontrare questo errore. I due motivi più comuni sono i seguenti:

1. L'app è connessa tramite un "proxy trasparente", ovvero un server, ad esempio il server aziendale, che intercetta il traffico HTTPS, ne esegue la decrittografia e la crittografia con un certificato autofirmato.

2. Viene eseguita un'applicazione, ad esempio un software antivirus, che inserisce un certificato SSL autofirmato nei messaggi HTTPS ricevuti.

Quando Storage Explorer rileva un problema, è possibile che l'informazione relativa alla possibile manomissione del messaggio HTTPS ricevuto non sia nota. Se si dispone di una copia del certificato autofirmato, è possibile fare in modo che Storage Explorer li consideri attendibili. Se non si conosce l'autore dell'inserimento del certificato, seguire questi passaggi per trovarlo:

1. Installare Open SSL

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html), una delle versioni light dovrebbe essere sufficiente

    - Mac e Linux: devono essere inclusi nel sistema operativo

2. Eseguire Open SSL

    - Windows: aprire la directory di installazione, fare clic su **/bin/**, quindi fare doppio clic su **openssl.exe**.
    - Mac e Linux: eseguire **openssl** da un terminale.

3. Eseguire s_client -showcerts -connect microsoft.com:443

4. Cercare i certificati autofirmati. Se si è certi di quali certificati sono autofirmati, verificare ovunque che l'oggetto ("s") e l'autorità emittente ("i:") siano uguali.

5. Dopo aver trovato i certificati autofirmati, per ognuno di essi, copiare e incollare tutto da e includendo **-----BEGIN CERTIFICATE-----** a **-----END CERTIFICATE-----** in un nuovo file con estensione CER.

6. Aprire Storage Explorer, fare clic su **Modifica** > **Certificati SSL** > **Importa certificati**, quindi usare la selezione file per trovare, selezionare e aprire i file con estensione CER creati.

Se non è possibile trovare alcun certificato autofirmato seguendo i passaggi precedenti, contattare Microsoft tramite lo strumento di feedback per ricevere assistenza.

### <a name="unable-to-retrieve-subscriptions"></a>Impossibile recuperare le sottoscrizioni

Se non è possibile recuperare le sottoscrizioni dopo aver eseguito correttamente l'accesso, seguire questi passaggi per risolvere questo problema:

- Verificare che l'account abbia accesso alle sottoscrizioni effettuando l'accesso al portale di Azure.

- Assicurarsi di aver effettuato l'accesso usando l'ambiente corretto, ad esempio Azure, Azure Cina, Azure Germania, Azure Governo degli Stati Uniti o Ambiente personalizzato/Azure Stack.

- Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia stato configurato correttamente.

- Provare a rimuovere e a aggiungere nuovamente l'account.

- Provare a eliminare i file seguenti dalla directory radice, ovvero, C:\Utenti\ContosoUser, e quindi aggiungere nuovamente l'account:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Controllare la console degli strumenti per sviluppatori premendo F12 quando si esegue l'accesso per i messaggi di errore:

![strumenti per sviluppatori](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>Impossibile visualizzare la pagina di autenticazione

Se non si riesce a visualizzare la pagina di autenticazione, seguire questi passaggi per risolvere questo problema:

- A seconda della velocità della connessione, il caricamento della pagina di accesso potrebbe richiedere un po' di tempo, attendere almeno un minuto prima di chiudere la finestra di dialogo di autenticazione.

- Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia stato configurato correttamente.

- Visualizzare la console per sviluppatori premendo il tasto F12. Controllare le risposte nella console per sviluppatori per trovare informazioni sul mancato funzionamento dell'autenticazione.

### <a name="cannot-remove-account"></a>Impossibile rimuovere l'account

Se non è possibile rimuovere un account o se il collegamento per eseguire nuovamente l'autenticazione non esegue alcuna operazione, attenersi alla seguente procedura per risolvere questo problema:

- Provare a eliminare i file seguenti dalla directory radice e quindi aggiungere nuovamente l'account:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Se si desidera rimuovere le risorse di archiviazione collegate al SAS, eliminare i file seguenti:

    - la cartella %AppData%/StorageExplorer per Windows

    - /Utenti/<your_name>/Library/Applicaiton SUpport/StorageExplorer per Mac

    - ~/.config/StorageExplorer per Linux

> [!NOTE]
>  Se si eliminano questi file è necessario immettere nuovamente tutte le credenziali.

## <a name="proxy-issues"></a>Problemi di proxy

In primo luogo, assicurarsi che le informazioni seguenti immesse siano corrette:

- l'URL del proxy e il numero di porta

- nome utente e password se richiesto dal proxy

### <a name="common-solutions"></a>soluzioni comuni

Se si verificano ancora problemi, attenersi alla procedura seguente per risolverli:

- Se è possibile connettersi a Internet senza usare il proxy, verificare che Storage Explorer funzioni senza le impostazioni del proxy abilitate. In questo caso, potrebbe esserci un problema con le impostazioni del proxy. Rivolgersi all'amministratore del proxy per identificare i problemi.

- Verificare che altre applicazioni che usano il server proxy funzionino come previsto.

- Verificare che sia possibile connettersi al portale di Microsoft Azure usando il Web browser

- Verificare di poter ricevere le risposte dagli endpoint del servizio. Immettere uno degli URL dell'endpoint nel browser. Se è possibile connettersi, si dovrebbe ricevere InvalidQueryParameterValue o una risposta XML simile.

- Se anche altre persone usano Storage Explorer con il server proxy, accertarsi che riescano a connettersi. Se questo non avviene, potrebbe essere necessario contattare l'amministratore del server proxy.

### <a name="tools-for-diagnosing-issues"></a>Strumenti per la diagnosi dei problemi

Se si dispone di strumenti di rete, ad esempio Fiddler per Windows, è possibile diagnosticare i problemi come indicato di seguito:

- Se si deve usare il proxy, è necessario configurare lo strumento di rete per connettersi tramite il proxy.

- Controllare il numero della porta usato dallo strumento di rete.

- Immettere l'URL dell'host locale e il numero della porta dello strumento di rete come impostazioni proxy in Storage Explorer. Se questa operazione viene eseguita correttamente, lo strumento di rete inizia la registrazione delle richieste di rete effettuate da Storage Explorer agli endpoint di gestione e del servizio. Ad esempio, immettere https://cawablobgrs.blob.core.windows.net/ per l'endpoint BLOB in un browser. Si riceverà una risposta simile alla seguente, che suggerisce che la risorsa è disponibile, anche se non è possibile accedervi.

![esempio di codice](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Contattare l'amministratore del server proxy

Se le impostazioni del proxy sono corrette, è necessario contattare l'amministratore del server proxy e

- Assicurarsi che il proxy non blocchi il traffico agli endpoint di gestione o risorse di Azure.

- Verificare il protocollo di autenticazione usato dal server proxy. Storage Explorer attualmente non supporta i proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Messaggio di errore "Unable to Retrieve Children" (Impossibile recuperare gli elementi figlio)

Se si è connessi ad Azure tramite un proxy, verificare che le impostazioni del proxy siano corrette. Se è stato concesso l'accesso a una risorsa dal proprietario della sottoscrizione o dell'account, verificare di avere letto o elencare le autorizzazioni per tale risorsa.

### <a name="issues-with-sas-url"></a>Problemi relativi all'URL SAS
Se ci si connette a un servizio tramite un URL SAS e si verifica questo errore:

- Verificare che l'URL abbia le autorizzazioni necessarie per la lettura o l'elenco delle risorse.

- Verificare che l'URL non sia scaduto.

- Se l'URL SAS si basa su un criterio di accesso, verificare che i criteri di accesso non siano stati revocati.

## <a name="next-steps"></a>Passaggi successivi

Se nessuna delle soluzioni funziona, comunicare il problema tramite lo strumento di feedback inserendo l'indirizzo di posta elettronica e il maggior numero di informazioni possibili, in modo che Microsoft possa contattare l'utente per risolvere il problema.

A tale scopo, fare clic sul menu **Guida** e quindi fare clic su **Commenti e suggerimenti**.

![Commenti e suggerimenti](./media/storage-explorer-troubleshooting/4022503_en_1.png)

