---
title: Guida alla risoluzione dei problemi di Azure Storage Explorer | Microsoft Docs
description: Cenni preliminari sulle tecniche di debug per Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: f24fb6c4d83da0d443702afaf673079363a9ffb0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714443"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Azure Storage Explorer

Microsoft Azure Storage Explorer è un'app autonoma che facilita l'uso dei dati con Archiviazione di Azure in Windows, macOS e Linux. L'app può connettersi ad account di archiviazione ospitati in Azure, cloud nazionali e Azure Stack.

Questa guida riepiloga le soluzioni per i problemi che si verificano comunemente in Storage Explorer.

## <a name="azure-rbac-permissions-issues"></a>Problemi relativi alle autorizzazioni RBAC di Azure

Il controllo degli accessi in base al ruolo [di Azure consente](https://docs.microsoft.com/azure/role-based-access-control/overview) la gestione degli accessi estremamente granulari delle risorse di Azure combinando set di autorizzazioni in _ruoli_. Ecco alcune strategie per ottimizzare il controllo degli accessi in base al ruolo di Azure in Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Ricerca per categorie accedere alle risorse in Storage Explorer?

In caso di problemi di accesso alle risorse di archiviazione tramite il controllo degli accessi in base al ruolo di Azure, è possibile che non siano stati assegnati i ruoli appropriati Le sezioni seguenti descrivono le autorizzazioni Storage Explorer attualmente richiede l'accesso alle risorse di archiviazione. Se non si è certi di disporre dei ruoli o delle autorizzazioni appropriate, contattare l'amministratore dell'account Azure.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problemi di autorizzazione "lettura: elenco/ottenere account di archiviazione"

È necessario disporre dell'autorizzazione per elencare gli account di archiviazione. Per ottenere questa autorizzazione, è necessario che sia stato assegnato il ruolo _lettore_ .

#### <a name="list-storage-account-keys"></a>Ottenere chiavi degli account di archiviazione

Storage Explorer inoltre possibile utilizzare le chiavi dell'account per autenticare le richieste. È possibile ottenere l'accesso alle chiavi dell'account tramite ruoli più potenti, ad esempio il ruolo _collaboratore_ .

> [!NOTE]
> Le chiavi di accesso concedono autorizzazioni senza restrizioni a tutti gli utenti che li contengono. Pertanto, non è consigliabile distribuire queste chiavi agli utenti dell'account. Se è necessario revocare le chiavi di accesso, è possibile rigenerarle dalla [portale di Azure](https://portal.azure.com/).

#### <a name="data-roles"></a>Ruoli dati

È necessario disporre di almeno un ruolo che concede l'accesso per la lettura dei dati dalle risorse. Se ad esempio si vuole elencare o scaricare i BLOB, è necessario almeno il ruolo _lettore dati BLOB di archiviazione_ .

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Perché è necessario un ruolo del livello di gestione per visualizzare le risorse in Storage Explorer?

Archiviazione di Azure offre due livelli di accesso: _gestione_ e _dati_. Le sottoscrizioni e gli account di archiviazione sono accessibili tramite il livello di gestione. È possibile accedere a contenitori, BLOB e altre risorse dati tramite il livello dati. Se ad esempio si vuole ottenere un elenco degli account di archiviazione da Azure, si invia una richiesta all'endpoint di gestione. Se si desidera un elenco di contenitori BLOB in un account, inviare una richiesta all'endpoint di servizio appropriato.

I ruoli di Azure possono concedere autorizzazioni per la gestione o l'accesso a livello di dati. Il ruolo Reader, ad esempio, concede l'accesso in sola lettura alle risorse del livello di gestione.

In modo rigoroso, il ruolo Reader non fornisce autorizzazioni per i livelli di dati e non è necessario per l'accesso al livello dati.

Storage Explorer facilita l'accesso alle risorse raccogliendo le informazioni necessarie per connettersi alle risorse di Azure. Ad esempio, per visualizzare i contenitori BLOB, Storage Explorer invia una richiesta "list containers" all'endpoint del servizio BLOB. Per ottenere l'endpoint, Storage Explorer Cerca nell'elenco di sottoscrizioni e account di archiviazione a cui si ha accesso. Per trovare le sottoscrizioni e gli account di archiviazione, Storage Explorer necessario anche accedere al livello di gestione.

Se non si dispone di un ruolo che concede autorizzazioni per i livelli di gestione, Storage Explorer non è in grado di ottenere le informazioni necessarie per connettersi al livello dati.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Cosa accade se non è possibile ottenere le autorizzazioni del livello di gestione necessarie dall'amministratore?

Se si vuole accedere a contenitori o code BLOB, è possibile connettersi a tali risorse usando le credenziali di Azure.

1. Aprire la finestra di dialogo Connetti.
2. Selezionare "Aggiungi una risorsa tramite Azure Active Directory (Azure AD). Scegliere Avanti.
3. Selezionare l'account utente e il tenant associati alla risorsa a cui si sta eseguendo la connessione. Scegliere Avanti.
4. Selezionare il tipo di risorsa, immettere l'URL della risorsa e immettere un nome visualizzato univoco per la connessione. Scegliere Avanti. Fare clic su Connetti.

Per altri tipi di risorse, non è attualmente disponibile una soluzione correlata a RBAC di Azure. Come soluzione alternativa, è possibile richiedere un URI di firma di accesso condiviso per [connettersi alla risorsa](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-azure-built-in-roles"></a>Ruoli predefiniti di Azure consigliati

Sono disponibili diversi ruoli predefiniti di Azure che possono fornire le autorizzazioni necessarie per usare Storage Explorer. Alcuni di questi ruoli sono:
- [Proprietario](/azure/role-based-access-control/built-in-roles#owner): consente di gestire tutto, incluso l'accesso alle risorse. **Nota**: questo ruolo fornirà l'accesso alla chiave.
- [Collaboratore](/azure/role-based-access-control/built-in-roles#contributor): consente di gestire tutto, escluso l'accesso alle risorse. **Nota**: questo ruolo fornirà l'accesso alla chiave.
- [Reader](/azure/role-based-access-control/built-in-roles#reader): leggere ed elencare le risorse.
- [Collaboratore account di archiviazione](/azure/role-based-access-control/built-in-roles#storage-account-contributor): gestione completa degli account di archiviazione. **Nota**: questo ruolo fornirà l'accesso alla chiave.
- [Proprietario dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner): accesso completo ai contenitori BLOB e ai dati di archiviazione di Azure.
- [Collaboratore dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor): lettura, scrittura ed eliminazione di BLOB e contenitori di archiviazione di Azure.
- [Lettore dati BLOB di archiviazione](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader): lettura ed elenco di contenitori e BLOB di archiviazione di Azure.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Errore: certificato autofirmato nella catena di certificati (ed errori simili)

Gli errori dei certificati si verificano in genere in una delle situazioni seguenti:

- L'app è connessa tramite un _proxy trasparente_. Ciò significa che un server (ad esempio il server aziendale) intercetta il traffico HTTPS, lo decrittografa e quindi lo crittografa usando un certificato autofirmato.
- Si sta eseguendo un'applicazione che inserisce un certificato TLS/SSL autofirmato nei messaggi HTTPS ricevuti. Esempi di applicazioni che inseriscono certificati includono il software antivirus e di ispezione del traffico di rete.

Quando Storage Explorer visualizza un certificato autofirmato o non attendibile, non è più in grado di riconoscere se il messaggio HTTPS ricevuto è stato modificato. Se si dispone di una copia del certificato autofirmato, è possibile indicare Storage Explorer di considerarla attendibile attenendosi alla procedura seguente:

1. Ottenere una copia X. 509 (. cer) codificata in base 64 del certificato.
2. Passare a **modifica**certificati  >  **SSL**  >  **Importa certificati**, quindi usare il selettore file per trovare, selezionare e aprire il file con estensione cer.

Questo problema può verificarsi anche se sono presenti più certificati (radice e intermedio). Per correggere l'errore, è necessario aggiungere entrambi i certificati.

Se non si è certi della provenienza del certificato, attenersi alla procedura seguente per trovarla:

1. Installare OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): una qualsiasi delle versioni leggere dovrebbe essere sufficiente.
    * Mac e Linux: devono essere inclusi nel sistema operativo.
2. Eseguire OpenSSL.
    * Windows: aprire la directory di installazione, selezionare **/bin/**, quindi fare doppio clic su **openssl.exe**.
    * Mac e Linux: eseguire `openssl` da un terminale.
3. Eseguire `s_client -showcerts -connect microsoft.com:443`.
4. Cercare i certificati autofirmati. Se non si è certi di quali certificati sono autofirmati, prendere nota di dove si trova l'oggetto `("s:")` e l'emittente `("i:")` .
5. Quando si individuano certificati autofirmati, per ognuno di essi copiare e incollare tutti gli elementi da (e inclusi) `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` in un nuovo file con estensione cer.
6. Aprire Storage Explorer e passare a **modifica**  >  **certificati SSL**  >  **Importa certificati**. Usare quindi la selezione file per trovare, selezionare e aprire i file con estensione cer creati.

Se non è possibile trovare alcun certificato autofirmato seguendo questa procedura, contattare Microsoft tramite lo strumento di feedback. È anche possibile aprire Storage Explorer dalla riga di comando usando il `--ignore-certificate-errors` flag. Quando viene aperto con questo flag, Storage Explorer ignora gli errori del certificato.

## <a name="sign-in-issues"></a>Problemi di accesso

### <a name="blank-sign-in-dialog-box"></a>Finestra di dialogo di accesso vuoto

Le finestre di dialogo di accesso vuote si verificano spesso quando Active Directory Federation Services (AD FS) richiede Storage Explorer di eseguire un reindirizzamento, che non è supportato da Electron. Per risolvere questo problema, è possibile provare a usare il flusso del codice del dispositivo per l'accesso. A questo scopo, attenersi alla procedura seguente:

1. Sulla barra degli strumenti verticale sinistra aprire **Impostazioni**. Nel pannello impostazioni passare ad **applicazione**  >  **Accedi**. Abilitare l' **accesso tramite il flusso di codice del dispositivo**.
2. Aprire la finestra di dialogo **Connetti** (tramite l'icona a forma di spina sulla barra verticale a sinistra o selezionando **Aggiungi account** nel pannello account).
3. Scegliere l'ambiente a cui si vuole accedere.
4. Selezionare **Accedi**.
5. Seguire le istruzioni visualizzate.

Se non è possibile accedere all'account che si vuole usare perché il browser predefinito è già connesso a un altro account, effettuare una delle operazioni seguenti:

- Copiare manualmente il collegamento e il codice in una sessione privata del browser.
- Copiare manualmente il collegamento e il codice in un browser diverso.

### <a name="reauthentication-loop-or-upn-change"></a>Ciclo di riautenticazione o modifica UPN

Se si è in un ciclo di riautenticazione o è stato modificato l'UPN di uno degli account, seguire questa procedura:

1. Rimuovere tutti gli account e quindi chiudere Storage Explorer.
2. Eliminare la cartella .IdentityService dal computer. In Windows, la cartella si trova in `C:\users\<username>\AppData\Local`. Per Mac e Linux, è possibile trovare la cartella nella radice della directory dell'utente.
3. Se si esegue Mac o Linux, sarà anche necessario eliminare la voce Microsoft. Developer. IdentityService dall'archivio chiavi del sistema operativo. Nel Mac, l'archivio chiavi è l'applicazione di *Keychain per GNOME* . In Linux, l'applicazione è in genere denominata _portachiavi_, ma il nome potrebbe variare a seconda della distribuzione.

### <a name="conditional-access"></a>Accesso condizionale

A causa di una limitazione nella libreria Azure AD usata da Storage Explorer, l'accesso condizionale non è supportato quando Storage Explorer viene usato in Windows 10, Linux o macOS.

## <a name="mac-keychain-errors"></a>Errori di Keychain su Mac

Il keychain di macOS può talvolta entrare in uno stato che causa problemi per la libreria di autenticazione Storage Explorer. Per ottenere il keychain da questo stato, seguire questa procedura:

1. Chiudere Azure Storage Explorer.
2. Aprire keychain (premere comando + barra spaziatrice, digitare **Keychain**e premere INVIO).
3. Selezionare il keychain "login".
4. Selezionare l'icona del lucchetto per bloccare il keychain. Il lucchetto verrà visualizzato al termine del processo. L'operazione potrebbe richiedere alcuni secondi, a seconda delle app aperte.

    ![Icona del lucchetto](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Aprire Esplora archivi.
6. Viene visualizzato un messaggio simile a "l'hub servizio vuole accedere al keychain". Immettere la password dell'account amministratore Mac e selezionare **Consenti sempre** (o **Consenti** se **Consenti sempre** non è disponibile).
7. Provare a effettuare l'accesso.

### <a name="general-sign-in-troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi di accesso generale

* Se si usa macOS e la finestra di accesso non viene mai visualizzata nella finestra di dialogo **in attesa dell'autenticazione** , provare a eseguire [questi passaggi](#mac-keychain-errors).
* Riavviare Storage Explorer.
* Se la finestra di autenticazione è vuota, attendere almeno un minuto prima di chiudere la finestra di dialogo di autenticazione.
* Assicurarsi che le impostazioni del proxy e del certificato siano configurate correttamente sia per il computer che per Storage Explorer.
* Se si esegue Windows e si ha accesso a Visual Studio 2019 nello stesso computer e alle credenziali di accesso, provare ad accedere a Visual Studio 2019. Dopo aver eseguito l'accesso a Visual Studio 2019, è possibile aprire Storage Explorer e visualizzare il proprio account nel pannello dell'account.

Se nessuno di questi metodi funziona, [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Sottoscrizioni mancanti e tenant interrotti

Se non è possibile recuperare le sottoscrizioni dopo aver eseguito correttamente l'accesso, provare i metodi di risoluzione dei problemi seguenti:

* Verificare che l'account abbia accesso alle sottoscrizioni previste. È possibile verificare l'accesso accedendo al portale per l'ambiente di Azure che si sta tentando di usare.
* Assicurarsi di aver effettuato l'accesso tramite l'ambiente Azure corretto (Azure, Azure China 21Vianet, Azure Germania, Azure US Government o l'ambiente personalizzato).
* Se si è protetti da un server proxy, verificare che il proxy di Storage Explorer sia stato configurato correttamente.
* Provare a rimuovere e a aggiungere nuovamente l'account.
* Se è presente un collegamento "ulteriori informazioni", controllare quali messaggi di errore vengono segnalati per i tenant che hanno avuto esito negativo. Se non si è certi di come rispondere ai messaggi di errore, è possibile [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Non è possibile rimuovere un account o una risorsa di archiviazione collegata

Se non è possibile rimuovere un account collegato o una risorsa di archiviazione tramite l'interfaccia utente, è possibile eliminare manualmente tutte le risorse collegate eliminando le cartelle seguenti:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Chiudere Storage Explorer prima di eliminare le cartelle.

> [!NOTE]
> Se sono stati importati certificati SSL, eseguire il backup del contenuto della `certs` Directory. In un secondo momento, è possibile usare il backup per reimportare i certificati SSL.

## <a name="proxy-issues"></a>Problemi di proxy

Assicurarsi prima di tutto che le seguenti informazioni immesse siano corrette:

* l'URL del proxy e il numero di porta
* Nome utente e password se richiesto dal proxy

> [!NOTE]
> Storage Explorer non supporta i file di configurazione automatica del proxy per la configurazione delle impostazioni proxy.

### <a name="common-solutions"></a>soluzioni comuni

Se si verificano ancora problemi, provare i metodi di risoluzione dei problemi seguenti:

* Se è possibile connettersi a Internet senza usare il proxy, verificare che Storage Explorer funzioni senza impostazioni proxy abilitate. In questo caso, potrebbe esserci un problema con le impostazioni del proxy. Collaborare con l'amministratore per identificare i problemi.
* Verificare che le altre applicazioni che utilizzano il server proxy funzionino come previsto.
* Verificare che sia possibile connettersi al portale per l'ambiente di Azure che si sta tentando di usare.
* Verificare di poter ricevere le risposte dagli endpoint del servizio. Immettere uno degli URL dell'endpoint nel browser. Se è possibile connettersi, è necessario ricevere InvalidQueryParameterValue o una risposta XML simile.
* Se anche altre persone usano Storage Explorer con il server proxy, accertarsi che riescano a connettersi. Se possibile, potrebbe essere necessario contattare l'amministratore del server proxy.

### <a name="tools-for-diagnosing-issues"></a>Strumenti per la diagnosi dei problemi

Se si dispone di strumenti di rete, ad esempio Fiddler per Windows, è possibile diagnosticare i problemi come indicato di seguito:

* Se si deve usare il proxy, è necessario configurare lo strumento di rete per connettersi tramite il proxy.
* Controllare il numero della porta usato dallo strumento di rete.
* Immettere l'URL dell'host locale e il numero della porta dello strumento di rete come impostazioni proxy in Storage Explorer. Quando si esegue questa operazione correttamente, lo strumento di rete avvia la registrazione delle richieste di rete effettuate da Storage Explorer agli endpoint di gestione e di servizio. Ad esempio, immettere `https://cawablobgrs.blob.core.windows.net/` per l'endpoint BLOB in un browser e si riceverà una risposta simile alla seguente:

  ![Esempio di codice](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Questa risposta suggerisce che la risorsa esiste, anche se non è possibile accedervi.

### <a name="contact-proxy-server-admin"></a>Contattare l'amministratore del server proxy

Se le impostazioni del proxy sono corrette, potrebbe essere necessario contattare l'amministratore del server proxy per:

* Assicurarsi che il proxy non blocchi il traffico verso gli endpoint della risorsa o di gestione di Azure.
* Verificare il protocollo di autenticazione usato dal server proxy. Storage Explorer attualmente non supporta i proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Messaggio di errore "Unable to Retrieve Children" (Impossibile recuperare gli elementi figlio)

Se si è connessi ad Azure tramite un proxy, verificare che le impostazioni del proxy siano corrette. Se viene concesso l'accesso a una risorsa dal proprietario della sottoscrizione o dell'account, verificare di avere le autorizzazioni di lettura o di elenco per tale risorsa.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Per la stringa di connessione non sono disponibili impostazioni di configurazione complete

Se viene visualizzato questo messaggio di errore, è possibile che non si disponga delle autorizzazioni necessarie per ottenere le chiavi per l'account di archiviazione. Per confermare che questo è il caso, passare al portale e individuare l'account di archiviazione. A tale scopo, fare clic con il pulsante destro del mouse sul nodo dell'account di archiviazione e scegliere **Apri nel portale**. Passare quindi al pannello **chiavi di accesso** . Se non si hanno le autorizzazioni per visualizzare le chiavi, verrà visualizzato il messaggio "non si ha accesso". Per risolvere questo problema, è possibile ottenere la chiave dell'account da un altro utente e collegarla tramite il nome e la chiave oppure è possibile chiedere a una firma di accesso condiviso per l'account di archiviazione e usarla per allegare l'account di archiviazione.

Se vengono visualizzate le chiavi dell'account, archiviare un problema in GitHub in modo da consentire la risoluzione del problema.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Si è verificato un errore durante l'aggiunta della nuova connessione: TypeError: Impossibile leggere la proprietà' version ' di undefined

Se si riceve questo messaggio di errore quando si tenta di aggiungere una connessione personalizzata, i dati di connessione archiviati in Gestione credenziali locale potrebbero essere danneggiati. Per risolvere il problema, provare a eliminare le connessioni locali danneggiate, quindi aggiungerle nuovamente:

1. Avviare Storage Explorer. Dal menu, passare a **Guida**  >  **strumenti di sviluppo**.
2. Nella finestra aperta, nella scheda **applicazione** , passare alla risorsa di **archiviazione locale** (lato sinistro) > **file://**.
3. A seconda del tipo di connessione con cui si verifica un problema, cercare la relativa chiave e quindi copiarne il valore in un editor di testo. Il valore è una matrice dei nomi di connessione personalizzati, come il seguente:
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
4. Dopo aver salvato i nomi di connessione correnti, impostare il valore Strumenti di sviluppo su `[]` .

Se si desidera mantenere le connessioni non danneggiate, è possibile utilizzare la procedura seguente per individuare le connessioni danneggiate. Se non si desidera perdere tutte le connessioni esistenti, è possibile ignorare questi passaggi e seguire le istruzioni specifiche della piattaforma per cancellare i dati di connessione.

1. Da un editor di testo, aggiungere di nuovo il nome della connessione a Strumenti di sviluppo, quindi verificare se la connessione è ancora funzionante.
2. Se una connessione funziona correttamente, non è danneggiata ed è possibile lasciarla in modo sicuro. Se una connessione non funziona, rimuovere il valore da Strumenti di sviluppo e registrarlo in modo che sia possibile aggiungerlo di nuovo in un secondo momento.
3. Ripetere fino a quando non sono state esaminate tutte le connessioni.

Dopo aver attraversato tutte le connessioni, per tutti i nomi delle connessioni che non sono stati aggiunti, è necessario cancellare i dati danneggiati (se presenti) e aggiungerli nuovamente utilizzando i passaggi standard in Storage Explorer:

# <a name="windows"></a>[Windows](#tab/Windows)

1. Nel menu **Start** cercare **Gestione credenziali** e aprirlo.
2. Passare a **Windows credentials**.
3. In **credenziali generiche**cercare le voci che contengono la `<connection_type_key>/<corrupted_connection_name>` chiave (ad esempio, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Eliminare queste voci e aggiungere di nuovo le connessioni.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Aprire Spotlight (Command + barra spaziatrice) e cercare **l'accesso Keychain**.
2. Cercare le voci che contengono la `<connection_type_key>/<corrupted_connection_name>` chiave (ad esempio, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Eliminare queste voci e aggiungere di nuovo le connessioni.

# <a name="linux"></a>[Linux](#tab/Linux)

La gestione delle credenziali locali varia a seconda della distribuzione di Linux. Se la distribuzione di Linux non fornisce uno strumento GUI integrato per la gestione delle credenziali locali, è possibile installare uno strumento di terze parti per gestire le credenziali locali. Ad esempio, è possibile usare [cavalluccio](https://wiki.gnome.org/Apps/Seahorse/), uno strumento di interfaccia utente grafica open source per la gestione delle credenziali locali di Linux.

1. Aprire lo strumento di gestione delle credenziali locale e trovare le credenziali salvate.
2. Cercare le voci che contengono la `<connection_type_key>/<corrupted_connection_name>` chiave (ad esempio, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Eliminare queste voci e aggiungere di nuovo le connessioni.
---

Se questo errore si verifica ancora dopo l'esecuzione di questi passaggi o se si vuole condividere i dati sospetti che hanno danneggiato le connessioni, [aprire un problema](https://github.com/microsoft/AzureStorageExplorer/issues) nella pagina di GitHub.

## <a name="issues-with-sas-url"></a>Problemi relativi all'URL SAS

Se ci si connette a un servizio tramite un URL SAS e si verifica un errore:

* Verificare che l'URL abbia le autorizzazioni necessarie per la lettura o l'elenco delle risorse.
* Verificare che l'URL non sia scaduto.
* Se l'URL SAS si basa su un criterio di accesso, verificare che i criteri di accesso non siano stati revocati.

Se è stata accidentalmente collegata usando un URL SAS non valido e non è possibile scollegarlo, attenersi alla procedura seguente:

1. Quando si esegue Storage Explorer, premere F12 per aprire la finestra di Strumenti di sviluppo.
2. Nella scheda **applicazione** selezionare **local storage**  >  **file://** nell'albero a sinistra.
3. Trovare la chiave associata al tipo di servizio dell'URI SAS che ha generato il problema. Ad esempio, se l'URI SAS non valido fa riferimento a un contenitore BLOB, cercare la chiave denominata `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Il valore della chiave deve essere una matrice JSON. Individuare l'oggetto associato all'URI errato, quindi eliminarlo.
5. Premere CTRL + R per ricaricare Storage Explorer.

## <a name="linux-dependencies"></a>Dipendenze Linux

### <a name="snap"></a>Snap

Storage Explorer 1.10.0 e versioni successive è disponibile come snap dall'archivio snap. Lo snap Storage Explorer installa automaticamente tutte le dipendenze e viene aggiornato quando è disponibile una nuova versione dello snap-in. L'installazione del Storage Explorer snap è il metodo consigliato per l'installazione.

Storage Explorer richiede l'uso di un gestore delle password, che potrebbe essere necessario connettersi manualmente prima che Storage Explorer funzioni correttamente. È possibile connettere Storage Explorer al gestore delle password del sistema eseguendo il comando seguente:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>File con estensione tar. gz

È anche possibile scaricare l'applicazione come file con estensione tar. gz, ma è necessario installare le dipendenze manualmente.

Storage Explorer come specificato nel download di. tar. gz è supportato solo per le versioni seguenti di Ubuntu. Storage Explorer potrebbe funzionare in altre distribuzioni di Linux, ma non sono ufficialmente supportate.

- Ubuntu 20,04 x64
- Ubuntu 18,04 x64
- Ubuntu 16,04 x64

Storage Explorer richiede l'installazione di .NET Core nel sistema. Si consiglia .NET Core 2,1, ma Storage Explorer funzionerà anche con 2,2.

> [!NOTE]
> Storage Explorer versione 1.7.0 e versioni precedenti richiedono .NET Core 2,0. Se è installata una versione più recente di .NET Core, è necessario applicare una [patch Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Se si esegue Storage Explorer 1.8.0 o versione successiva, è necessario almeno .NET Core 2,1.

# <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Scaricare il file Storage Explorer. tar. gz.
2. Installare il [runtime di .NET Core](https://docs.microsoft.com/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Scaricare il file Storage Explorer. tar. gz.
2. Installare il [runtime di .NET Core](https://docs.microsoft.com/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Scaricare il file Storage Explorer. tar. gz.
2. Installare il [runtime di .NET Core](https://docs.microsoft.com/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Molte librerie richieste da Storage Explorer vengono preinstallate con le installazioni standard di Canonical di Ubuntu. Per gli ambienti personalizzati potrebbero mancare alcune di queste librerie. Se si verificano problemi durante l'avvio di Storage Explorer, è consigliabile assicurarsi che nel sistema siano installati i pacchetti seguenti:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Applicazione di patch Storage Explorer per le versioni più recenti di .NET Core

Per Storage Explorer 1.7.0 o versioni precedenti, potrebbe essere necessario applicare patch alla versione di .NET Core usata da Storage Explorer:

1. Scaricare la versione 1.5.43 di StreamJsonRpc [da NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Cercare il collegamento "Scarica pacchetto" sul lato destro della pagina.
2. Dopo aver scaricato il pacchetto, modificare l'estensione del file da `.nupkg` a `.zip` .
3. Decomprimere il pacchetto.
4. Aprire la cartella `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Copiare `StreamJsonRpc.dll` nei percorsi seguenti nella cartella Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Apri in Esplora risorse" dal portale di Azure non funziona

Se il pulsante **Apri in Esplora** sul portale di Azure non funziona, assicurarsi che si stia usando un browser compatibile. I browser seguenti sono stati testati per la compatibilità:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Passaggi successivi

Se nessuna di queste soluzioni funziona, [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Questa operazione può essere eseguita anche selezionando il pulsante **Segnala problema in GitHub** nell'angolo in basso a sinistra.

![Commenti e suggerimenti](./media/storage-explorer-troubleshooting/feedback-button.PNG)
