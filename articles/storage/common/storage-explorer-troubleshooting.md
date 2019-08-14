---
title: Guida alla risoluzione dei problemi di Azure Storage Explorer | Microsoft Docs
description: Cenni preliminari sulle tecniche di debug per Azure Storage Explorer
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 96a8eab57f1714eed4831bea01508e9140d1dfad
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934978"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Azure Storage Explorer

Microsoft Azure Storage Explorer è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux. L'app può connettersi ad account di archiviazione ospitati in Azure, cloud nazionali e Azure Stack.

In questa guida sono riepilogate le soluzioni per gli errori comuni riscontrati in Storage Explorer.

## <a name="role-based-access-control-permission-issues"></a>Problemi di autorizzazione del controllo degli accessi in base al ruolo

Il controllo degli accessi in [base al ruolo (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) offre una gestione degli accessi con granularità fine per le risorse di Azure combinando set di autorizzazioni in _ruoli_. Ecco alcuni suggerimenti che è possibile seguire per fare in modo che RBAC funzioni in Storage Explorer.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Cosa è necessario per visualizzare le risorse in Storage Explorer?

Se si verificano problemi durante l'accesso alle risorse di archiviazione tramite RBAC, è possibile che non siano stati assegnati i ruoli appropriati. Le sezioni seguenti descrivono le autorizzazioni Storage Explorer attualmente necessarie per accedere alle risorse di archiviazione.

Se non si è certi di disporre dei ruoli o delle autorizzazioni appropriate, contattare l'amministratore dell'account Azure.

#### <a name="read-listget-storage-accounts"></a>Lettura: Elenca/ottiene gli account di archiviazione

È necessario disporre dell'autorizzazione per elencare gli account di archiviazione. È possibile ottenere questa autorizzazione assegnando il ruolo "lettore".

#### <a name="list-storage-account-keys"></a>Elenca chiavi dell'account di archiviazione

Storage Explorer inoltre possibile utilizzare le chiavi dell'account per autenticare le richieste. È possibile ottenere l'accesso alle chiavi con ruoli più potenti, ad esempio il ruolo "collaboratore".

> [!NOTE]
> Le chiavi di accesso concedono autorizzazioni senza restrizioni a tutti gli utenti che li contengono. Pertanto, in genere non è consigliabile distribuirle agli utenti dell'account. Se è necessario revocare le chiavi di accesso, è possibile rigenerarle dal [portale di Azure](https://portal.azure.com/).

#### <a name="data-roles"></a>Ruoli dati

È necessario disporre di almeno un ruolo che concede l'accesso in lettura ai dati dalle risorse. Se, ad esempio, è necessario elencare o scaricare i BLOB, sarà necessario almeno il ruolo "lettore dati BLOB di archiviazione".

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Perché è necessario un ruolo del livello di gestione per visualizzare le risorse in Storage Explorer?

Archiviazione di Azure offre due livelli di accesso: _gestione_ e _dati_. Le sottoscrizioni e gli account di archiviazione sono accessibili tramite il livello di gestione. È possibile accedere a contenitori, BLOB e altre risorse dati tramite il livello dati. Se ad esempio si vuole ottenere un elenco degli account di archiviazione da Azure, si invia una richiesta all'endpoint di gestione. Se si desidera un elenco di contenitori BLOB in un account, inviare una richiesta all'endpoint di servizio appropriato.

I ruoli RBAC possono contenere autorizzazioni per la gestione o l'accesso a livello di dati. Il ruolo "Reader", ad esempio, concede le risorse del livello di gestione dell'accesso di sola lettura.

In modo rigoroso, il ruolo "lettore" non fornisce autorizzazioni per i livelli di dati e non è necessario per l'accesso al livello dati.

Storage Explorer facilita l'accesso alle risorse raccogliendo le informazioni necessarie per connettersi alle risorse di Azure. Ad esempio, per visualizzare i contenitori BLOB, Storage Explorer invia una richiesta list Containers all'endpoint del servizio BLOB. Per ottenere l'endpoint, Storage Explorer Cerca nell'elenco di sottoscrizioni e account di archiviazione a cui si ha accesso. Tuttavia, per trovare le sottoscrizioni e gli account di archiviazione, Storage Explorer necessario anche accedere al livello di gestione.

Se non si dispone di un ruolo che concede autorizzazioni per i livelli di gestione, Storage Explorer non è in grado di ottenere le informazioni necessarie per connettersi al livello dati.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Cosa accade se non è possibile ottenere le autorizzazioni del livello di gestione necessarie dall'amministratore?

Al momento non è ancora presente una soluzione relativa al controllo degli accessi in base al ruolo. Come soluzione alternativa, è possibile richiedere un URI di firma di accesso condiviso per [connettersi alla risorsa](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Errore: certificato autofirmato nella catena di certificati (ed errori simili)

Gli errori di certificato sono causati da una delle due situazioni seguenti:

1. L'app è connessa tramite un "proxy trasparente": questo significa che un server (ad esempio, un server aziendale) intercetta il traffico HTTPS, ne esegue la decrittografia e la crittografia con un certificato autofirmato.
2. Si sta eseguendo un'applicazione che inserisce un certificato SSL autofirmato nei messaggi HTTPS ricevuti. Esempi di applicazioni che inseriscono certificati includono il software antivirus e per l'ispezione del traffico di rete.

In presenza di un certificato autofirmato o non attendibile, Storage Explorer non riesce più a stabilire se il messaggio HTTPS ricevuto è stato modificato. Se è disponibile una copia del certificato autofirmato, è possibile fare in modo che Storage Explorer lo consideri attendibile seguendo questa procedura:

1. Ottenere una copia del certificato X.509 con codifica Base64 (file con estensione cer) del certificato
2. Fare clic su **Modifica** > **Certificati SSL** > **Importa certificati** e quindi usare la selezione file per trovare, selezionare e aprire il file con estensione cer.

Il problema potrebbe anche essere il risultato di più certificati (radice e intermedi). Per risolvere l'errore, è necessario aggiungere entrambi i certificati.

Se non si è certi della provenienza del certificato, è possibile provare questi passaggi per trovarlo:

1. Installare Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html), una delle versioni light dovrebbe essere sufficiente
    * Mac e Linux: devono essere inclusi nel sistema operativo
2. Eseguire Open SSL
    * Windows: aprire la directory di installazione, fare clic su **/bin/** , quindi fare doppio clic su **openssl.exe**.
    * Mac e Linux: eseguire **openssl** da un terminale.
3. Eseguire `s_client -showcerts -connect microsoft.com:443`
4. Cercare i certificati autofirmati. Se non si è certi di quali certificati sono autofirmati, cercare in qualsiasi punto in `("s:")` cui l'oggetto `("i:")` e l'emittente sono uguali.
5. Dopo aver trovato i certificati autofirmati, per ognuno di essi, copiare e incollare tutto da e includendo **-----BEGIN CERTIFICATE-----** a **-----END CERTIFICATE-----** in un nuovo file con estensione CER.
6. Aprire Storage Explorer, fare clic su **Modifica** > **Certificati SSL** > **Importa certificati**, quindi usare la selezione file per trovare, selezionare e aprire i file con estensione CER creati.

Se non è possibile trovare certificati autofirmati usando i passaggi precedenti, contattare Microsoft tramite lo strumento di feedback per ulteriori informazioni. È anche possibile scegliere di avviare Storage Explorer dalla riga di comando con il `--ignore-certificate-errors` flag. Quando viene avviato con questo flag, Storage Explorer ignorerà gli errori del certificato.

## <a name="sign-in-issues"></a>Problemi di accesso

### <a name="blank-sign-in-dialog"></a>Finestra di dialogo di accesso vuota

Le finestre di dialogo di accesso vuote spesso sono causate dalla richiesta da parte di ADFS Storage Explorer di eseguire un reindirizzamento, che non è supportato da Electron. Per risolvere questo problema, è possibile provare a usare il flusso del codice del dispositivo per l'accesso. A questo scopo, seguire questa procedura:

1. Menu Anteprima-> "usare l'accesso al codice del dispositivo".
2. Aprire la finestra di dialogo della connessione (tramite l'icona a forma di spina sulla barra verticale a sinistra o facendo clic su "Aggiungi account" nel pannello dell'account).
3. Scegliere l'ambiente a cui si vuole accedere.
4. Fare clic sul pulsante "Accedi".
5. Seguire le istruzioni visualizzate.

Se si riscontrano problemi durante l'accesso all'account che si desidera utilizzare perché il browser predefinito è già connesso a un account diverso, è possibile effettuare una delle operazioni seguenti:

1. Copiare manualmente il collegamento e il codice in una sessione privata del browser.
2. Copiare manualmente il collegamento e il codice in un browser diverso.

### <a name="reauthentication-loop-or-upn-change"></a>Ciclo di riautenticazione o modifica UPN

Se si è in un ciclo di riautenticazione o è stato modificato l'UPN di uno degli account, provare a eseguire i passaggi seguenti:

1. Rimuovere tutti gli account e quindi chiudere Storage Explorer
2. Eliminare la cartella .IdentityService dal computer. In Windows, la cartella si trova in `C:\users\<username>\AppData\Local`. Per Mac e Linux, è possibile trovare la cartella nella radice della directory dell'utente.
3. Se si usa Mac o Linux, sarà anche necessario eliminare la voce Microsoft. Developer. IdentityService dall'archivio chiavi del sistema operativo. Su Mac, l'archivio chiavi è l'applicazione "Gnome Keychain". Per Linux, l'applicazione viene in genere chiamata "Keyring", ma il nome può variare a seconda della distribuzione.

### <a name="conditional-access"></a>Accesso condizionale

L'accesso condizionale non è supportato quando si usa Storage Explorer in Windows 10, Linux o macOS. Ciò è dovuto a una limitazione nella libreria AAD utilizzata da Storage Explorer.

## <a name="mac-keychain-errors"></a>Errori di Keychain su Mac

Il Keychain macOS può talvolta andare in uno stato che causa problemi alla libreria di autenticazione di Storage Explorer. Per ottenere il keychain da questo stato, provare a eseguire i passaggi seguenti:

1. Chiudere Azure Storage Explorer.
2. Aprire il keychain (**cmd + barra spaziatrice**, digitare keychain, premere Invio).
3. Selezionare il keychain "login".
4. Fare clic sull'icona del lucchetto per bloccare il keychain (al termine il lucchetto assumerà un'animazione in posizione di blocco, cosa che potrebbe richiedere alcuni secondi in base a quali applicazioni sono aperte).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Avviare Azure Storage Explorer.
6. Dovrebbe essere visualizzato un menu a comparsa per segnalare qualcosa come "L'hub di servizio vuole accedere al keychain". In tal caso, immettere la password dell'account amministratore Mac e fare clic su **Consenti sempre** (o **Consenti** se **Consenti sempre** non è disponibile).
7. Provare a effettuare l'accesso.

### <a name="general-sign-in-troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi di accesso generale

* Se ci si trova in macOS e la finestra di accesso non viene mai visualizzata su "in attesa di autenticazione..." , quindi provare [questa procedura](#mac-keychain-errors)
* Riavviare Storage Explorer
* Se la finestra di autenticazione è vuota, attendere almeno un minuto prima di chiudere la finestra di dialogo di autenticazione.
* Verificare che le impostazioni del proxy e dei certificati siano configurate correttamente per il computer e per Storage Explorer.
* Se si è in Windows e si ha accesso a Visual Studio 2019 nello stesso computer e si accede, provare ad accedere a Visual Studio 2019. Dopo aver eseguito l'accesso a Visual Studio 2019, è possibile aprire Storage Explorer e visualizzare il proprio account nel pannello dell'account.

Se nessuno di questi metodi funziona [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Sottoscrizioni mancanti e tenant interrotti

Se non si riesce a recuperare le sottoscrizioni dopo aver eseguito l'accesso, provare i metodi di risoluzione dei problemi seguenti:

* Verificare che l'account abbia accesso alle sottoscrizioni previste. È possibile verificare l'accesso accedendo al portale per l'ambiente di Azure che si sta tentando di usare.
* Assicurarsi di aver eseguito l'accesso usando l'ambiente Azure corretto (Azure, Azure China 21Vianet, Azure Germania, Azure US Government o l'ambiente personalizzato).
* Se si è protetti da un proxy, verificare di aver configurato correttamente il proxy Storage Explorer.
* Provare a rimuovere e a aggiungere nuovamente l'account.
* Se è presente un collegamento "ulteriori informazioni", esaminare i messaggi di errore che vengono segnalati per i tenant che hanno avuto esito negativo. Se you'ren't è certo che cosa fare con i messaggi di errore visualizzati, è possibile [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Non è possibile rimuovere l'account collegato o la risorsa di archiviazione

Se non si è in grado di rimuovere un account collegato o una risorsa di archiviazione tramite l'interfaccia utente, è possibile eliminare manualmente tutte le risorse collegate eliminando le cartelle seguenti:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Chiudere Storage Explorer prima di eliminare le cartelle precedenti.

> [!NOTE]
> Se sono stati importati certificati SSL, eseguire il backup del contenuto della directory `certs`. In un secondo momento, è possibile usare il backup per reimportare i certificati SSL.

## <a name="proxy-issues"></a>Problemi di proxy

In primo luogo, assicurarsi che le informazioni seguenti immesse siano corrette:

* l'URL del proxy e il numero di porta
* nome utente e password se richiesto dal proxy

> [!NOTE]
> Storage Explorer non supporta i file di configurazione automatica del proxy per la configurazione delle impostazioni proxy.

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
* Immettere l'URL dell'host locale e il numero della porta dello strumento di rete come impostazioni proxy in Storage Explorer. Quando questa operazione viene eseguita correttamente, lo strumento di rete inizia la registrazione delle richieste di rete effettuate da Storage Explorer agli endpoint di gestione e del servizio. Ad esempio, immettere https://cawablobgrs.blob.core.windows.net/ per l'endpoint BLOB in un browser e si riceverà una risposta simile alla seguente, che suggerisce che la risorsa esiste, anche se non è possibile accedervi.

![esempio di codice](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Contattare l'amministratore del server proxy

Se le impostazioni del proxy sono corrette, è necessario contattare l'amministratore del server proxy e

* Assicurarsi che il proxy non blocchi il traffico verso gli endpoint di gestione o di risorsa di Azure.
* Verificare il protocollo di autenticazione usato dal server proxy. Storage Explorer attualmente non supporta i proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Messaggio di errore "Unable to Retrieve Children" (Impossibile recuperare gli elementi figlio)

Se si è connessi ad Azure tramite un proxy, verificare che le impostazioni del proxy siano corrette. Se viene concesso l'accesso a una risorsa dal proprietario della sottoscrizione o dell'account, verificare di avere le autorizzazioni di lettura o di elenco per tale risorsa.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Per la stringa di connessione non sono disponibili impostazioni di configurazione complete

Se viene visualizzato questo messaggio di errore, è possibile che non si disponga delle autorizzazioni necessarie per ottenere le chiavi per l'account di archiviazione. Per verificare se questo è il caso, passare al portale e individuare l'account di archiviazione. È possibile eseguire questa operazione facendo clic con il pulsante destro del mouse sul nodo dell'account di archiviazione e scegliendo "Apri nel portale". Al termine dell'operazione, passare al pannello "Chiavi di accesso". Se non si hanno le autorizzazioni per visualizzare le chiavi, viene visualizzata una pagina con il messaggio "non si ha accesso". Per risolvere questo problema, è possibile ottenere la chiave dell'account da un altro utente e collegarla con il nome e la chiave oppure è possibile chiedere a un utente di una firma di accesso condiviso per l'account di archiviazione e usarlo per associare l'account di archiviazione.

Se vengono visualizzate le chiavi dell'account, archiviare un problema in GitHub in modo da consentire la risoluzione del problema.

## <a name="issues-with-sas-url"></a>Problemi relativi all'URL SAS

Se ci si connette a un servizio tramite un URL SAS e si verifica questo errore:

* Verificare che l'URL abbia le autorizzazioni necessarie per la lettura o l'elenco delle risorse.
* Verificare che l'URL non sia scaduto.
* Se l'URL SAS si basa su un criterio di accesso, verificare che i criteri di accesso non siano stati revocati.

Se per errore è stato associato un URL SAS non valido e non è possibile annullare l'associazione, seguire questa procedura:

1. Durante l'esecuzione di Storage Explorer, premere il tasto F12 per aprire la finestra Strumenti di sviluppo.
2. Fare clic sulla scheda Applicazione e quindi fare clic su Risorsa di archiviazione locale > file:// nell'albero a sinistra.
3. Trovare la chiave associata al tipo di servizio dell'URI SAS che ha generato il problema. Ad esempio, se l'URI SAS non valido fa riferimento a un contenitore BLOB, cercare la chiave denominata `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Il valore della chiave deve essere una matrice JSON. Trovare l'oggetto associato all'URI non valido e rimuoverlo.
5. Premere CTRL + R per ricaricare Storage Explorer.

## <a name="linux-dependencies"></a>Dipendenze Linux

<!-- Storage Explorer 1.9.0 and later is available as a snap from the Snap Store. The Storage Explorer snap installs all of its dependencies with no extra hassle.

Storage Explorer requires the use of a password manager, which may need to be connected manually before Storage Explorer will work correctly. You can connect Storage Explorer to your system's password manager with the following command:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

You can also download the application .tar.gz file, but you'll have to install dependencies manually. -->

> [!IMPORTANT]
> Storage Explorer come specificato nel download di. tar. gz è supportato solo per le distribuzioni di Ubuntu. Altre distribuzioni non sono state verificate e possono richiedere pacchetti alternativi o aggiuntivi.

Questi pacchetti sono i requisiti più comuni per Storage Explorer in Linux:

* [Runtime di .NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` o `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage Explorer versione 1.7.0 e versioni precedenti richiedono .NET Core 2,0. Se è installata una versione più recente di .NET Core, sarà necessario applicare una [patch Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Se si esegue Storage Explorer 1.8.0 o versione successiva, si dovrebbe essere in grado di usare fino a .NET Core 2,2. Le versioni successive a 2,2 non sono state verificate per funzionare in questo momento.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. Scaricare Storage Explorer.
2. Installare il [runtime di .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Eseguire il comando seguente:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Scaricare Storage Explorer.
2. Installare il [runtime di .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Eseguire il comando seguente:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Scarica Storage Explorer
2. Installare il [runtime di .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Eseguire il comando seguente:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Scarica Storage Explorer
2. Installare il [runtime di .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Eseguire il comando seguente:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Applicazione di patch Storage Explorer per le versioni più recenti di .NET Core

Per Storage Explorer 1.7.0 o versioni precedenti, potrebbe essere necessario applicare patch alla versione di .NET Core usata da Storage Explorer.

1. Scaricare la versione 1.5.43 di StreamJsonRpc [da NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Cercare il collegamento "Scarica pacchetto" sul lato destro della pagina.
2. Dopo aver scaricato il pacchetto, modificare l'estensione del `.nupkg` file `.zip`da a.
3. Decomprimere il pacchetto.
4. Aprire la cartella `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Copiare `StreamJsonRpc.dll` nei percorsi seguenti all'interno della cartella Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Apri in Esplora da portale di Azure non funziona

Se il pulsante "Apri in Esplora" nel portale di Azure non funziona per l'utente, assicurarsi di usare un browser compatibile. I browser seguenti sono stati testati per la compatibilità.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Passaggi successivi

Se nessuna delle soluzioni funziona, [aprire un problema in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). È possibile accedere rapidamente a GitHub anche tramite il pulsante "Segnala un problema in GitHub" nell'angolo inferiore sinistro.

![Commenti e suggerimenti](./media/storage-explorer-troubleshooting/feedback-button.PNG)
