<properties 
   pageTitle="PowerShell per la gestione dei dispositivi StorSimple | Microsoft Azure"
   description="Informazioni su come utilizzare Windows PowerShell per StorSimple per gestire il dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="alkohli@microsoft.com" />

# Utilizzo di Windows PowerShell per StorSimple per amministrare il dispositivo

## Panoramica

Windows PowerShell per StorSimple fornisce un'interfaccia della riga di comando che può essere utilizzata per gestire il dispositivo Microsoft Azure StorSimple. Come suggerisce il nome, è un'interfaccia della riga di comando basata su Windows PowerShell realizzata in uno spazio di esecuzione vincolato. Dal punto di vista dell'utente nella riga di comando, uno spazio di esecuzione vincolato viene considerato come una versione limitata di Windows PowerShell. Pur mantenendo alcune funzionalità di base di Windows PowerShell, questa interfaccia dispone di ulteriori cmdlet dedicati che sono pensati per la gestione del dispositivo Microsoft Azure StorSimple.

In questo articolo vengono descritte le funzionalità di Windows PowerShell per StorSimple, tra cui come è possibile connettersi a questa interfaccia e sono contenuti i collegamenti alle procedure dettagliate o flussi di lavoro che possono essere eseguiti utilizzando questa interfaccia. I flussi di lavoro includono come registrare il dispositivo, configurare l'interfaccia di rete sul dispositivo, installare gli aggiornamenti che richiedono che il dispositivo sia in modalità di manutenzione, modificare lo stato del dispositivo e risolvere eventuali problemi che possono verificarsi.

Dopo aver letto l'articolo, l'utente sarà in grado di:

- Connettersi al dispositivo StorSimple utilizzando Windows PowerShell per StorSimple

- Amministrazione del dispositivo StorSimple utilizzando Windows PowerShell per StorSimple

- Ottenere la guida in Windows PowerShell per StorSimple

>[AZURE.NOTE] 	

