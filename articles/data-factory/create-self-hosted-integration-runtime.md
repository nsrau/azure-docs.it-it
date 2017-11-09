---
title: Creare il runtime di integrazione self-hosted in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare il runtime di integrazione self-hosted in Azure Data Factory, che consente alle data factory di accedere agli archivi dati in una rete privata.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: 63e4bb600d053a43c500b601a3942eb96ac16b07
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2017
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Come creare e configurare il runtime di integrazione self-hosted
Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le funzionalità di integrazione di dati in diversi ambienti di rete. Per informazioni dettagliate sul runtime di integrazione, vedere [Integration Runtime Overview](concepts-integration-runtime.md) (Panoramica del runtime di integrazione).

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-introduction.md).

Un runtime di integrazione self-hosted può eseguire attività di copia tra un archivio dati cloud e un archivio dati in una rete privata e inviare le attività di trasformazione sulle risorse di calcolo in una rete virtuale locale o di Azure. L'installazione del runtime di integrazione self-hosted è necessaria in un computer locale o in una macchina virtuale all'interno di una rete privata.  

Questo documento illustra come creare e configurare il runtime di integrazione self-hosted.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Procedura generale per installare il runtime di integrazione self-hosted
1.  Creare un runtime di integrazione self-hosted. Di seguito è illustrato un esempio di PowerShell:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  Scaricare e installare il runtime di integrazione self-hosted (nel computer locale).
3.  Recuperare la chiave di autenticazione e registrare il runtime di integrazione self-hosted con la chiave. Di seguito è illustrato un esempio di PowerShell:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>Flusso dei comandi e flusso di dati
Quando si spostano i dati tra ambiente cloud e locale, l'attività usa un runtime di integrazione self-hosted per trasferire i dati dall'origine dati locale al cloud e viceversa.

Di seguito è riportato un flusso di dati generale per il riepilogo dei passaggi per la copia con il runtime di integrazione self-hosted:

![Panoramica generale](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. Lo sviluppatore di dati crea un runtime di integrazione self-hosted in un'istanza di Azure Data Factory usando un cmdlet di PowerShell. Il portale di Azure attualmente non supporta questa funzionalità.
2. Lo sviluppatore di dati crea un servizio collegato per un archivio dati locale specificando l'istanza del runtime di integrazione self-hosted da usare per la connessione agli archivi dati. Una parte della configurazione del servizio collegato consiste nell'uso dell'applicazione "Gestione credenziali" (attualmente non supportata) per impostare i tipi di autenticazione e le credenziali. La finestra di dialogo dell'applicazione Gestione credenziali comunica con l'archivio dati per eseguire il test della connessione e con il runtime di integrazione self-hosted per salvare le credenziali.
4.  Il nodo del runtime di integrazione self-hosted crittografa la credenziale usando Windows Data Protection Application Programming Interface (DPAPI) e la salva in locale. Se più nodi vengono impostati per la disponibilità elevata, le credenziali vengono ulteriormente sincronizzate negli altri nodi. Ogni nodo la crittografa usando DPAPI e la archivia in locale. La sincronizzazione delle credenziali è trasparente per lo sviluppatore di dati e viene gestita dal runtime di integrazione self-hosted.    
5.  Il servizio Data Factory comunica con il runtime di integrazione self-hosted per la pianificazione e la gestione dei processi tramite un **canale di controllo** che usa una coda condivisa del bus di servizio di Azure. Quando è necessario eseguire un processo di attività, Data Factory accoda la richiesta con le informazioni sulle credenziali (se le credenziali non sono già archiviate nel runtime di integrazione self-hosted). Il runtime di integrazione self-hosted avvia il processo dopo il polling della coda.
6.  Il runtime di integrazione self-hosted copia i dati dall'archivio locale in una risorsa di archiviazione cloud o viceversa in base alla configurazione dell'attività di copia nella pipeline di dati. Per questo passaggio il runtime di integrazione self-hosted comunica direttamente con i servizi di archiviazione basati sul cloud, ad esempio Archiviazione BLOB di Azure, su un canale protetto (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Considerazioni sull'uso del runtime di integrazione self-hosted

- Un singolo runtime di integrazione self-hosted può essere usato per più origini dati locali. Tuttavia, un **singolo runtime di integrazione self-hosted viene associato a una sola istanza di Azure Data Factory** e non può essere condiviso con un'altra data factory.
- In un computer può essere installata **una sola istanza del runtime di integrazione self-hosted**. Si supponga di avere due data factory che richiedono l'accesso alle origini dati locali: è necessario installare il runtime di integrazione self-hosted nei due computer locali. In altre parole, un runtime di integrazione self-hosted viene associato a una data factory specifica
- Il **runtime di integrazione self-hosted non deve trovarsi sullo stesso computer dell'origine dati**. Se tuttavia il runtime di integrazione self-hosted è più vicino all'origine dati, impiega meno tempo per connettersi all'origine dati. Si consiglia di installare il runtime di integrazione self-hosted in un computer diverso da quello che ospita l'origine dati locale. Quando il runtime di integrazione self-hosted e l'origine dati si trovano in computer diversi, non si contendono le risorse.
- È possibile avere **più runtime di integrazione self-hosted in diversi computer che si connettono alla stessa origine dati locale**. Ad esempio, potrebbero essere disponibili due runtime di integrazione self-hosted che servono due data factory, ma la stessa origine dati locale viene registrata con entrambe le data factory.
- Se un gateway è già installato nel computer per uno scenario **Power BI**, installare un **runtime di integrazione self-hosted separato per Azure Data Factory** in un altro computer.
- Il runtime di integrazione self-hosted deve essere usato per supportare l'integrazione dei dati nella rete virtuale di Azure.
- Considerare l'origine dati come origine dati locale, ovvero protetta da firewall, anche quando si usa **ExpressRoute**. Usare il runtime di integrazione self-hosted per stabilire la connettività tra il servizio e l'origine dati.
- È necessario usare il runtime di integrazione self-hosted anche se l'archivio dati è nel cloud su una **macchina virtuale IaaS di Azure**.

## <a name="prerequisites"></a>Prerequisiti

- Sono supportati i **sistemi operativi** Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. L'installazione del runtime di integrazione self-hosted in un **controller di dominio non è supportata**.
- È necessario **.NET Framework 4.6.1 o versioni successive**. Se si installa il runtime di integrazione self-hosted in un computer Windows 7, installare .NET Framework 4.6.1 o versioni successive. Per informazioni dettagliate, vedere [Requisiti di sistema di .NET Framework](/dotnet/framework/get-started/system-requirements) .
- La **configurazione** consigliata per il computer di runtime di integrazione self-hosted è di almeno 2 GHz, 4 core, 8 GB di RAM e un disco da 80 GB.
- Se il computer host entra in stato di ibernazione, il runtime di integrazione self-hosted non risponde alle richieste di dati. Configurare quindi una combinazione per il risparmio di energia appropriata nel computer prima di installare il runtime di integrazione self-hosted. Se il computer è configurato per l'ibernazione, l'installazione del runtime di integrazione self-hosted invia un messaggio.
- È necessario essere un amministratore del computer per installare e configurare correttamente il runtime di integrazione self-hosted.
- Dato che le esecuzioni dell'attività di copia seguono una frequenza specifica, l'utilizzo delle risorse, ovvero CPU e memoria, nel computer segue lo stesso ciclo costituito da periodi di picco alternati a periodi di inattività. L'utilizzo delle risorse dipende molto anche dalla quantità di dati da spostare. Quando sono in corso più processi di copia, l'utilizzo delle risorse aumenta durante i periodi di picco.

## <a name="installation-best-practices"></a>Procedure consigliate per l'installazione
Il runtime di integrazione self-hosted può essere installato scaricando un pacchetto di installazione MSI dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Vedere l'[articolo sullo spostamento di dati tra origini locali e cloud](tutorial-hybrid-copy-powershell.md) per le istruzioni dettagliate.

- Configurare la combinazione per il risparmio di energia nel computer host del runtime di integrazione self-hosted in modo che il computer non entri in stato di ibernazione. Se il computer host entra in stato di ibernazione, il runtime di integrazione self-hosted passa offline.
- Eseguire regolarmente un backup delle credenziali associate al runtime di integrazione self-hosted.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Installare e registrare il runtime di integrazione self-hosted dall'Area download

1. Passare alla [pagina di download di Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Fare clic su **Scarica**, selezionare la versione appropriata (**a 32 bit** o **a 64 bit**) e fare clic su **Avanti**.
3. Eseguire direttamente il file **MSI** oppure salvarlo sul disco rigido ed eseguirlo.
4. Nella pagina di **benvenuto** selezionare una **lingua** e fare clic su **Avanti**.
5. **Accettare** il contratto di licenza e fare clic su **Avanti**.
6. Selezionare la **cartella** in cui installare il runtime di integrazione self-hosted e fare clic su **Avanti**.
7. Nella pagina **Pronto per l'installazione** fare clic su **Installa**.
8. Fare clic su **Fine** per completare l'installazione.
9. Ottenere la chiave di autenticazione con Azure PowerShell. Esempio di PowerShell per il recupero della chiave di autenticazione:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Nella pagina **Registra Runtime di integrazione (self-hosted)** di Gestione configurazione di Microsoft Integration Runtime in esecuzione sul computer, seguire questa procedura:
    1. Incollare la **chiave di autenticazione** nell'area di testo.
    2. Facoltativamente, fare clic su **Show authentication key** (Visualizza chiave di autenticazione) per visualizzare il testo della chiave.
    3. Fare clic su **Register**.


## <a name="high-availability-and-scalability"></a>Disponibilità e scalabilità elevate
Un runtime di integrazione self-hosted può essere associato a più computer locali. Questi computer sono chiamati nodi. È possibile avere fino a quattro nodi associati a un runtime di integrazione self-hosted. I vantaggi di avere più nodi (computer locali con un gateway installato) per un gateway logico sono:
1. Disponibilità più elevata del runtime di integrazione self-hosted in modo che non sia più il singolo punto di guasto nella soluzione per Big Data o nell'integrazione dei dati cloud con Azure Data Factory, assicurando la continuità fino a 4 nodi.
2. Miglioramento delle prestazioni e della velocità effettiva durante lo spostamento dati tra archivi dati locali e cloud. Ottenere altre informazioni sui [confronti delle prestazioni](copy-activity-performance.md).

È possibile associare più nodi semplicemente installando il software del runtime di integrazione self-hosted dall'[Area download](https://www.microsoft.com/download/details.aspx?id=39717) e registrandolo con una delle due chiavi di autenticazione ottenute dal cmdlet New-AzureRmDataFactoryV2IntegrationRuntimeKey, come descritto nell'[esercitazione](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Non è necessario creare un nuovo runtime di integrazione self-hosted per associare ogni nodo.

## <a name="system-tray-icons-notifications"></a>Notifiche/icone nell'area di notifica
Spostando il cursore sul messaggio di notifica o sull'icona nell'area di notifica, è possibile trovare i dettagli relativi allo stato del runtime di integrazione self-hosted.

![Notifiche dell'area di notifica](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Porte e firewall
È necessario considerare due firewall, ovvero il **firewall aziendale** in esecuzione nel router centrale dell'organizzazione e **Windows Firewall**, configurato come servizio daemon nel computer locale in cui è installato il runtime di integrazione self-hosted.

![Firewall](media\create-self-hosted-integration-runtime\firewall.png)

A livello di **firewall aziendale** è necessario configurare le porte in uscita e i domini seguenti:

Nomi di dominio | Porte | Descrizione
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Utilizzato per la comunicazione con il backend Data Movement Service
*.core.windows.net | 443 | Utilizzato per la copia di staging mediante il BLOB di Azure (se configurata)
*.frontend.clouddatahub.net | 443 | Utilizzato per la comunicazione con il backend Data Movement Service

A livello di **Windows Firewall** (a livello di computer) queste porte in uscita sono generalmente abilitate. In caso contrario, è possibile configurare le porte e i domini nel modo appropriato nel computer del runtime di integrazione self-hosted.

> [!NOTE]
> In base all'origine o ai sink, potrebbe essere necessario consentire altri domini e porte in uscita nel firewall aziendale o in Windows Firewall.
>
> Per alcuni database cloud (ad esempio, database SQL di Azure, Azure Data Lake e così via), potrebbe essere necessario consentire l'indirizzo IP del computer del runtime di integrazione self-hosted nella configurazione del firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiare i dati da un'origine a un sink
Verificare che le regole del firewall siano abilitate correttamente per il firewall aziendale, per Windows Firewall nel computer del runtime di integrazione self-hosted e per l'archivio dati stesso, per poter consentire al runtime di integrazione self-hosted di connettersi all'origine e al sink. Abilitare le regole per ogni archivio dati interessato dall'operazione di copia.

Ad esempio, per eseguire la copia da un **archivio dati locale a un sink di database SQL di Azure o a un sink di Azure SQL Data Warehouse**, seguire questa procedura:

- Consentire comunicazioni **TCP** in uscita sulla porta **1433** per Windows Firewall e il firewall aziendale.
- Configurare le impostazioni del firewall del server SQL di Azure aggiungendo l'indirizzo IP relativo al computer del runtime di integrazione self-hosted all'elenco degli indirizzi IP consentiti.

> [!NOTE]
> Se il firewall non consente la porta in uscita 1433, il runtime di integrazione self-hosted non riesce ad accedere direttamente ad Azure SQL. In questo caso, è possibile usare la [copia di staging](copy-activity-performance.md) sul database SQL di Azure o SQL Azure DW. In questo scenario è necessario solo HTTPS (porta 443) per lo spostamento dei dati.


## <a name="proxy-server-considerations"></a>Considerazioni sui server proxy
Se l'ambiente di rete aziendale usa un server proxy per accedere a Internet, configurare il runtime di integrazione self-hosted per l'uso delle impostazioni proxy appropriate. È possibile impostare il proxy durante la fase di registrazione iniziale.

![Specificare il proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

Il runtime di integrazione self-hosted usa il server proxy per connettersi al servizio cloud. Fare clic sul collegamento Modifica durante la configurazione iniziale. Viene visualizzata la finestra di dialogo delle impostazioni del proxy.

![Impostare il proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Sono disponibili tre opzioni di configurazione:

- **Non utilizzare proxy**: il runtime di integrazione self-hosted non usa in modo esplicito i proxy per connettersi ai servizi cloud.
- **Usa il proxy di sistema**: il runtime di integrazione self-hosted usa l'impostazione del proxy configurata in diahost.exe.config e diawp.exe.config. Se non è stato configurato alcun proxy in diahost.exe.config e diawp.exe.config, il runtime di integrazione self-hosted si connette al servizio cloud direttamente senza passare attraverso il proxy.
- **Usa proxy personalizzato**: configurare le impostazioni del proxy HTTP che il runtime di integrazione self-hosted deve usare al posto delle configurazioni in diahost.exe.config e diawp.exe.config. L'indirizzo e la porta sono valori obbligatori. Nome utente e password sono facoltativi a seconda dell'impostazione di autenticazione del proxy. Tutte le impostazioni vengono crittografate con Windows DPAPI nel runtime di integrazione self-hosted e archiviate localmente nel computer.

Il servizio host di runtime di integrazione viene riavviato automaticamente dopo avere salvato le impostazioni proxy aggiornate.

Dopo aver registrato correttamente il runtime di integrazione self-hosted, se si vuole visualizzare o aggiornare le impostazioni proxy, usare Gestione configurazione di Integration Runtime.

1.  Avviare **Gestione configurazione di Microsoft Integration Runtime**.
2.  Passare alla scheda **Impostazioni** .
3.  Fare clic sul collegamento **Cambia** nella sezione **Proxy HTTP** per avviare la finestra di dialogo **Imposta proxy HTTP**.
4.  Dopo avere fatto clic sul pulsante **Avanti**, una finestra di dialogo di avviso richiede l'autorizzazione per salvare le impostazioni del proxy e riavviare il servizio host di runtime di integrazione.

È possibile visualizzare e aggiornare il proxy HTTP tramite lo strumento Gestione configurazione.

![Visualizzare il proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Se si configura un server proxy con autenticazione NTLM, il servizio host di runtime di integrazione viene eseguito nell'account di dominio. Se in un secondo momento si modifica la password per l'account di dominio, ricordarsi di aggiornare le impostazioni di configurazione per il servizio e riavviarlo. Per questo requisito, si consiglia di usare un account di dominio dedicato per accedere al server proxy che non richieda l'aggiornamento frequente della password.

### <a name="configure-proxy-server-settings"></a>Configurare le impostazioni del server proxy

Se si seleziona l'impostazione **Usa il proxy di sistema** per il proxy HTTP, il runtime di integrazione self-hosted usa l'impostazione proxy contenuta in diahost.exe.config e diawp.exe.config. Se non è stato specificato alcun proxy in diahost.exe.config e diawp.exe.config, il runtime di integrazione self-hosted si connette al servizio cloud direttamente senza passare attraverso il proxy. La procedura seguente fornisce istruzioni per l'aggiornamento del file diahost.exe.config.

1. In Esplora file creare una copia sicura di C:\Programmi\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config per eseguire il backup del file originale.
2. Avviare Notepad.exe come amministratore e aprire il file di testo "C:\Programmi\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config". Il tag predefinito per system.net viene trovato come indicato nel codice seguente:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    È quindi possibile aggiungere i dettagli del server proxy, come illustrato nell'esempio seguente:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    È possibile aggiungere altre proprietà all'interno del tag del proxy per specificare le impostazioni obbligatorie, ad esempio scriptLocation. Per informazioni sulla sintassi, vedere [Elemento proxy (Impostazioni di rete)](https://msdn.microsoft.com/library/sa91de1e.aspx).

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Salvare il file di configurazione nel percorso originale, quindi riavviare il servizio host di runtime di integrazione self-hosted per rilevare le modifiche. Per riavviare il servizio: con l'applet dei servizi dal Pannello di controllo o da **Gestione configurazione di Integration Runtime** > fare clic sul pulsante **Arresta servizio** quindi su **Avvia servizio**. Se il servizio non viene avviato, è probabile che una sintassi non corretta del tag XML sia stata aggiunta al file di configurazione dell'applicazione modificato.

> [!IMPORTANT]
> Non dimenticare di aggiornare entrambi i file: diahost.exe.config e diawp.exe.config.

Oltre ai punti precedenti, è necessario assicurarsi anche Microsoft Azure sia stato aggiunto all'elenco aziendale degli elementi consentiti. È possibile scaricare l'elenco di indirizzi IP validi per Microsoft Azure dall' [Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possibili sintomi di problemi correlati al firewall e al server proxy
Se si verificano errori simili ai seguenti, potrebbero essere dovuti a una configurazione non corretta del firewall o del server proxy, che impedisce al runtime di integrazione self-hosted di connettersi a Data Factory per l'autenticazione. Per assicurarsi che la configurazione del firewall e del server proxy sia corretta, vedere la sezione precedente.

1.  Quando si prova a registrare il runtime di integrazione self-hosted, viene visualizzato l'errore seguente: "Non è stato possibile registrare questo nodo di Runtime di integrazione. Verificare la validità della chiave di autenticazione e che il servizio host servizio di integrazione sia in esecuzione nel computer. "
2.  Quando si apre Gestione configurazione di Integration Runtime, lo stato del gateway visualizzato può essere "**Disconnesso**" o "**Connessione**". Quando si visualizzano i registri eventi di Windows, in "Visualizzatore eventi" > "Registri applicazioni e servizi" > "Runtime di integrazione Microsoft", vengono visualizzati messaggi di errore simili al seguente:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="open-port-8060-for-credential-encryption"></a>Aprire la porta 8060 per la crittografia delle credenziali
L'applicazione **Impostazione credenziali** (attualmente non supportata) usa la porta in ingresso 8060 per inoltrare le credenziali al runtime di integrazione self-hosted quando si configura un servizio collegato locale nel portale di Azure. Durante la configurazione del runtime di integrazione self-hosted, per impostazione predefinita, l'installazione del runtime di integrazione self-hosted lo apre nel computer del runtime di integrazione self-hosted.

Se si usa un firewall di terze parti, è possibile aprire manualmente la porta 8050. In caso di problemi del firewall durante la configurazione del runtime di integrazione self-hosted, è possibile provare a usare il comando seguente per installare il runtime di integrazione self-hosted senza configurare il firewall.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se si sceglie di non aprire la porta 8060 nel computer del runtime di integrazione self-hosted, usare meccanismi diversi dall'uso dell'applicazione **Impostazione credenziali** per configurare le credenziali dell'archivio dati. È ad esempio possibile usare il cmdlet di PowerShell New-AzureRmDataFactoryV2LinkedServiceEncryptCredential. Per informazioni su come impostare le credenziali dell'archivio dati, vedere la sezione sull'impostazione delle credenziali e della sicurezza.


## <a name="next-steps"></a>Passaggi successivi
Vedere l'esercitazione seguente per le istruzioni dettagliate: [Tutorial: copy on-premises data to cloud (Esercitazione: Copiare i dati locali nel cloud)](tutorial-hybrid-copy-powershell.md).
