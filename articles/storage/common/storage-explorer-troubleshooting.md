---
title: Guida alla risoluzione dei problemi di Azure Storage Explorer | Microsoft Docs
description: Panoramica delle due funzionalità di debug di Azure
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.subservice: common
ms.openlocfilehash: 180780c3a3a644a8da0fa544c37bc8cd252c982f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469499"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Azure Storage Explorer

Microsoft Azure Storage Explorer è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux. L'app può connettersi ad account di archiviazione ospitati in Azure, cloud nazionali e Azure Stack.

In questa guida sono riepilogate le soluzioni per gli errori comuni riscontrati in Storage Explorer.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Errore: certificato autofirmato nella catena di certificati (ed errori simili)

Gli errori di certificato sono causati da una delle due situazioni seguenti:

1. L'app è connessa tramite un "proxy trasparente": questo significa che un server (ad esempio, un server aziendale) intercetta il traffico HTTPS, ne esegue la decrittografia e la crittografia con un certificato autofirmato.
2. Viene eseguita un'applicazione che inserisce un certificato SSL autofirmato nei messaggi HTTPS ricevuti. Esempi di applicazioni che inseriscono certificati includono il software antivirus e per l'ispezione del traffico di rete.

In presenza di un certificato autofirmato o non attendibile, Storage Explorer non è più in grado di stabilire se il messaggio HTTPS ricevuto è stato modificato. Se è disponibile una copia del certificato autofirmato, è possibile fare in modo che Storage Explorer lo consideri attendibile seguendo questa procedura:

1. Ottenere una copia del certificato X.509 con codifica Base64 (file con estensione cer) del certificato
2. Fare clic su **Modifica** > **Certificati SSL** > **Importa certificati** e quindi usare la selezione file per trovare, selezionare e aprire il file con estensione cer.

Il problema potrebbe anche essere il risultato di più certificati (radice e intermedi). Per risolvere l'errore, è necessario aggiungere entrambi i certificati.

In caso di dubbi sulla provenienza del certificato, è possibile provare questa procedura:

1. Installare Open SSL

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html), una delle versioni light dovrebbe essere sufficiente
    * Mac e Linux: devono essere inclusi nel sistema operativo
