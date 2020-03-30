---
title: Guida alla risoluzione dei problemi di Azure Storage Explorer | Microsoft Docs
description: Panoramica delle tecniche di debug per Esplora archivi di AzureOverview of debugging techniques for Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: aec8048c7ef2eb0d944cdd2a863e23578f4f87e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561681"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Azure Storage Explorer

Microsoft Azure Storage Explorer is a standalone app that makes it easy to work with Azure Storage data on Windows, macOS, and Linux. L'app può connettersi agli account di archiviazione ospitati in Azure, cloud nazionali e Azure Stack.The app can connect to storage accounts hosted on Azure, national clouds, and Azure Stack.

In questa guida vengono riepilogate le soluzioni per i problemi comunemente riscontrati in Esplora archivi.

## <a name="rbac-permissions-issues"></a>Problemi relativi alle autorizzazioni RBAC

Il controllo [degli](https://docs.microsoft.com/azure/role-based-access-control/overview) accessi in base al ruolo consente una gestione dell'accesso altamente granulare delle risorse di Azure combinando set di autorizzazioni in _ruoli._ Ecco alcune strategie per far funzionare il controllo degli accessi in base al ruolo in modo ottimale in Storage Explorer.Here are some strategies to get RBAC working optimally in Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Come si accede alle risorse in Storage Explorer?

Se si verificano problemi di accesso alle risorse di archiviazione tramite il controllo degli accessi in base al ruolo, è possibile che non siano stati assegnati i ruoli appropriati. Nelle sezioni seguenti vengono descritte le autorizzazioni attualmente necessarie da Esplora risorse per L'archiviazione per l'accesso alle risorse di archiviazione. Se non si è certi di disporre dei ruoli o delle autorizzazioni appropriate, contattare l'amministratore dell'account Azure.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problema di autorizzazioni "Lettura: Elenco/Ottieni account di archiviazione"

È necessario disporre dell'autorizzazione per elencare gli account di archiviazione. Per ottenere questa autorizzazione, è necessario disporre del ruolo _Lettore._

#### <a name="list-storage-account-keys"></a>Ottenere chiavi degli account di archiviazione

Storage Explorer può anche usare le chiavi di account per autenticare le richieste. È possibile ottenere l'accesso alle chiavi dell'account tramite ruoli più potenti, ad esempio il ruolo _Collaboratore.You_ can get access to account keys through more powerful roles, such as the Contributor role.

> [!NOTE]
> I tasti di scelta concedono autorizzazioni senza restrizioni a chiunque le possieda. Pertanto, non è consigliabile distribuire queste chiavi agli utenti dell'account. Se è necessario revocare le chiavi di accesso, è possibile rigenerarle dal portale di [Azure.](https://portal.azure.com/)

#### <a name="data-roles"></a>Ruoli dei dati

È necessario disporre di almeno un ruolo che concede l'accesso per leggere i dati dalle risorse. Ad esempio, se si desidera elencare o scaricare BLOB, è necessario almeno il ruolo _Lettore dati BLOB di archiviazione._

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Perché è necessario un ruolo del livello di gestione per visualizzare le risorse in Esplora archivi?

Archiviazione di Azure ha due livelli di accesso: _gestione_ e _dati._ Le sottoscrizioni e gli account di archiviazione sono accessibili tramite il livello di gestione. I contenitori, i BLOB e altre risorse di dati sono accessibili tramite il livello dati. Ad esempio, se si vuole ottenere un elenco degli account di archiviazione da Azure, si invia una richiesta all'endpoint di gestione. Se si vuole un elenco di contenitori BLOB in un account, si invia una richiesta all'endpoint del servizio appropriato.

I ruoli RBAC possono contenere autorizzazioni per la gestione o l'accesso al livello dati. Il ruolo Lettore, ad esempio, concede l'accesso in sola lettura alle risorse del livello di gestione.

In senso stretto, il ruolo Lettore non fornisce autorizzazioni per il livello dati e non è necessario per accedere al livello dati.

Storage Explorer semplifica l'accesso alle risorse raccogliendo le informazioni necessarie per connettersi alle risorse di Azure.Storage Explorer makes it easy to access your resources by gathering the necessary information to connect to your Azure resources. Ad esempio, per visualizzare i contenitori BLOB, Storage Explorer invia una richiesta "elenca contenitori" all'endpoint del servizio BLOB. Per ottenere l'endpoint, Storage Explorer esegue la ricerca nell'elenco delle sottoscrizioni e degli account di archiviazione a cui si ha accesso. Per trovare le sottoscrizioni e gli account di archiviazione, Storage Explorer deve accedere anche al livello di gestione.

Se non si dispone di un ruolo che concede autorizzazioni del livello di gestione, Storage Explorer non è in grado di ottenere le informazioni necessarie per connettersi al livello dati.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Cosa succede se non riesco a ottenere le autorizzazioni del livello di gestione necessarie al mio amministratore?

Al momento non è disponibile una soluzione correlata al controllo degli accessi in base al ruolo per questo problema. In alternativa, è possibile richiedere un URI di accesso condiviso da [associare alla risorsa.](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri)

### <a name="recommended-built-in-rbac-roles"></a>Ruoli RBAC predefiniti consigliatiRecommended built-in RBAC roles

Esistono diversi ruoli RBAC predefiniti che possono fornire le autorizzazioni necessarie per usare Storage Explorer.There are several built-in RBAC roles which can provide the permissions needed to use Storage Explorer. Alcuni di questi ruoli sono:
- [Proprietario](/azure/role-based-access-control/built-in-roles#owner): Gestire tutto, incluso l'accesso alle risorse. **Nota**: questo ruolo consente l'accesso alla chiave.
- [Collaboratore](/azure/role-based-access-control/built-in-roles#contributor): Gestire tutto, escluso l'accesso alle risorse. **Nota**: questo ruolo consente l'accesso alla chiave.
- [Lettore](/azure/role-based-access-control/built-in-roles#reader): Leggere ed elencare le risorse.
- [Storage Account Contributor](/azure/role-based-access-control/built-in-roles#storage-account-contributor): Gestione completa degli account di archiviazione. **Nota**: questo ruolo consente l'accesso alla chiave.
- [Proprietario dei dati BLOB di archiviazione:](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)accesso completo ai dati e ai contenitori BLOB di Archiviazione blob di Azure.Storage Blob Data Owner : Full access to Azure Storage blob containers and data.
- [Collaboratore dati BLOB di archiviazione:](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)leggere, scrivere ed eliminare i contenitori e i BLOB di Archiviazione blob di Azure.Storage Blob Data Contributor : Read, write, and delete Azure Storage containers and blobs.
- [Lettore di dati BLOB di archiviazione:](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)leggere ed elencare i contenitori e i BLOB di Archiviazione di Azure.Storage Blob Data Reader: Read and list Azure Storage containers and blobs.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Errore: certificato autofirmato nella catena di certificati (e errori simili)

Gli errori dei certificati si verificano in genere in una delle seguenti situazioni:

- L'app è connessa tramite un _proxy trasparente,_ ovvero un server (ad esempio il server aziendale) intercetta il traffico HTTPS, lo decrittografa e quindi lo crittografa utilizzando un certificato autofirmato.
- Si esegue un'applicazione che sta inserendo un certificato SSL autofirmato nei messaggi HTTPS ricevuti. Esempi di applicazioni che inseriscono certificati includono antivirus e software di ispezione del traffico di rete.

Quando Storage Explorer vede un certificato autofirmato o non attendibile, non sa più se il messaggio HTTPS ricevuto è stato alterato. Se si dispone di una copia del certificato autofirmato, è possibile indicare a Storage Explorer di considerarlo attendibile attenendosi alla seguente procedura:

1. Ottenere una copia del certificato con codifica Base 64 X.509 (con estensione cer).
2. Passare a **Modifica** > **certificati** > SSL**di importazione certificati**e quindi utilizzare la selezione file per trovare, selezionare e aprire il file con estensione cer.

Questo problema può verificarsi anche se sono presenti più certificati (radice e intermedio). Per correggere questo errore, è necessario aggiungere entrambi i certificati.

Se non si è certi della provenivieniità del certificato, attenersi alla seguente procedura per trovarlo:

1. Installare OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Qualsiasi versione della luce dovrebbe essere sufficiente.
    * Mac e Linux: devono essere inclusi nel sistema operativo.
2. Eseguire OpenSSL.
    * Windows: aprire la directory di installazione, selezionare **/bin/**, quindi fare doppio clic su **openssl.exe**.
    * Mac e Linux: eseguire `openssl` da un terminale.
3. Eseguire `s_client -showcerts -connect microsoft.com:443`.
4. Cercare i certificati autofirmati. Se non si è certi di quali certificati sono autofirmati, prendere nota di qualsiasi punto il soggetto `("s:")` e l'autorità emittente `("i:")` sono uguali.
5. Quando si trovano certificati autofirmati, per ognuno di essi `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` copiare e incollare tutto da (e incluso) in un nuovo file CER.
6. Aprire Esplora archivi e passare a **Modifica** > **certificati** > SSL**importazione certificati**. Quindi utilizzare la selezione file per trovare, selezionare e aprire i file con estensione cer creati.

Se non riesci a trovare alcun certificato autofirmato seguendo questi passaggi, contattaci tramite lo strumento di feedback. È inoltre possibile aprire Esplora archivi dalla `--ignore-certificate-errors` riga di comando utilizzando il flag . Quando viene aperto con questo flag, Storage Explorer ignora gli errori del certificato.

## <a name="sign-in-issues"></a>Problemi di accesso

### <a name="blank-sign-in-dialog-box"></a>Finestra di dialogo di accesso vuota

Finestre di dialogo di accesso vuote si verificano più spesso quando Active Directory Federation Services (ADFS) richiede a Storage Explorer di eseguire un reindirizzamento, che non è supportato da Electron. Per risolvere questo problema, è possibile provare a utilizzare il flusso di codice del dispositivo per l'accesso. A questo scopo, attenersi alla procedura seguente:

1. Sulla barra degli strumenti verticale sinistra aprire **Impostazioni**. Nel pannello Impostazioni, accedere a**Accesso** **all'applicazione** > . Abilitare **Usa accesso flusso del codice del dispositivo**.
2. Aprire la finestra di dialogo **Connetti** (tramite l'icona della spina sulla barra verticale a sinistra o selezionando **Aggiungi account** nel pannello account).
3. Scegliere l'ambiente a cui si desidera accedere.
4. Selezionare **Accedi**.
5. Seguire le istruzioni visualizzate.

Se non riesci ad accedere all'account che vuoi usare perché il browser predefinito ha già eseguito l'accesso a un account diverso, esegui una delle operazioni seguenti:

- Copiare manualmente il collegamento e il codice in una sessione privata del browser.
- Copiare manualmente il collegamento e il codice in un browser diverso.

### <a name="reauthentication-loop-or-upn-change"></a>Ciclo di riautenticazione o modifica UPN

Se si è in un ciclo di riautenticazione o è stato modificato l'UPN di uno dei tuoi account, attenersi alla seguente procedura:

1. Rimuovere tutti gli account e quindi chiudere Storage Explorer.
2. Eliminare la cartella .IdentityService dal computer. In Windows, la cartella si trova in `C:\users\<username>\AppData\Local`. Per Mac e Linux, è possibile trovare la cartella nella radice della directory dell'utente.
3. Se si esegue Mac o Linux, è necessario eliminare anche la voce Microsoft.Developer.IdentityService dall'archivio chiavi del sistema operativo. Sul Mac, il keystore è l'applicazione *Gnome Keychain.* In Linux, l'applicazione viene in genere chiamata _Keyring_, ma il nome potrebbe variare a seconda della distribuzione.

### <a name="conditional-access"></a>Accesso condizionale

A causa di una limitazione nella libreria di Azure AD usata da Storage Explorer, l'accesso condizionale non è supportato quando Storage Explorer viene usato in Windows 10, Linux o macOS.

## <a name="mac-keychain-errors"></a>Errori di Keychain su Mac

Il portachiavi macOS a volte può entrare in uno stato che causa problemi per la libreria di autenticazione di Storage Explorer.The macOS Keychain can sometimes enter a state that causes issues for the Storage Explorer authentication library. Per ottenere il portachiavi da questo stato, attenersi alla seguente procedura:

1. Chiudere Azure Storage Explorer.
2. Aprire il portachiavi (premere COMANDO e barra **spaziatrice,** digitare portachiavi e premere INVIO).
3. Selezionare il portachiavi "login".
4. Selezionare l'icona del lucchetto per bloccare il portachiavi. (Il lucchetto apparirà bloccato al termine del processo. Potrebbero essere aperti alcuni secondi, a seconda delle app aperte.

    ![Icona del lucchetto](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Aprire Esplora archivi.
6. Viene visualizzato il messaggio "L'hub del servizio desidera accedere al portachiavi". Inserisci la password del tuo account amministratore Mac e seleziona **Consenti sempre** (o **Consenti** se **Consenti sempre** non è disponibile).
7. Provare a effettuare l'accesso.

### <a name="general-sign-in-troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi di accesso generale

* Se usi macOS e la finestra di accesso non viene mai visualizzata nella finestra di dialogo **In attesa di autenticazione,** prova [a eseguire questa procedura.](#mac-keychain-errors)
* Riavviare Storage Explorer.
* Se la finestra di autenticazione è vuota, attendere almeno un minuto prima di chiudere la finestra di dialogo di autenticazione.
* Assicurarsi che le impostazioni del proxy e del certificato siano configurate correttamente sia per il computer che per Esplora archivi.
* Se si esegue Windows e si ha accesso a Visual Studio 2019 nello stesso computer e alle credenziali di accesso, provare ad accedere a Visual Studio 2019. Dopo aver eseguito correttamente l'accesso a Visual Studio 2019, è possibile aprire Storage Explorer e visualizzare l'account nel riquadro dell'account.

Se nessuno di questi metodi funziona, [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Sottoscrizioni mancanti e tenant interrotti

Se non è possibile recuperare gli abbonamenti dopo aver eseguito correttamente l'accesso, provare i metodi di risoluzione dei problemi seguenti:If you can't retrieve your subscriptions after you successfully sign in, try the following troubleshooting methods:

* Verificare che l'account abbia accesso alle sottoscrizioni previste. È possibile verificare l'accesso accedendo al portale per l'ambiente Azure che si sta tentando di usare.
* Assicurarsi di aver eseguito l'accesso tramite l'ambiente Azure corretto (Azure, Azure China 21Vianet, Azure Germania, Azure US per enti pubblici o ambiente personalizzato).
* Se si è protetti da un server proxy, assicurarsi di aver configurato correttamente il proxy di Storage Explorer.
* Provare a rimuovere e aggiungere nuovamente l'account.
* Se è presente un collegamento "Ulteriori informazioni", verificare quali messaggi di errore vengono segnalati per i tenant che hanno esito negativo. Se non sei sicuro di come rispondere ai messaggi di errore, sentiti libero di [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Impossibile rimuovere un account collegato o una risorsa di archiviazione

Se non è possibile rimuovere un account collegato o una risorsa di archiviazione tramite l'interfaccia utente, è possibile eliminare manualmente tutte le risorse associate eliminando le cartelle seguenti:If you can't remove an attached account or storage resource through the UI, you can manually delete all attached resources by deleting the following folders:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Chiudere Storage Explorer prima di eliminare queste cartelle.

> [!NOTE]
> Se sono stati importati certificati SSL, eseguire il `certs` backup del contenuto della directory. In un secondo momento, è possibile usare il backup per reimportare i certificati SSL.

## <a name="proxy-issues"></a>Problemi di proxy

Innanzitutto, assicurati che le seguenti informazioni inserite siano corrette:

* l'URL del proxy e il numero di porta
* Nome utente e password se il proxy li richiede

> [!NOTE]
> Storage Explorer non supporta i file di configurazione automatica del proxy per la configurazione delle impostazioni proxy.

### <a name="common-solutions"></a>soluzioni comuni

Se i problemi persistono, prova i seguenti metodi di risoluzione dei problemi:

* Se è possibile connettersi a Internet senza utilizzare il proxy, verificare che Storage Explorer funzioni senza le impostazioni proxy abilitate. In questo caso, potrebbe esserci un problema con le impostazioni del proxy. Collaborare con l'amministratore per identificare i problemi.
* Verificare che le altre applicazioni che utilizzano il server proxy funzionino come previsto.
* Verificare che sia possibile connettersi al portale per l'ambiente di Azure che si sta tentando di usare.
* Verificare di poter ricevere le risposte dagli endpoint del servizio. Immettere uno degli URL dell'endpoint nel browser. Se è possibile connettersi, si dovrebbe ricevere InvalidQueryParameterValue o una risposta XML simile.
* Se anche altre persone usano Storage Explorer con il server proxy, accertarsi che riescano a connettersi. Se possibile, potrebbe essere necessario contattare l'amministratore del server proxy.

### <a name="tools-for-diagnosing-issues"></a>Strumenti per la diagnosi dei problemi

Se si dispone di strumenti di rete, ad esempio Fiddler per Windows, è possibile diagnosticare i problemi come segue:

* Se si deve usare il proxy, è necessario configurare lo strumento di rete per connettersi tramite il proxy.
* Controllare il numero della porta usato dallo strumento di rete.
* Immettere l'URL dell'host locale e il numero della porta dello strumento di rete come impostazioni proxy in Storage Explorer. Quando si esegue questa operazione correttamente, lo strumento di rete avvia la registrazione delle richieste di rete effettuate da Storage Explorer agli endpoint di gestione e del servizio. Ad esempio, `https://cawablobgrs.blob.core.windows.net/` immettere per l'endpoint BLOB in un browser e si riceverà una risposta simile alla seguente:For example, enter for your blob endpoint in a browser, and you'll receive a response that resembles the following:

  ![Esempio di codice](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Questa risposta suggerisce che la risorsa esiste, anche se non è possibile accedervi.

### <a name="contact-proxy-server-admin"></a>Contattare l'amministratore del server proxy

Se le impostazioni del proxy sono corrette, potrebbe essere necessario contattare l'amministratore del server proxy per:

* Assicurarsi che il proxy non blocchi il traffico verso la gestione di Azure o gli endpoint delle risorse.
* Verificare il protocollo di autenticazione usato dal server proxy. Storage Explorer attualmente non supporta i proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Messaggio di errore "Unable to Retrieve Children" (Impossibile recuperare gli elementi figlio)

Se si è connessi ad Azure tramite un proxy, verificare che le impostazioni del proxy siano corrette. Se si ha accesso a una risorsa dal proprietario della sottoscrizione o dell'account, verificare di disporre delle autorizzazioni di lettura o elenco per tale risorsa.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>La stringa di connessione non dispone di impostazioni di configurazione completeConnection string doesn't have complete configuration settings

Se viene visualizzato questo messaggio di errore, è possibile che non si disponga delle autorizzazioni necessarie per ottenere le chiavi per l'account di archiviazione. Per verificare che questo sia il caso, passare al portale e individuare l'account di archiviazione. A tale scopo, fare clic con il pulsante destro del mouse sul nodo dell'account di archiviazione e scegliere **Apri nel portale**. Passare quindi al pannello Chiavi di **accesso.** Se non disponi delle autorizzazioni per visualizzare le chiavi, verrà visualizzato il messaggio "Non si dispone dell'accesso". Per risolvere questo problema, è possibile ottenere la chiave dell'account da un altro utente e collegare tramite il nome e la chiave oppure è possibile chiedere una configurazione di accesso locale all'account di archiviazione e usarla per collegare l'account di archiviazione.

Se vedi le chiavi dell'account, invia un problema in GitHub in modo che possiamo aiutarti a risolvere il problema.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Errore durante l'aggiunta di una nuova connessione: TypeError: Impossibile leggere la proprietà 'version' di undefined

Se viene visualizzato questo messaggio di errore quando si tenta di aggiungere una connessione personalizzata, i dati di connessione archiviati nel gestore credenziali locale potrebbero essere danneggiati. Per risolvere questo problema, provare a eliminare le connessioni locali danneggiate e quindi aggiungerle nuovamente:

1. Avviare Esplora archivi. Dal menu, vai alla Guida > **Attiva/disattiva strumenti**di sviluppo . **Help**
2. Nella finestra aperta, nella scheda **Applicazione,** passare a **Archiviazione locale** (lato sinistro) > **file://**.
3. A seconda del tipo di connessione con cui si sta riscontrando un problema, cercarne la chiave e quindi copiarne il valore in un editor di testo. Il valore è una matrice dei nomi di connessione personalizzati, come segue:
    * Account di archiviazione
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Contenitori BLOB
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Condivisioni file
        * `StorageExplorer_CustomConnections_Files_v1`
    * Code
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabelle
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Dopo aver salvato i nomi delle connessioni correnti, impostare il valore in Strumenti di sviluppo su `[]`.

Se si desidera mantenere le connessioni non danneggiate, è possibile utilizzare la procedura seguente per individuare le connessioni danneggiate. Se non ti dispiace perdere tutte le connessioni esistenti, puoi saltare questi passaggi e seguire le istruzioni specifiche della piattaforma per cancellare i dati di connessione.

1. Da un editor di testo, aggiungere nuovamente ogni nome di connessione a Strumenti di sviluppo, quindi verificare se la connessione continua a funzionare.
2. Se una connessione funziona correttamente, non è danneggiata e si può tranquillamente lasciare lì. Se una connessione non funziona, rimuovine il valore da Strumenti di sviluppo e registrala in modo da poterla aggiungere di nuovo in un secondo momento.
3. Ripetere l'operazione fino a esaminare tutte le connessioni.

Dopo aver esaminato tutte le connessioni, per tutte le connessioni i nomi non aggiunti di nuovo, è necessario cancellare i dati danneggiati (se presenti) e aggiungerli nuovamente utilizzando i passaggi standard in Storage Explorer:

# <a name="windows"></a>[Windows](#tab/Windows)

1. Nel menu **Start** cercare **Gestione credenziali** e aprirlo.
2. Passare a **Credenziali di Windows**.
3. In **Credenziali generiche**cercare le voci `<connection_type_key>/<corrupted_connection_name>` che contengono `StorageExplorer_CustomConnections_Accounts_v1/account1`la chiave, ad esempio .
4. Eliminare queste voci e aggiungere nuovamente le connessioni.

# <a name="macos"></a>[Macos](#tab/macOS)

1. Aprire Spotlight (Comando-Barra spaziatrice) e cercare **accesso Portachiavi**.
2. Cercare le voci che `<connection_type_key>/<corrupted_connection_name>` con. `StorageExplorer_CustomConnections_Accounts_v1/account1`
3. Eliminare queste voci e aggiungere nuovamente le connessioni.

# <a name="linux"></a>[Linux](#tab/Linux)

La gestione delle credenziali locali varia a seconda della distribuzione Linux.Local credential management varies depending on the Linux distribution. Se la distribuzione Linux non fornisce uno strumento GUI incorporato per la gestione delle credenziali locali, è possibile installare uno strumento di terze parti per gestire le credenziali locali. Ad esempio, è possibile utilizzare Seahorse , uno strumento GUI open source per la gestione delle credenziali locali di Linux.For example, you can use [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), an open-source GUI tool for managing Linux local credentials.

1. Aprire lo strumento di gestione delle credenziali locali e trovare le credenziali salvate.
2. Cercare le voci che `<connection_type_key>/<corrupted_connection_name>` con. `StorageExplorer_CustomConnections_Accounts_v1/account1`
3. Eliminare queste voci e aggiungere nuovamente le connessioni.
---

Se si verifica ancora questo errore dopo l'esecuzione di questi passaggi, o se si desidera condividere ciò che si sospetta abbia danneggiato le connessioni, [aprire un problema](https://github.com/microsoft/AzureStorageExplorer/issues) nella nostra pagina GitHub.

## <a name="issues-with-sas-url"></a>Problemi relativi all'URL SAS

Se ci si connette a un servizio tramite un URL di disponibilità di chiamata e si verifica un errore:If you're connecting to a service through a SAS URL and experiencing an error:

* Verificare che l'URL abbia le autorizzazioni necessarie per la lettura o l'elenco delle risorse.
* Verificare che l'URL non sia scaduto.
* Se l'URL SAS si basa su un criterio di accesso, verificare che i criteri di accesso non siano stati revocati.

Se accidentalmente collegato utilizzando un URL di firma di chiamata non valido e ora non è possibile scollegare, attenersi alla seguente procedura:

1. Quando si esegue Storage Explorer, premere F12 per aprire la finestra Strumenti di sviluppo.
2. Nella scheda **Applicazione** selezionare **Archiviazione** > locale**file://** nell'albero a sinistra.
3. Trovare la chiave associata al tipo di servizio dell'URI SAS che ha generato il problema. Ad esempio, se l'URI SAS non valido fa riferimento a un contenitore BLOB, cercare la chiave denominata `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Il valore della chiave deve essere una matrice JSON. Individuare l'oggetto associato all'URI non valido e quindi eliminarlo.
5. Premere CTRL + R per ricaricare Storage Explorer.

## <a name="linux-dependencies"></a>Dipendenze Linux

Storage Explorer 1.10.0 e versioni successive è disponibile come snap dall'Snap Store. Lo snap di Storage Explorer installa automaticamente tutte le relative dipendenze e viene aggiornato quando è disponibile una nuova versione dello snap. L'installazione dello snap di Storage Explorer è il metodo consigliato per l'installazione.

Storage Explorer richiede l'uso di un gestore di password, che potrebbe essere necessario connettersi manualmente prima che Storage Explorer funzioni correttamente. È possibile connettere Storage Explorer al gestore password del sistema eseguendo il comando seguente:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

È anche possibile scaricare l'applicazione come file .tar.gz, ma sarà necessario installare le dipendenze manualmente.

> [!IMPORTANT]
> Storage Explorer come specificato nel download .tar.gz è supportato solo per le distribuzioni Ubuntu. Altre distribuzioni non sono state verificate e potrebbero richiedere pacchetti alternativi o aggiuntivi.

Questi pacchetti sono i requisiti più comuni per Storage Explorer su Linux:These packages are the most common requirements for Storage Explorer on Linux:

* [Runtime di .NET Core 2.2](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` o `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage Explorer versione 1.7.0 e versioni precedenti richiedono .NET Core 2.0. Se è installata una versione più recente di .NET Core, sarà necessario applicare una patch a [Storage Explorer.](#patching-storage-explorer-for-newer-versions-of-net-core) Se si esegue Storage Explorer 1.8.0 o versione successiva, dovrebbe essere possibile usare fino a .NET Core 2.2.If you're running Storage Explorer 1.8.0 or later, you should be able to use up to .NET Core 2.2. Le versioni successive alla 2.2 non sono state verificate per funzionare in questo momento.

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. Scaricare Storage Explorer.Download Storage Explorer.
2. Installare [.NET Core Runtime.](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current)
3. Eseguire il comando seguente:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Scaricare Storage Explorer.Download Storage Explorer.
2. Installare [.NET Core Runtime.](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current)
3. Eseguire il comando seguente:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Scaricare Storage Explorer.Download Storage Explorer.
2. Installare [.NET Core Runtime.](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current)
3. Eseguire il comando seguente:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[Ubuntu 14.04](#tab/1404)

1. Scaricare Storage Explorer.Download Storage Explorer.
2. Installare [.NET Core Runtime.](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current)
3. Eseguire il comando seguente:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Applicazione di patch a Esplora archivi per le versioni più recenti di .NET Core

Per Storage Explorer 1.7.0 o versioni precedenti, potrebbe essere necessario applicare una patch alla versione di .NET Core usata da Storage Explorer:

1. Scaricare la versione 1.5.43 di StreamJsonRpc [da NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Cerca il link "Scarica pacchetto" sul lato destro della pagina.
2. Dopo aver scaricato il pacchetto, `.nupkg` modificarne l'estensione da . `.zip`
3. Decomprimere il pacchetto.
4. Aprire la cartella `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Copiare `StreamJsonRpc.dll` nei percorsi seguenti nella cartella Esplora archivi:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Apri in Esplora risorse" dal portale di Azure non funziona

Se il pulsante **Apri in Esplora risorse** nel portale di Azure non funziona, assicurati di usare un browser compatibile. I seguenti browser sono stati testati per la compatibilità:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Passaggi successivi

Se nessuna di queste soluzioni funziona, [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). È anche possibile eseguire questa operazione selezionando il pulsante **Segnala problema a GitHub** nell'angolo inferiore sinistro.

![Commenti e suggerimenti](./media/storage-explorer-troubleshooting/feedback-button.PNG)