>- I cmdlet di Windows PowerShell per StorSimple consentono di gestire il dispositivo StorSimple da una console seriale o in remoto tramite la comunicazione remota di Windows PowerShell. Per ulteriori informazioni su ciascuno dei singoli cmdlet che può essere utilizzato in questa interfaccia andare alla [documentazione di riferimento dei cmdlet di Windows PowerShell per StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

>- I cmdlet StorSimple di Azure PowerShell sono una raccolta di diversi cmdlet che consentono di automatizzare le attività StorSimple a livello di servizio e di migrazione dalla riga di comando. Per ulteriori informazioni sui cmdlet di Azure PowerShell per StorSimple, andare alla [documentazione di riferimento dei cmdlet di Azure StorSimple](https://msdn.microsoft.com/library/azure/dn920427.aspx).

È possibile accedere a Windows PowerShell per StorSimple utilizzando uno dei metodi seguenti:

- [Connessione alla console seriale del dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Connessione in remoto a StorSimple tramite Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
	

## Connessione a Windows PowerShell per StorSimple tramite la console seriale del dispositivo

È possibile [scaricare PuTTY](http://www.putty.org/) o software di emulazione di terminale simili per connettersi a Windows PowerShell per StorSimple. È necessario configurare PuTTY in modo specifico per accedere al dispositivo Microsoft Azure StorSimple. Nei seguenti argomenti vengono descritti i passaggi dettagliati su come configurare PuTTy e connettersi al dispositivo. Vengono anche illustrate varie opzioni di menu sulla console seriale.

### Impostazioni puTTY

Assicurarsi di utilizzare le seguenti impostazioni PuTTY per connettersi all'interfaccia di Windows PowerShell dalla console seriale.

#### Per configurare PuTTY:

1. Nella finestra di dialogo **Reconfiguration** di PuTTY, selezionare **Keyboard** nel riquadro **Category**.

2. Assicurarsi che siano selezionate le opzioni seguenti (queste sono le impostazioni predefinite quando si avvia una nuova sessione).

 	|Elemento tastiera|Selezionare|
 	|---|---|
 	|Tasto backspace|Ctrl-? (127)|
	|Tasti home o fine|Standard|
	|Tasti e tastierino funzione|ESC[n~|
	|Stato iniziale dei tasti cursore|Normal|
	|Stato iniziale del tastierino numerico|Normal|
	|Abilitare le funzionalità di tastiera aggiuntive|Ctrl-Alt è diverso da AltGr|

	![Impostazioni PuTTY supportate](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Fare clic su **Apply**.

4. Nel riquadro **Categoria**, selezionare**Traduzione**.

5. Nella casella di riepilogo **Set di caratteri remoti**, selezionare **UTF-8**.

6. Sotto **Gestione dei caratteri lineette**, selezionare **Utilizza punti di codice lineette Unicode**. Nella figura seguente vengono illustrate le selezioni di PuTTY corrette.

	![Impostazioni PuTTY UTF](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Fare clic su **Apply**.


È ora possibile usare PuTTY per la connessione alla console seriale del dispositivo attendendosi ai passaggi seguenti.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### Uscire dalla console seriale.

Quando si accede all'interfaccia Windows PowerShell del dispositivo StorSimple tramite la console seriale, viene visualizzato un messaggio di intestazione, seguito dalle opzioni di menu.

Il messaggio di intestazione contiene informazioni di base sul dispositivo StorSimple come modello, nome, versione software installata e stato del controller a cui si accede. Nella figura seguente viene illustrato un esempio di messaggio di intestazione.

![Messaggio di intestazione seriale](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] È possibile utilizzare il messaggio di intestazione per identificare se il controller a cui si è connessi è attivo o passivo.

Nell'immagine seguente vengono illustrate le varie opzioni di spazio di esecuzione disponibili nel menu della console seriale.

![Registrare il dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

È possibile scegliere tra le seguenti impostazioni:

1. **Accedi con accesso completo** Questa opzione consente di connettersi (con le credenziali appropriate) allo spazio di esecuzione **SSAdminConsole** sul controller locale. (Il controller locale è il controller a cui si accede attualmente tramite la console seriale del dispositivo StorSimple.) Questa opzione può essere utilizzata anche per consentire al supporto Microsoft di accedere allo spazio di esecuzione senza limitazioni (una sessione di supporto) per risolvere eventuali problemi relativi al dispositivo. Dopo aver utilizzato l'opzione 1 per l'accesso, è possibile consentire al tecnico del supporto Microsoft di accedere allo stazio di esecuzione senza limitazioni eseguendo un cmdlet specifico. Per informazioni dettagliate, consultare [Avvio di una sessione di supporto](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Accedere al controller peer con accesso completo**questa opzione equivale all'opzione 1, è la differenza che è possibile connettersi (con le credenziali appropriate) il**SSAdminConsole**spazio di esecuzione del controller peer. Poiché il dispositivo StorSimple è un dispositivo a elevata disponibilità con due controller in una configurazione attiva-passiva, peer fa riferimento all'altro controller del dispositivo a cui si accede tramite la console seriale). Simile all'opzione 1, questa opzione può inoltre essere utilizzata per consentire al supporto Microsoft di accedere allo spazio di esecuzione senza restrizioni su un controller peer.

3. **Connetti con accesso limitato** Questa opzione viene utilizzata per accedere all'interfaccia Windows PowerShell in modalità limitata. Non vengono richieste le credenziali di accesso. Questa opzione consente la connessione a uno spazio di esecuzione più limitato rispetto alle opzioni 1 e 2. Alcune delle attività disponibili tramite l'opzione 1 che non possono essere eseguite in questo spazio di esecuzione sono:

	- Ripristino alle impostazioni di fabbrica
	- Modifica della password
	- Abilitazione o disabilitazione dell'accesso del supporto
	- Applicazione degli aggiornamenti
	- Installazione degli aggiornamenti rapidi 
												

	>[AZURE.NOTE] **Questa è l'opzione consigliata se si dimentica la password di amministratore del dispositivo e non è possibile connettersi tramite l'opzione 1 o 2.**

4. **Modifica lingua** Questa opzione consente di modificare la lingua di visualizzazione nell'interfaccia Windows PowerShell. Le lingue supportate sono inglese, giapponese, russo, francese, sudcoreano, spagnolo, italiano, tedesco, cinese e portoghese (Brasile).


## Connessione remota a StorSimple tramite Windows PowerShell per StorSimple

È possibile utilizzare la comunicazione remota di Windows PowerShell per la connessione al dispositivo StorSimple. Quando ci si connette in questo modo, non verrà visualizzato un menu. Viene visualizzato un menu solo se si usa la console seriale del dispositivo per la connessione. La connessione remota consente di passare direttamente all'equivalente dell'opzione 1 (accesso completo) nella console seriale. Con la comunicazione remota di Windows PowerShell, ci si connette a uno specifico spazio di esecuzione. È inoltre possibile specificare la lingua di visualizzazione.

La lingua di visualizzazione è indipendente dalla lingua impostata tramite l'opzione **Modifica lingua** nel menu della console seriale. La sessione remota di PowerShell recupererà automaticamente le impostazioni locali del dispositivo da cui ci si connette se non è specificati.

>[AZURE.NOTE] Se si lavora con gli host virtuali di Microsoft Azure e i dispositivi virtuali StorSimple, è possibile utilizzare la comunicazione remota di Windows PowerShell e l'host virtuale per connettersi al dispositivo virtuale. Se è stata configurata una posizione di condivisione nell'host in cui salvare le informazioni dalla sessione di Windows PowerShell, tenere presente che l'entità di sicurezza Tutti include solo gli utenti autenticati. Per questo motivo, se è stata configurata la condivisione per consentire l'accesso da parte di Tutti e ci si connette senza specificare le credenziali, viene usata l'entità di sicurezza Anonimo e viene visualizzato un errore. Per risolvere questo problema, nell'host di condivisione è necessario abilitare l'account Guest e fornire all'account Guest l'accesso completo alla condivisione oppure è necessario specificare credenziali valide e il cmdlet di Windows PowerShell.

È possibile utilizzare HTTP o HTTPS per connettersi tramite la comunicazione remota di Windows PowerShell. Utilizzare le istruzioni nelle esercitazioni seguenti:

- [Connessione tramite HTTP](storsimple-remote-connect.md#connect-through-http)
- [Connessione tramite HTTPS](storsimple-remote-connect.md#connect-through-https)

## Considerazioni sulla sicurezza di connessione

Quando si decide come connettersi a Windows PowerShell per StorSimple, considerare quanto segue:

- La connessione diretta alla console seriale del dispositivo è protetta ma la connessione alla console seriale sugli switch di rete non lo è. Prestare attenzione al rischio di sicurezza quando ci si connette alla console seriale del dispositivo sugli switch di rete.

- La connessione tramite una sessione HTTP potrebbe offrire maggiore sicurezza rispetto alla connessione tramite la console seriale sulla rete. Sebbene non sia il metodo più sicuro, è accettabile su reti attendibili.

- La connessione tramite una sessione HTTPS è l'opzione consigliata e la più sicura.


## Amministrazione del dispositivo StorSimple tramite Windows PowerShell per StorSimple
La tabella seguente illustra un riepilogo di tutte le attività di gestione comuni e dei flussi di lavoro complessi che possono essere eseguiti nell'interfaccia Windows PowerShell del dispositivo StorSimple. Per altre informazioni su ogni flusso di lavoro, fare clic sulla voce appropriata nella tabella.

#### Flussi di lavoro di Windows PowerShell per StorSimple

|Se si desidera eseguire questa operazione...|Usare questa procedura.|
|---|---|
|Registrazione del dispositivo|[Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Configurare il proxy web </br> Visualizzare le impostazioni del proxy web|[Configurare il proxy web per il dispositivo StorSimple](storsimple-configure-web-proxy.md)|
|Modifica delle impostazioni dell'interfaccia di rete DATA 0 sul dispositivo|[Modificare le impostazioni dell'interfaccia di rete DATA 0 per il dispositivo StorSimple](storsimple-modify-data-0.md)|
|Arrestare un controller </br> Riavviare o arrestare un controller </br> Arrestare un dispositivo </br> Ripristinare il dispositivo alle impostazioni predefinite di fabbrica|[Gestire i controller dei dispositivi](storsimple-manage-device-controller.md)|
|Installazione degli aggiornamenti in modalità di manutenzione e rapidi|[Aggiornare il dispositivo](storsimple-update-device.md)|
|Inserire la modalità di manutenzione </br> Uscire dalla modalità di manutenzione|[Modalità del dispositivo StorSimple](storsimple-device-modes.md)|
|Creare un pacchetto di supporto</br>Decrittografare e modificare un pacchetto di supporto|[Creare e gestire un pacchetto di supporto](storsimple-create-manage-support-package.md)|
|Avviare una sessione di supporto </br>|[Avviare una sessione di supporto in Windows PowerShell per StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## Ottenimento della guida in Windows PowerShell per StorSimple

In Windows PowerShell per StorSimple è disponibile la guida per i cmdlet. È inoltre disponibile una versione aggiornata in linea di questa guida, che è possibile utilizzare per aggiornare la guida del sistema.

La guida in questa interfaccia si ottiene in modo simile a quella in Windows PowerShell e la maggior parte dei cmdlet correlati alla guida funzionerà. È possibile trovare la guida in linea per Windows PowerShell in linea nella libreria TechNet: [Utilizzo degli script con Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Di seguito viene riportata una breve descrizione dei tipi di guida per l'interfaccia di Windows PowerShell, tra cui come aggiornare la guida.

#### Per ottenere la guida per un cmdlet:

- Per ottenere la guida per un cmdlet o una funzione, utilizzare il comando seguente: `Get-Help <cmdlet-name>`

- Per ottenere la guida in linea per tutti i cmdlet, utilizzare il cmdlet precedente con il parametro `-Online`: `Get-Help <cmdlet-name> -Online`

- Per la Guida completa, è possibile usare il parametro `–Full` e, per gli esempi, il parametro `–Examples`.

#### Per aggiornare la guida:

È possibile aggiornare facilmente la guida dell'interfaccia di Windows PowerShell. Attenersi ai passaggi seguenti per aggiornare la guida del sistema.

#### Per aggiornare la guida dei cmdlet:

1. Avviare Windows PowerShell con l'opzione **Esegui come amministratore**.

1. Al prompt dei comandi digitare: `Update-Help`

1. Verranno installati i file della guida aggiornati.

1. Una volta installati i file della guida, digitare: `Get-Help Get-Command`. Verrà visualizzato un elenco dei cmdlet per cui è disponibile la guida.


>[AZURE.NOTE] Per ottenere un elenco di tutti i cmdlet disponibili in ciascuno degli spazi di esecuzione, accedere all'opzione di menu corrispondente ed eseguire il cmdlet `Get-Command`.

## Passaggi successivi
Se si riscontrano problemi relativi al dispositivo StorSimple durante l'esecuzione di uno dei flussi di lavoro riportati in precedenza, fare riferimento a [Strumenti per la risoluzione dei problemi di distribuzioni di StorSimple](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

<!---HONumber=AcomDC_0615_2016-->