2. Eseguire Open SSL

    * Windows: aprire la directory di installazione, fare clic su **/bin/**, quindi fare doppio clic su **openssl.exe**.
    * Mac e Linux: eseguire **openssl** da un terminale.
3. Eseguire `s_client -showcerts -connect microsoft.com:443`
4. Cercare i certificati autofirmati. Se non si è certi di quali certificati sono autofirmati, verificare ovunque che il soggetto `("s:")` e l'autorità emittente `("i:")` siano uguali.
5. Dopo aver trovato i certificati autofirmati, per ognuno di essi, copiare e incollare tutto da e includendo **-----BEGIN CERTIFICATE-----** a **-----END CERTIFICATE-----** in un nuovo file con estensione CER.
6. Aprire Storage Explorer, fare clic su **Modifica** > **Certificati SSL** > **Importa certificati**, quindi usare la selezione file per trovare, selezionare e aprire i file con estensione CER creati.

Se non è possibile trovare alcun certificato autofirmato seguendo i passaggi precedenti, contattare Microsoft tramite lo strumento di feedback per ricevere assistenza. In alternativa, è possibile scegliere di avviare Storage Explorer dalla riga di comando con il flag `--ignore-certificate-errors`. Quando viene avviato con questo flag, Storage Explorer ignorerà gli errori del certificato.

## <a name="sign-in-issues"></a>Problemi di accesso

### <a name="reauthentication-loop-or-upn-change"></a>Ciclo di riautenticazione o modifica UPN
Se ci si trova in un ciclo di riautenticazione, o se l'UPN di uno degli account è stato modificato, procedere come segue:
1. Rimuovere tutti gli account e quindi chiudere Storage Explorer
2. Eliminare la cartella .IdentityService dal computer. In Windows, la cartella si trova in `C:\users\<username>\AppData\Local`. Per Mac e Linux, è possibile trovare la cartella nella radice della directory dell'utente.
3. Se si usa Mac o Linux, è necessario anche eliminare la voce Microsoft.Developer.IdentityService dall'archivio chiavi dei sistemi operativi. Su Mac, l'archivio chiavi è l'applicazione "Gnome Keychain". Per Linux, l'applicazione viene in genere chiamata "Keyring", ma il nome può variare a seconda della distribuzione.

### <a name="conditional-access"></a>Accesso condizionale
L'accesso condizionale non è supportato quando Storage Explorer è in uso in Windows 10, Linux o macOS. Ciò è dovuto a una limitazione della libreria di AAD usata da Storage Explorer.

## <a name="mac-keychain-errors"></a>Errori di Keychain su Mac
Il Keychain macOS può talvolta andare in uno stato che causa problemi alla libreria di autenticazione di Storage Explorer. Per togliere il keychain da questo stato provare la procedura seguente:
1. Chiudere Azure Storage Explorer.
2. Aprire il keychain (**cmd + barra spaziatrice**, digitare keychain, premere Invio).
3. Selezionare il keychain "login".
4. Fare clic sull'icona del lucchetto per bloccare il keychain (al termine il lucchetto assumerà un'animazione in posizione di blocco, cosa che potrebbe richiedere alcuni secondi in base a quali applicazioni sono aperte).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Avviare Azure Storage Explorer.
6. Dovrebbe essere visualizzato un menu a comparsa per segnalare qualcosa come "L'hub di servizio vuole accedere al keychain". Quando si verifica, immettere la password dell'account amministratore Mac e fare clic su **Consenti sempre** (o **Consenti** se **Consenti sempre** non è disponibile).
7. Provare a effettuare l'accesso.

### <a name="general-sign-in-troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi di accesso generale
* Se si sta usando macOS e la finestra di accesso non appare sulla finestra di dialogo "Waiting for authentication..." (In attesa di autenticazione...), provare [questa procedura](#mac-keychain-errors)
* Riavviare Storage Explorer
* Se la finestra di autenticazione è vuota, attendere almeno un minuto prima di chiudere la finestra di dialogo di autenticazione.
* Verificare che le impostazioni del proxy e dei certificati siano configurate correttamente per il computer e per Storage Explorer.
* Se si usa Windows e si ha accesso a Visual Studio 2017 nello stesso computer e con lo stesso account, provare ad accedere a Visual Studio 2017. Dopo aver eseguito correttamente l'accesso a Visual Studio 2017, dovrebbe essere possibile aprire Storage Explorer e visualizzare l'account nel pannello account.

Se nessuno di questi metodi funziona [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Sottoscrizioni mancanti e tenant interrotti

Se non è possibile recuperare le sottoscrizioni dopo aver eseguito correttamente l'accesso, provare i metodi di risoluzione dei problemi seguenti:

* Verificare che l'account abbia accesso alle sottoscrizioni previste. È possibile verificare di disporre dell'accesso effettuando l'accesso al portale per l'ambiente di Azure che si sta tentando di usare.
* Assicurarsi di aver effettuato l'accesso usando l'ambiente di Azure corretto, ad esempio Azure, Azure Cina, Azure Germania, Azure Governo degli Stati Uniti o Ambiente personalizzato.
* Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia stato configurato correttamente.
* Provare a rimuovere e a aggiungere nuovamente l'account.
* Se è presente un collegamento "Altre informazioni", esaminare e vedere quali messaggi di errore sono stati segnalati per i tenant con esito negativo. Se non si è certi su che cosa fare con i messaggi di errore visualizzati, è possibile [Segnalare un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Non è possibile rimuovere un account o una risorsa di archiviazione collegato

Se non si riesce a rimuovere un account o una risorsa di archiviazione collegato tramite l'interfaccia utente, è possibile eliminare manualmente tutte le risorse collegate eliminando le cartelle seguenti:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Chiudere Storage Explorer prima di eliminare le cartelle precedenti.

> [!NOTE]
>  Se sono stati importati certificati SSL, eseguire il backup del contenuto della directory `certs`. In un secondo momento, è possibile usare il backup per reimportare i certificati SSL.

## <a name="proxy-issues"></a>Problemi di proxy

In primo luogo, assicurarsi che le informazioni seguenti immesse siano corrette:

* l'URL del proxy e il numero di porta
* nome utente e password se richiesto dal proxy

Tenere presente che Storage Explorer non supporta il file con estensione pac per la configurazione delle impostazioni del proxy.

### <a name="common-solutions"></a>soluzioni comuni

Se si verificano ancora problemi, provare i metodi di risoluzione dei problemi seguenti:

* Se è possibile connettersi a Internet senza usare il proxy, verificare che Storage Explorer funzioni senza le impostazioni del proxy abilitate. In questo caso, potrebbe esserci un problema con le impostazioni del proxy. Rivolgersi all'amministratore del proxy per identificare i problemi.
* Verificare che altre applicazioni che usano il server proxy funzionino come previsto.
* Verificare che sia possibile connettersi al portale per l'ambiente di Azure che si sta tentando di usare
* Verificare di poter ricevere le risposte dagli endpoint del servizio. Immettere uno degli URL dell'endpoint nel browser. Se è possibile connettersi, si dovrebbe ricevere InvalidQueryParameterValue o una risposta XML simile.
* Se anche altre persone usano Storage Explorer con il server proxy, accertarsi che riescano a connettersi. Se questo non avviene, potrebbe essere necessario contattare l'amministratore del server proxy.

### <a name="tools-for-diagnosing-issues"></a>Strumenti per la diagnosi dei problemi

Se si dispone di strumenti di rete, ad esempio Fiddler per Windows, è possibile diagnosticare i problemi come indicato di seguito:

* Se si deve usare il proxy, è necessario configurare lo strumento di rete per connettersi tramite il proxy.
* Controllare il numero della porta usato dallo strumento di rete.
* Immettere l'URL dell'host locale e il numero della porta dello strumento di rete come impostazioni proxy in Storage Explorer. Quando questa operazione viene eseguita correttamente, lo strumento di rete inizia la registrazione delle richieste di rete effettuate da Storage Explorer agli endpoint di gestione e del servizio. Ad esempio, immettere https://cawablobgrs.blob.core.windows.net/ per l'endpoint BLOB in un browser. Si riceverà una risposta simile alla seguente, che suggerisce che la risorsa è disponibile, anche se non è possibile accedervi.

![esempio di codice](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Contattare l'amministratore del server proxy

Se le impostazioni del proxy sono corrette, è necessario contattare l'amministratore del server proxy e

* Assicurarsi che il proxy non blocchi il traffico agli endpoint di gestione o risorse di Azure.
* Verificare il protocollo di autenticazione usato dal server proxy. Storage Explorer attualmente non supporta i proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Messaggio di errore "Unable to Retrieve Children" (Impossibile recuperare gli elementi figlio)

Se si è connessi ad Azure tramite un proxy, verificare che le impostazioni del proxy siano corrette. Se è stato concesso l'accesso a una risorsa dal proprietario della sottoscrizione o dell'account, verificare di avere letto o elencare le autorizzazioni per tale risorsa.

## <a name="connection-string-does-not-have-complete-configuration-settings"></a>La stringa di connessione non dispone di impostazioni di configurazione complete

Se si riceve questo messaggio di errore, è possibile che non si disponga delle autorizzazioni necessarie per ottenere le chiavi dell'account di archiviazione. Per verificare se questo è il caso, passare al portale e individuare l'account di archiviazione. È possibile eseguire questa operazione rapidamente facendo clic con il pulsante destro del mouse sul nodo dell'account di archiviazione e scegliendo "Apri in portale". Al termine dell'operazione, passare al pannello "Chiavi di accesso". Se non si dispone delle autorizzazioni per visualizzare le chiavi, verrà visualizzata una pagina con il messaggio "Accesso non consentito". Per risolvere questo problema, è possibile ottenere la chiave dell'account da un altro utente e collegarsi con nome e chiave oppure è possibile chiedere a un altro utente una firma di accesso condiviso all'account di archiviazione e usarla per collegare l'account di archiviazione.

Se è possibile visualizzare le chiavi dell'account, inviare una segnalazione in GitHub per consentire al team Microsoft di aiutare a risolvere il problema.

## <a name="issues-with-sas-url"></a>Problemi relativi all'URL SAS
Se ci si connette a un servizio tramite un URL SAS e si verifica questo errore:

* Verificare che l'URL abbia le autorizzazioni necessarie per la lettura o l'elenco delle risorse.
* Verificare che l'URL non sia scaduto.
* Se l'URL SAS si basa su un criterio di accesso, verificare che i criteri di accesso non siano stati revocati.

Se per errore è stato associato un URL SAS non valido e non è possibile annullare l'associazione, seguire questa procedura:
1.  Durante l'esecuzione di Storage Explorer, premere il tasto F12 per aprire la finestra Strumenti di sviluppo.
2.  Fare clic sulla scheda Applicazione e quindi fare clic su Risorsa di archiviazione locale > file:// nell'albero a sinistra.
3.  Trovare la chiave associata al tipo di servizio dell'URI SAS che ha generato il problema. Ad esempio, se l'URI SAS non valido fa riferimento a un contenitore BLOB, cercare la chiave denominata `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Il valore della chiave deve essere una matrice JSON. Trovare l'oggetto associato all'URI non valido e rimuoverlo.
5.  Premere CTRL + R per ricaricare Storage Explorer.

## <a name="linux-dependencies"></a>Dipendenze Linux

Per distribuzioni Linux diverse da Ubuntu 16.04, è necessario installare manualmente alcune dipendenze. In generale sono necessari i pacchetti seguenti:
* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* GCC aggiornato

A seconda del tipo di distribuzione, potrebbe essere necessario installare altri pacchetti. Le [note sulla versione](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) di Storage Explorer contengono passaggi specifici per alcune distribuzioni.

## <a name="next-steps"></a>Passaggi successivi

Se nessuna delle soluzioni funziona, [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). È possibile accedere rapidamente a GitHub anche tramite il pulsante "Segnala un problema in GitHub" nell'angolo inferiore sinistro.

![Commenti e suggerimenti](./media/storage-explorer-troubleshooting/feedback-button.PNG)
