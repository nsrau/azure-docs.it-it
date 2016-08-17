<properties 
	pageTitle="Gateway di gestione dati per Data Factory | Microsoft Azure"
	description="Configurare un gateway dati per spostare dati tra origini locali e il cloud. Usare Gateway di gestione dati in Azure Data Factory per spostare dati." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="spelluru"/>

# Gateway di gestione dati
Il Gateway di gestione dati è un agente client che deve essere installato nell'ambiente locale per consentire il trasferimento dei dati tra gli archivi cloud e locali [supportati dall'attività di copia](data-factory-data-movement-activities.md##supported-data-stores).

Questo articolo si integra all’articolo [Spostare dati tra archivi cloud e locali](data-factory-move-data-between-onprem-and-cloud.md), nel quale viene illustrata la procedura dettagliata per creare una pipeline di Data Factory che utilizza il gateway per trasferire i dati da un database di SQL Server locale a un Blob di Azure. Questo articolo illustra informazioni approfondite sul Gateway di gestione dati.

## Funzionalità del Gateway di gestione dati
Il Gateway di gestione dati offre le funzionalità seguenti:

- Consente di modellare le origini dati locali e le origini dati nel cloud all'interno di un'unica istanza di Data Factory e di spostare i dati al suo interno.
- Consente di monitorare e gestire lo stato del gateway in un'unica schermata dal pannello di Data Factory.
- Consente di gestire in modo sicuro l'accesso alle origini dati locali.
	- Non è richiesta alcuna modifica del firewall aziendale. Il gateway stabilisce soltanto connessioni basate su HTTP in uscita per accedere a Internet.
	- È possibile crittografare le informazioni sulle credenziali per gli archivi dati locali usando il proprio certificato.
- Consente di spostare i dati in modo efficace: i dati vengono trasferiti in parallelo e sono resilienti ai problemi di rete intermittente grazie alla logica di ripetizione dei tentativi automatica.

## Flusso di dati quando si utilizza il gateway
Quando si usa un'attività di copia in una pipeline di dati per inserire dati locali nel cloud ai fini di una successiva elaborazione o per esportare nuovamente i dati memorizzati nel cloud in un archivio dati locale, l'attività di copia usa un gateway per trasferire i dati dall'origine dati locale nel cloud e viceversa.

Di seguito sono riportati un flusso di dati generale e un riepilogo dei passaggi per la copia con il gateway di dati: ![Flusso di dati mediante gateway](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1.	Viene creato un nuovo gateway per l'istanza di Data factory di Azure usando il [portale di Azure](https://portal.azure.com) oppure un [cmdlet di PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2.	Viene creato un servizio collegato per un archivio dati locale specificando il gateway. Una parte della configurazione del servizio collegato consiste nell'uso dell'applicazione Impostazione credenziali per specificare i tipi di autenticazione e le credenziali. La finestra di dialogo dell'applicazione Impostazione credenziali comunica con l'archivio dati per eseguire il test della connessione e con il gateway per salvare le credenziali.
3. Il gateway crittografa le credenziali tramite il certificato associato al gateway (fornito dallo sviluppatore) prima di salvare le credenziali nel cloud.
4. Il servizio Data Factory comunica con il gateway per la pianificazione e la gestione dei processi tramite un canale di controllo che usa una coda condivisa del bus di servizio di Azure. Quando occorre avviare il processo di attività di copia, Data Factory accoda la richiesta insieme alle informazioni sulle credenziali. Il gateway avvia il processo dopo avere eseguito il polling della coda.
5.	Il gateway decrittografa le credenziali tramite lo stesso certificato e quindi si connette all'archivio dati locale con il tipo di autenticazione appropriato e le credenziali.
6.	Il gateway copia i dati dall'archivio dati locale in una risorsa di archiviazione cloud o viceversa in base alla configurazione dell'attività di copia nella pipeline di dati. Nota: per questo passaggio il gateway comunica direttamente con un servizio di archiviazione basato sul cloud, ad esempio BLOB di Azure, Azure SQL e così via, su un canale protetto (HTTPS).

## Considerazioni sull'uso del gateway
- Una singola istanza del Gateway di gestione dati può essere usata per più origini dati locali. Tenere presente, però, che **una sola istanza del gateway può essere associata a una singola istanza di Data factory di Azure** e non può essere condivisa con altre istanze di Data factory.
- In un computer può essere installata **una sola istanza del Gateway di gestione dati**. Si supponga di avere due istanze di Data Factory che richiedono l'accesso alle origini dati locali: è necessario installare i gateway nei due computer locali in cui ogni gateway sia associato a un'istanza separata di Data Factory.
- Il **gateway non deve trovarsi nello stesso computer dell'origine dati**; tuttavia, la prossimità all'origine dati riduce il tempo di connessione tra il gateway e l'origine dati. Si consiglia di installare il gateway in un computer diverso da quello che ospita l'origine dati locale in modo che il gateway non si contenda le risorse con l'origine dati.
- È possibile disporre di **più gateway su diversi computer che si connettono alla stessa origine dati locale**. Ad esempio, potrebbero essere disponibili due gateway che servono due data factory, ma la stessa origine dati locale viene registrata con entrambe le data factory.
- Se un gateway è già installato nel computer per uno scenario **Power BI**, installare un **gateway separato per Data factory di Azure** in un altro computer.
- È necessario **usare il gateway anche quando si usa ExpressRoute**.
- È necessario considerare l'origine dati come un'origine dati locale, ovvero come se fosse protetta da firewall, anche quando si usa **ExpressRoute** e **il gateway** per stabilire la connettività tra il servizio e l'origine dati.
- È necessario **usare il gateway** anche se l'archivio dati è nel cloud in una **VM IaaS di Azure**.

## Installare Gateway di gestione dati

### Installazione del gateway - Prerequisiti
- Sono supportati i **sistemi operativi** Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. L’installazione di Gateway di gestione dati sul controller di dominio al momento non è supportata.
- La **configurazione** consigliata per il computer gateway è di almeno 2 GHz, 4 core, 8 GB di RAM e un disco da 80 GB.
- Se il computer host entra in stato di ibernazione, il gateway non può rispondere alla richiesta di dati. Pertanto, configurare una **combinazione per il risparmio di energia** appropriata nel computer prima di installare il gateway. L'installazione del gateway invia un messaggio se il computer è configurato per l'ibernazione.
- È necessario essere un amministratore nella macchina per installare e configurare correttamente il gateway di gestione dati. È possibile aggiungere altri utenti al gruppo di Windows locale degli **utenti del gateway di gestione dati**. I membri di questo gruppo potranno usare lo strumento Gestione configurazione del gateway di gestione dati per configurare il gateway.

Dato che le esecuzioni delle attività di copia seguono una frequenza specifica, l'utilizzo delle risorse nel computer (CPU e memoria) segue lo stesso ciclo costituito da periodi di massimo utilizzo alternati a periodi di inattività. L'utilizzo delle risorse dipende molto anche dalla quantità di dati da spostare. Quando sono in corso più processi di copia, l'utilizzo delle risorse aumenta durante i periodi di massimo utilizzo. Dato che la configurazione illustrata sopra corrisponde alla configurazione minima, a seconda del carico previsto per lo spostamento dei dati è sempre consigliabile disporre di maggiori risorse.

### Installazione
Il Gateway di gestione dati può essere installato nei seguenti modi:

- Scaricando un pacchetto di installazione MSI dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Il pacchetto con estensione msi può inoltre essere usato per aggiornare il Gateway di gestione dati esistente alla versione più recente mantenendo tutte le impostazioni.
- Facendo clic sul collegamento **Scarica e installa il gateway dati** in INSTALLAZIONE MANUALE o **Installa direttamente sul computer** in INSTALLAZIONE RAPIDA. Vedere l’articolo [Spostare i dati tra gli ambienti cloud e locali](data-factory-move-data-between-onprem-and-cloud.md) per le istruzioni dettagliate sull’installazione rapida. La procedura manuale consente di accedere all'area download, mentre le istruzioni per scaricare e installare il gateway dall'area download sono nella sezione successiva.


### Procedure consigliate per l'installazione:
1.	Configurare la combinazione per il risparmio di energia nel computer host del gateway in modo che il computer non entri in stato di ibernazione. Se il computer host entra in stato di ibernazione, il gateway non può rispondere alla richiesta di dati.
2.	È necessario eseguire il backup del certificato associato al gateway.

### Installare il gateway dall'area download
1. Andare alla [pagina di download del Gateway di gestione dati di Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Fare clic su **Scarica**, selezionare la versione appropriata (**a 32 bit** o **a 64 bit**), fare clic su **Avanti**.
3. Eseguire direttamente il file **MSI** oppure salvarlo sul disco rigido ed eseguirlo.
4. Nella pagina di **benvenuto**, selezionare una **lingua** e fare clic su **Avanti**.
5. **Accettare** il contratto di licenza e fare clic su **Avanti**.
6. Selezionare la **cartella** per installare il gateway e fare clic su **Avanti**.
7. Nella pagina **Pronto per l'installazione**, fare clic su **installa**.
8. Fare clic su **Fine** per completare l’installazione.
9. Ottenere la chiave dal Portale di Azure. Vedere la sezione successiva per le istruzioni dettagliate.
10. Nella pagina **Registra gateway** di **Gestione configurazione di Gateway di gestione dati** in esecuzione sul la macchina, incollare la chiave nel testo (facoltativo), fare clic su **Mostra chiave del gateway** per visualizzare il testo della chiave e selezionare **Registra**.

### Ottenere la chiave

#### Se non è ancora stato creato un gateway logico nel portale
Seguire i passaggi della procedura dettagliata dell’articolo [Spostare i dati tra gli ambienti cloud e locali](data-factory-move-data-between-onprem-and-cloud.md) per creare un nuovo gateway nel portale e ottenere la chiave dal pannello **Configura**.

#### Se è già stato creato un gateway logico nel portale
1. Nel portale di Azure, andare al pannello **Data Factory** e fare clic sul riquadro **Servizi collegati**.

	![Pannello Data factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Nel pannello **Servizi collegati**, selezionare il **gateway** logico creato nel portale.

	![gateway logico](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
2. Nel pannello **Gateway dati**, fare clic su **Scarica e installa il gateway dati**.

	![Link di download nel portale](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
3. Nel pannello **Configura**, fare clic su **Ricrea chiave**. Fare clic su Sì nel messaggio di avviso dopo averlo letto con attenzione.

	![Ricrea chiave](media/data-factory-data-management-gateway/recreate-key-button.png)
4. Fare clic sul pulsante Copia accanto alla chiave per copiarla negli Appunti.
	
	![Copiare la chiave](media/data-factory-data-management-gateway/copy-gateway-key.png)

## Gateway di gestione dati di Configuration Manager 
Dopo aver installato il gateway, è possibile avviare Gestione configurazione di Gateway di gestione dati in uno dei modi seguenti:

- Nella finestra **Cerca** digitare **Gateway di gestione dati** per accedere a questa utilità.
- Eseguire il file ConfigManager.exe nella cartella: C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared
 
### Home page
Nella Home page è possibile eseguire le operazioni seguenti:

- Visualizzare lo stato del gateway per vedere se è connesso al servizio cloud o meno.
- **Registrare** utilizzando una chiave dal portale.
- **Interrompere** e avviare il **servizio host di Gateway di gestione dati** sulla macchina gateway.
- **Pianificare gli aggiornamenti** a un orario specifico del giorno.
- Visualizzare la data dell’**ultimo aggiornamento** del gateway.

### Pagina Impostazioni
La pagina Impostazioni consente di eseguire le operazioni seguenti:

- Visualizzare, modificare ed esportare il **certificato** utilizzato dal gateway. Questo certificato viene usato per crittografare le credenziali dell'origine dati.
- Modificare la **porta HTTPS** per l'endpoint. Il gateway apre una porta per impostare le credenziali dell'origine dati.
- **Stato** dell'endpoint
- La visualizzazione del **certificato SSL** viene usata per la comunicazione SSL tra il gateway e il portale per impostare le credenziali per le origini dati.

### Pagina diagnostica
La pagina Diagnostica consente di eseguire le operazioni seguenti:

- Abilitare la **registrazione** dettagliata, visualizzare i log nel visualizzatore eventi e inviare i log a Microsoft in caso di errore.
- **Test della connessione** su un'origine dati.

### Pagina della guida
La Pagina della guida consente di eseguire le operazioni seguenti:

- Breve descrizione del gateway
- Numero di versione
- Collegamenti alla Guida in linea, informativa sulla privacy e contratto di licenza.

## Notifiche/icone nell'area di notifica
L'immagine seguente illustra alcune delle icone visualizzate nell'area di notifica.

![icone dell'area di notifica](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Se si sposta il cursore sul messaggio di notifica o sull'icona nell'area di notifica, verranno visualizzati i dettagli relativi allo stato dell'operazione del gateway o di aggiornamento in una finestra popup.

## Aggiornare Gateway di gestione dati
Per impostazione predefinita, Gateway di gestione dati viene aggiornato automaticamente quando è disponibile una versione più recente del gateway. Il gateway non viene aggiornato finché non vengono eseguite tutte le operazioni pianificate. Nessun'altra attività viene elaborata dal gateway fino al completamento dell'operazione di aggiornamento. Se l'aggiornamento non riesce, viene eseguito il rollback del gateway alla versione precedente.

Il tempo di aggiornamento pianificato nel portale verrà visualizzato nel pannello delle proprietà del gateway, nella home page di Gestione configurazione di Gateway di gestione dati e nel messaggio di notifica nell'area di notifica.

La scheda Home di Gestione configurazione di Gateway di gestione dati visualizza la pianificazione dell'aggiornamento, nonché la data e l'ora dell'ultima installazione o dell'ultimo aggiornamento del gateway.

![Pianificare gli aggiornamenti](media/data-factory-data-management-gateway/UpdateSection.png)

È disponibile un'opzione per installare l'aggiornamento immediatamente o attendere che il gateway venga aggiornato automaticamente all'ora pianificata. Ad esempio, la schermata seguente mostra il messaggio di notifica in Gestione configurazione di Gateway di gestione dati con il pulsante Aggiorna su cui si fa clic per avviare immediatamente l'installazione.

![Aggiorna in Gestione configurazione di Gateway di gestione dati](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Il messaggio di notifica nell'area di notifica sarà simile al seguente:

![Messaggio nell'area di notifica](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Lo stato dell'operazione di aggiornamento, manuale o automatica, sarà visualizzato nell'area di notifica. Alla successiva apertura di Gestione configurazione di Gateway di gestione dati verrà visualizzato un messaggio sulla barra di notifica per indicare che il gateway è stato aggiornato, insieme a un collegamento [all'argomento Novità](data-factory-gateway-release-notes.md).

### Per abilitare o disabilitare la funzionalità di aggiornamento automatico
È possibile abilitare/disabilitare la funzionalità di aggiornamento seguendo questa procedura:

1. Avviare Windows PowerShell nel computer gateway.
2. Passare alla cartella C:\\Programmi\\Microsoft Data Management Gateway\\1.0\\PowerShellScript.
3. Eseguire il comando seguente per disattivare (disabilitare) la funzionalità di aggiornamento automatico.

		.\GatewayAutoUpdateToggle.ps1  -off

4. Per riattivarla:
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Considerazioni su porte e sicurezza
È necessario considerare due firewall, ovvero il **firewall aziendale** in esecuzione nel router centrale dell'organizzazione e **Windows Firewall**, configurato come servizio daemon nel computer locale in cui è installato il gateway.

![firewall](./media/data-factory-data-management-gateway/firewalls.png)

## Connettere il gateway ai servizi cloud
Per mantenere la connettività del gateway con Azure Data Factory e altri servizi cloud è necessario verificare che la regola in uscita per le porte **TCP** **80** e **443** sia configurata. È possibile abilitare facoltativamente le porte da **9350** a **9354** che vengono usate dal bus di servizio di Microsoft Azure per stabilire la connessione tra Azure Data Factory e il Gateway di gestione dati e possono migliorare le prestazioni della comunicazione tra di essi.

A livello di firewall aziendale è necessario configurare le porte in uscita e i domini seguenti:

| Nomi di dominio | Porte | Descrizione |
| ------ | --------- | ------------ |
| *.servicebus.windows.net | 443, 80 | Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) | 
| *.servicebus.windows.net | 9350-9354, 5671 | Inoltro del bus di servizio su TCP facoltativo | 
| *.core.windows.net | 443 | HTTPS | 
| *.clouddatahub.net | 443 | HTTPS | 
| graph.windows.net | 443 | HTTPS |
| login.windows.net | 443 | HTTPS | 

A livello di Windows Firewall queste porte in uscita sono generalmente abilitate. In caso contrario, è possibile configurare le porte e i domini nel modo appropriato nel computer gateway.

## Impostare le credenziali del gateway
La porta in ingresso **8050** verrà usata dall'applicazione di **impostazione delle credenziali** per inoltrare le credenziali al gateway quando si configura un servizio collegato locale nel portale di Azure, come illustrato più avanti nell'articolo. Durante la configurazione del gateway, l'installazione di Gateway di gestione dati apre la porta nel computer gateway per impostazione predefinita.
 
Se si usa un firewall di terze parti, è possibile aprire manualmente la porta 8050. In caso di problemi del firewall durante la configurazione del gateway, è possibile provare a usare il comando seguente per installare il gateway senza configurare il firewall.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Se si sceglie di non aprire la porta 8050 nel computer gateway, per configurare un servizio collegato locale sarà necessario usare meccanismi diversi dall'uso dell'applicazione di **impostazione delle credenziali** per configurare le credenziali dell'archivio dati. È ad esempio possibile usare il cmdlet di PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Per informazioni su come impostare le credenziali dell'archivio dati, vedere la sezione [Impostare le credenziali e la sicurezza](#set-credentials-and-securityy).

**Per copiare dati da un archivio dati di origine a un archivio dati sink:**

È necessario assicurarsi che le regole del firewall siano abilitate correttamente sul firewall aziendale, su Windows Firewall nel computer del gateway e sull'archivio dati stesso, in modo da consentire al gateway di connettersi all'origine e al sink. È necessario abilitare le regole per ogni archivio dati interessato dall'operazione di copia.

Per copiare ad esempio da **un archivio dati locale a un sink del database SQL di Azure o un sink di Azure SQL Data Warehouse** è necessario consentire la comunicazione **TCP** in uscita sulla porta **1433** per Windows Firewall e il firewall aziendale. È anche necessario configurare le impostazioni per il firewall del server di Azure SQL per aggiungere l'indirizzo IP del computer gateway all'elenco di indirizzi IP consentiti.

### Considerazioni sui server proxy
Per impostazione predefinita, Gateway di gestione dati userà le impostazioni del proxy da Internet Explorer e userà le credenziali predefinite per accedervi. Se questo non soddisfa le proprie esigenze, è possibile configurare ulteriormente le **impostazioni del server proxy**, come illustrato più avanti, per assicurarsi che il gateway possa connettersi ad Azure Data Factory:

1.	Dopo l'installazione del servizio Gateway di gestione dati, in Esplora file creare una copia sicura di "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config" per eseguire il backup del file originale.
2.	Avviare Notepad.exe come amministratore e aprire il file di testo "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config". Il tag predefinito per system.net è il seguente:

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	È quindi possibile aggiungere i dettagli relativi al server proxy, ad esempio l'indirizzo del proxy, all'interno di tale tag padre, ad esempio:

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	È possibile aggiungere altre proprietà all'interno del tag del proxy per specificare le impostazioni obbligatorie, ad esempio scriptLocation. Per informazioni sulla sintassi, vedere [Elemento <proxy> (Impostazioni di rete)](https://msdn.microsoft.com/library/sa91de1e.aspx).

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. Salvare il file di configurazione nel percorso originale, quindi riavviare il servizio Gateway di gestione dati per rilevare le modifiche. È possibile eseguire questa operazione da **Start** > **Services.msc** oppure in **Gestione configurazione di Gateway di gestione dati** > fare clic sul pulsante **Arresta servizio** e quindi su **Avvia servizio**. Se il servizio non viene avviato, è probabile che una sintassi non corretta del tag XML sia stata aggiunta al file di configurazione dell'applicazione modificato.

Oltre ai punti precedenti, è necessario assicurarsi anche Microsoft Azure sia stato aggiunto all'elenco elementi consentiti aziendale. È possibile scaricare l'elenco di indirizzi IP validi per Microsoft Azure dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### Possibili sintomi di problemi correlati al firewall e al server proxy:
Se si verificano errori analoghi ai seguenti, è possibile che siano dovuti a una configurazione non corretta del firewall o del server proxy, che impedisce al servizio Gateway di gestione dati di connettersi ad Azure Data Factory per l'autenticazione. Per assicurarsi che la configurazione del firewall e del server proxy sia corretta, vedere la sezione precedente.

1.	Quando si tenta di registrare il gateway, viene visualizzato l'errore seguente: "Impossibile registrare la chiave del gateway. Prima di tentare di registrare nuovamente la chiave del gateway, verificare che il Gateway di gestione dati sia in stato di connessione e il relativo servizio host sia stato avviato".
2.	Quando si apre Gestione configurazione, lo stato del gateway visualizzato può essere "Disconnesso" o "Connessione". Quando si visualizzano i registri eventi di Windows, in "Visualizzatore eventi" > "Registri applicazioni e servizi" > "Gateway di gestione dati" vengono visualizzati messaggi di errore, ad esempio "Impossibile connettersi al server remoto" oppure "Un componente del Gateway di gestione dati non risponde e verrà riavviato automaticamente. Nome del componente: Gateway".

## Risoluzione dei problemi del gateway

- Per informazioni dettagliate, vedere i log del gateway nei registri eventi di Windows. a cui è possibile accedere tramite il **Visualizzatore eventi** di Windows in **Registri applicazioni e servizi** > **Gateway di gestione dati**. Durante la risoluzione dei problemi correlati al gateway è possibile cercare eventi a livello di errore nel Visualizzatore eventi.
- Se il gateway smette di funzionare dopo la **modifica del certificato**, riavviare (arrestare e avviare) il **servizio Gateway di gestione dati** con lo strumento Gestione configurazione di Gateway di gestione dati di Microsoft o l'applet del pannello di controllo Servizi. Se viene ancora visualizzato un errore, è necessario assegnare le autorizzazioni esplicite per l'utente del servizio Gateway di gestione dati ad accedere al certificato nella console di gestione dei certificati (Certmgr.msc). L'account utente predefinito per il servizio è: **NT Service\\DIAHostService**.
- Se l'applicazione **Gestione credenziali** non riesce a **crittografare** le credenziali quando si fa clic sul pulsante Crittografa nell'editor di Data Factory, verificare che l'applicazione sia in esecuzione nel computer in cui è installato il gateway. In caso contrario, eseguire l'applicazione nel computer gateway e provare a crittografare le credenziali.
- Se vengono visualizzati errori di connessione all'archivio dati o relativi al driver, avviare **Gestione configurazione di Gateway di gestione dati** nel computer gateway, passare alla scheda **Diagnostica**, selezionare o immettere i valori appropriati per i campi nel gruppo **Esegui il test della connessione a un'origine dati locale con questo gateway** e fare clic su **Test connessione** per verificare se è possibile connettersi all'origine dati locale dal computer gateway usando le informazioni e le credenziali di connessione. Se il test della connessione non riesce dopo aver installato un driver, riavviare il gateway in modo che rilevi la modifica più recente.

	![Test di connessione](./media/data-factory-data-management-gateway/TestConnection.png)

### Inviare i log del gateway a Microsoft
Se si verificano problemi con il gateway ed è necessario contattare il supporto Microsoft, all’utente potrebbe essere richiesto di condividere i log del gateway. La versione del gateway consente di condividere facilmente i log necessari tramite doppio clic sul pulsante in Gestione configurazione gateway.

1. Passare alla scheda **Diagnostica** di Gestione configurazione gateway.
 
	![Gateway di gestione dati - scheda Diagnostica](media/data-factory-data-management-gateway/data-management-gateway-diagnostics-tab.png)
2. Fare clic sul collegamento **Invia log** per aprire la finestra di dialogo seguente.

	![Gateway di gestione dati - Invia log](media/data-factory-data-management-gateway/data-management-gateway-send-logs-dialog.png)
3. (facoltativo) Fare clic su **Visualizza log** per esaminare i log nel visualizzatore eventi.
4. (facoltativo) Fare clic su **Privacy** per esaminare l’informativa sulla privacy per i servizi online di Microsoft.
3. Una volta soddisfatti di ciò che si intende caricare, fare clic su **Invia log** per inviare i log degli ultimi 7 giorni a Microsoft per la risoluzione dei problemi. Lo stato dell'operazione dovrebbe corrispondere a quello indicato sotto.

	![Gateway di gestione dati - stato dell’operazione Invia log](media/data-factory-data-management-gateway/data-management-gateway-send-logs-status.png)
4. Al termine dell'operazione, si aprirà una finestra di dialogo come quella mostrata sotto.
	
	![Gateway di gestione dati - stato dell’operazione Invia log](media/data-factory-data-management-gateway/data-management-gateway-send-logs-result.png)
5. Annotare l’**ID report** e condividerlo con il supporto Microsoft. L'ID report serve per individuare i log del gateway caricati per la risoluzione dei problemi. L'ID report viene salvato anche nel visualizzatore eventi come riferimento. È possibile trovarlo cercando l'ID evento "25" e verificando la data e l’ora.
	
	![Gateway di gestione dati - ID report dell’operazione Invia log](media/data-factory-data-management-gateway/data-management-gateway-send-logs-report-id.png)

### Archiviare i log del gateway nel computer host del gateway
Esistono dei casi in cui si verificano problemi con il gateway e non è possibile condividere direttamente i log del gateway:

- quando gli utenti installano e registrano il gateway manualmente;
- quando gli utenti tentano di registrare il gateway con una chiave rigenerata in Gestione configurazione;
- quando gli utenti tentano di inviare i log ma non è possibile connettersi al servizio host del gateway;

In questi casi, è possibile salvare i log del gateway come file zip e condividerlo dopo aver contattato il supporto Microsoft. Ad esempio, se si verifica un errore durante la registrazione del gateway come illustrato di seguito:

![Gateway di gestione dati - errore di registrazione](media/data-factory-data-management-gateway/data-management-gateway-registration-error.png)

Fare clic sul collegamento ai log **Archivia gateway** per archiviare e salvare i log, quindi condividere il file zip con il supporto Microsoft.

![Gateway di gestione dati - Archivia log](media/data-factory-data-management-gateway/data-management-gateway-archive-logs.png)

 
## Spostare il gateway da un computer a un altro
Questa sezione illustra la procedura per spostare il client del gateway da un computer a un altro.

2. Nel portale passare alla **Home page di Data factory** e fare clic sul riquadro **Servizi collegati**.

	![Collegamento Gateway dati](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
3. Selezionare il gateway nella sezione **GATEWAY DATI** nel pannello **Servizi collegati**.
	
	![Pannello Servizi collegati con gateway selezionato](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
4. Nel pannello **Gateway dati** fare clic su **Scaricare e installare il gateway dati**.
	
	![Collegamento di download del gateway](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
5. Nel pannello **Configura** fare clic su **Scaricare e installare il gateway dati** e seguire le istruzioni per installare il gateway dati nel computer.

	![Pannello Configura](./media/data-factory-data-management-gateway/ConfigureBlade.png)
6. Tenere aperto **Gestione configurazione di Gateway di gestione dati di Microsoft**.
 
	![Gestione configurazione](./media/data-factory-data-management-gateway/ConfigurationManager.png)
7. Nel pannello **Configura** del portale fare clic su **Ricrea chiave** nella barra dei comandi e su **Sì** per il messaggio di avviso. Fare clic sul **pulsante Copia** accanto al testo della chiave per copiare la chiave negli Appunti. Il gateway nel computer precedente smetterà di funzionare non appena si ricrea la chiave.
	
	![Ricrea chiave](./media/data-factory-data-management-gateway/RecreateKey.png)
	 
8. Incollare la **Chiave** nella casella di testo della pagina **Registra gateway** in **Gestione configurazione di Gateway di gestione dati** nel computer. (Facoltativo) Selezionare la casella di controllo **Mostra chiave del gateway** per visualizzare il testo della chiave.
 
	![Copia della chiave e registrazione](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
9. Fare clic su **Registra** per registrare il gateway con il servizio cloud.
10. Nella scheda **Impostazioni**, fare clic su **Modifica** per selezionare lo stesso certificato usato con il gateway precedente, immettere la **password** e fare clic su **Fine**.
 
	![Specificare un certificato](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

	È possibile esportare un certificato dal gateway precedente seguendo questa procedura: avviare Gestione configurazione di Gateway di gestione dati nel computer precedente, passare alla scheda **Certificato**, fare clic su **Esporta** e seguire le istruzioni.
10. Dopo la registrazione del gateway, nella home page di Gestione configurazione di Gateway di gestione dati verranno visualizzati **Registrazione** impostato su **Registrato** e **Stato** impostato su **Avviato**.

## Impostare le credenziali e la sicurezza
Per crittografare le credenziali in Data Factory Editor, seguire questa procedura:

1. Avviare il browser web nel **computer gateway**, passare al [portale di Azure](http://portal.azure.com), cercare la data factory se necessario, aprire la data factory nel pannello **DATA FACTORY** e quindi fare clic su **Creare e distribuire** per avviare l'editor di Data Factory.
1. Fare clic su un **servizio collegato** esistente nella visualizzazione albero per vedere la relativa definizione JSON o creare un nuovo servizio collegato che richieda un gateway di gestione dati, ad esempio SQL Server o Oracle.
2. Nell'editor JSON specificare il nome del gateway per la proprietà **gatewayName**.
3. Immettere il nome del server per la proprietà **Origine dati** in **connectionString**.
4. Immettere il nome del server per la proprietà **Catalogo iniziale** in **connectionString**.
5. Fare clic sul pulsante **Crittografa** sulla barra dei comandi per avviare l'applicazione ClickOnce **Gestione credenziali**. Verrà visualizzata la finestra di dialogo **Impostazione credenziali**. ![Finestra di dialogo Impostazione credenziali](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
6. Nella finestra di dialogo **Impostazione credenziali** seguire questa procedura:
	1.	Selezionare l'**autenticazione** che sarà usata dal servizio Data Factory per connettersi al database.
	2.	Per l'impostazione **NOME UTENTE** immettere il nome dell'utente che ha accesso al database.
	3.	Per l'impostazione **PASSWORD** immettere la password dell'utente.
	4.	Fare clic su **OK** per crittografare le credenziali e chiudere la finestra di dialogo.
5.	Verrà ora visualizzata una proprietà **encryptedCredential** in **connectionString**.
		
			{
	    		"name": "SqlServerLinkedService",
		    	"properties": {
		        	"type": "OnPremisesSqlServer",
			        "description": "",
		    	    "typeProperties": {
		    	        "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
		            	"gatewayName": "adftutorialgateway"
		        	}
		    	}
			}

Se si accede al portale da un computer diverso dal computer del gateway, è necessario assicurarsi che l'applicazione di gestione credenziali possa connettersi al computer del gateway. Se l'applicazione non riesce a raggiungere il computer del gateway, non sarà possibile impostare le credenziali per l'origine dati e per testare la connessione all'origine dati.

Quando si usa l'applicazione **Impostazione credenziali** avviata dal portale di Azure per impostare le credenziali per un'origine dati locale, il portale crittografa le credenziali usando il certificato specificato nella scheda **Certificato** di **Gestione configurazione di Gateway di gestione dati** del computer gateway.

Se si vuole un approccio basato su API per crittografare le credenziali, è possibile usare il cmdlet di PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Questo cmdlet consente di crittografare le credenziali mediante il certificato usato dal gateway. È possibile aggiungere le credenziali crittografate restituite da questo cmdlet all'elemento **EncryptedCredential** di **connectionString** nel file JSON da usare con il cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) oppure nel frammento di codice JSON dell'editor di Data Factory nel portale.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Esiste un altro approccio per impostare le credenziali usando l'editor delle data factory. Se si crea un servizio collegato di SQL Server usando l'editor e si immettono le credenziali in testo normale, le credenziali vengono crittografate tramite un certificato che appartiene al servizio Data Factory, NON tramite il certificato usato dal gateway. Anche se questo approccio potrebbe apparire leggermente più veloce, in alcuni casi risulta meno sicuro. È pertanto consigliabile seguire questo approccio solo per scopi di sviluppo o di test.


## Cmdlet PowerShell 
Questa sezione descrive come creare e registrare un gateway con i cmdlet di Azure PowerShell.

1. Avviare **Azure PowerShell** in modalità di amministrazione.
2. Accedere all'account Azure eseguendo il comando seguente e immettendo le credenziali di Azure.

	Login-AzureRmAccount
2. Usare il cmdlet **New-AzureRmDataFactoryGateway** per creare un gateway logico come illustrato di seguito:

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Comando di esempio e output**:


		PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded
		Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI

	
4. In Azure PowerShell passare alla cartella **C:\\Programmi\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** ed eseguire lo script **RegisterGateway.ps1** associato alla variabile locale **$Key**, come mostrato nel comando seguente per registrare l'agente client installato nel computer con il gateway logico creato in precedenza.

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	È possibile registrare il gateway in un computer remoto usando il parametro IsRegisterOnRemoteMachine. Esempio:
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. È possibile usare il cmdlet **Get-AzureRmDataFactoryGateway** per ottenere l'elenco di gateway nell'istanza di Data factory. Quando lo **stato** è **online**, il gateway è pronto per essere usato.

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

È possibile rimuovere un gateway con il cmdlet **Remove-AzureRmDataFactoryGateway** e aggiornare la descrizione per un gateway usando i cmdlet **Set-AzureRmDataFactoryGateway**. Per la sintassi e altri dettagli relativi a questi cmdlet, vedere Riferimento ai cmdlet di Data Factory.

### Elencare i gateway usando PowerShell

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

### Rimuovere il gateway usando PowerShell
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 


## Passaggi successivi
- Leggere l’articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per avere informazioni dettagliate sul gateway.

<!---HONumber=AcomDC_0803_2016-->