<properties
   pageTitle="Note sulla versione del Catalogo dati di Azure"
   description="Note sulla versione di anteprima pubblica del 28 agosto 2015 del Catalogo dati di Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/04/2015"
   ms.author="maroche"/>

# Note sulla versione del Catalogo dati di Azure

## Note per la versione del 4 settembre 2015 del Catalogo dati di Azure

### Il profilo dei dati può essere mancante per alcune risorse di dati registrate

Quando si registrano le origini dei dati con l'opzione di profiling dei dati selezionata nello strumento di registrazione delle origine dati, le informazioni sul profilo dei dati non possono essere incluse durante la registrazione delle tabelle Oracle e delle visualizzazioni con caratteri multibyte in nomi di colonna.

Queste restrizioni sono dovute a un problema noto nella versione del 4 settembre e verranno risolte in un aggiornamento futuro del Catalogo dati di Azure.

## Note per la versione del 13 luglio 2015 del Catalogo dati di Azure

### Registrazione e connessione al database Oracle

Per la connessione alle origini dati del Database Oracle, gli utenti devono installare i driver Oracle corretti che corrispondono al numero di bit (32 bit o 64 bit) del software in uso.

-	Per la registrazione delle origini dati di Oracle in un computer che esegue Windows a 32 bit, verranno usati i driver di Oracle a 32 bit
-	Per la registrazione delle origini dati di Oracle in un computer che esegue Windows a 64 bit, verranno usati i driver di Oracle a 64 bit
-	Per la connessione alle origini dati di Oracle tramite Excel in un computer che esegue la versione a 32 bit di Microsoft Office, incluso in Windows a 64 bit, verranno usati i driver di Oracle a 32 bit
-	Per la connessione alle origini dati di Oracle tramite Excel in un computer che esegue la versione a 64 bit di Microsoft Office, verranno usati i driver di Oracle a 64 bit

### Registrazione e la connessione a SQL Server Reporting Services

Il supporto per le origini dati di SQL Server Reporting Services (SSRS) nella versione di anteprima iniziale del Catalogo dati di Azure è limitato al server in modalità nativa. Il supporto per SSRS in modalità SharePoint verrà aggiunto in una versione successiva.

### Apertura degli asset di dati in Excel

Quando si aprono asset di dati in Microsoft Excel dal portale del Catalogo dati di Azure, potrebbe essere visualizzata per gli utenti una finestra di dialogo **Informazioni sulla sicurezza per Microsoft Excel**. È un comportamento standard e previsto e gli utenti possono selezionare **Abilita** per continuare.

Per altre informazioni, vedere [Attivazione o disattivazione degli avvisi di protezione relativi ai collegamenti a siti Web sospetti e a file scaricati da tali siti](https://support.office.com/it-IT/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### Colonne di BLOB e UTD mancanti nelle anteprime

Durante la registrazione di tabelle e viste che contengono colonne di oggetti binari di grandi dimensioni (BLOB) e di tipo definito dall'utente (UDT), se si sceglie di includere l'anteprima per gli asset di dati, queste colonne non verranno incluse nell'anteprima.

### Configurazione di proxy e criteri e registrazione dell'origine dati

È possibile che si verifichi una situazione in cui gli utenti possono accedere al portale del Catalogo dati di Azure, ma, quando tentano di accedere allo strumento di registrazione dell'origine dati, viene restituito un messaggio di errore che impedisce l'accesso.

Le possibili cause di questo comportamento problematico sono due:

**Causa 1: la configurazione di Active Directory Federation Services** Lo strumento di registrazione dell'origine dati usa l'autenticazione basata su form per convalidare gli accessi degli utenti in Active Directory. Per accedere, l'autenticazione basata su form deve essere abilitata nei criteri di autenticazione globali da un amministratore di Active Directory.

In alcune situazioni, questo errore si verifica solo quando l'utente è nella rete aziendale o si connette dall'esterno della rete aziendale. I criteri di autenticazione globali consentono di abilitare i metodi di autenticazione separatamente per le connessioni Extranet e Intranet. Se l'autenticazione basata su form non è abilitata per la rete da cui l'utente si connette, è possibile che si verifichino errori di accesso.

Per altre informazioni, vedere [Configurare i criteri di autenticazione](https://technet.microsoft.com/it-IT/library/dn486781.aspx).

**Causa 2: la configurazione del proxy di rete** Se la rete aziendale usa un server proxy, lo strumento di registrazione potrebbe non essere in grado di connettersi ad Azure Active Directory tramite il proxy. Gli utenti possono abilitare lo strumento di registrazione modificando il file di configurazione dello strumento e aggiungendo questa sezione al file:


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


Per individuare il file RegistrationTool.exe.config, avviare lo strumento di registrazione e quindi aprire l'utilità Gestione attività Windows. Nella scheda Dettagli di Gestione attività, fare clic con il pulsante destro del mouse su RegistrationTool.exe e scegliere Apri percorso file dal menu a comparsa.

<!---HONumber=Sept15_HO3-->