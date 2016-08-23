<properties
   pageTitle="Eseguire qualsiasi app di Windows su qualsiasi dispositivo con Azure RemoteApp | Microsoft Azure"
   description="Informazioni su come condividere con gli utenti qualsiasi applicazione Windows tramite Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# Eseguire qualsiasi app su qualsiasi dispositivo con Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l'[annuncio](https://go.microsoft.com/fwlink/?linkid=821148).

È possibile eseguire un'applicazione Windows ovunque e veramente su qualsiasi dispositivo - semplicemente utilizzando Azure RemoteApp. Che si tratti di un'applicazione personalizzata scritta 10 anni fa o di un'app di Office, gli utenti non saranno più vincolati a uno specifico sistema operativo (come Windows XP) per quelle poche applicazioni.

Con Azure RemoteApp, gli utenti possono usare dispositivi Android o Apple e usufruire della stessa esperienza che ottengono in Windows (o con i dispositivi Windows Phone). A questo scopo, occorre ospitare l'applicazione Windows in una raccolta di macchine virtuali Windows in Azure, dove gli utenti possono ottenere l'accesso ovunque sia disponibile una connessione Internet.

Continuare a leggere per un esempio di come eseguire esattamente questa operazione.

In questo articolo, si condividerà Access con tutti gli utenti. Tuttavia, è possibile usare QUALSIASI app. Poiché è possibile installare l'app in un computer Windows Server 2012 R2, è possibile condividerla usando la procedura seguente. È possibile esaminare i [requisiti dell'app](remoteapp-appreqs.md), per assicurarsi che l'app funzionerà.

Si noti che poiché Access è un database e come tale è necessario che sia utile, si eseguiranno alcuni passaggi aggiuntivi per consentire agli utenti di accedere alla condivisione dei dati di Access. Se l'app non è un database o non è necessario che gli utenti siano in grado di accedere a una condivisione di file, è possibile ignorare tali passaggi nell'esercitazione.

> [AZURE.NOTE] <a name="note"></a>Per completare l'esercitazione, è necessario un account Azure:
> - È possibile [aprire un account Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e, anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio Siti Web. La carta di credito non verrà mai addebitata, a meno l'utente non modifichi le impostazioni e che richieda esplicitamente di essere addebitato.
> - È possibile [attivare i benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.


## Creare una raccolta in RemoteApp

Iniziare con la creazione di una raccolta, che servirà come contenitore per le app e gli utenti. Ogni raccolta si basa su un'immagine. È possibile creare un'immagine personalizzata o usarne una fornita con la sottoscrizione. Per questa esercitazione si userà l'immagine di valutazione di Office 2013 che contiene l'app da condividere.

1. Nel Portale di Azure, scorrere verso il basso il pannello di navigazione sinistro fino a visualizzare RemoteApp. Aprire la pagina.
2. Fare clic su **Crea una raccolta RemoteApp**.
3. Fare clic su **Creazione rapida** e immettere un nome per la raccolta.
4. Selezionare l'area da usare per creare la raccolta. Per un'esperienza ottimale, selezionare l'area geograficamente più vicina alla località in cui gli utenti accederanno all'app. Ad esempio, in questa esercitazione gli utenti risiederanno a Redmond, Washington. L'area di Azure più vicina è **Stati Uniti occidentali**.
5. Selezionare il piano di fatturazione da usare. Il piano di fatturazione di base prevede 16 utenti in una macchina virtuale di Azure di grandi dimensioni, mentre il piano di fatturazione standard include 10 in una macchina virtuale di Azure di grandi dimensioni. Come esempio generale, il piano di base è un'ottima soluzione per un flusso di lavoro di tipo immissione dati. Per un'app di produttività come Office è consigliabile il piano standard.
6. Infine selezionare l'immagine di Office 2013 Professional. Questa immagine contiene le app di Office 2013. Promemoria - questa immagine è valida solo per le raccolte della versione di valutazione e le POCs. Non è possibile utilizzare questa immagine in una raccolta di produzione.
7. Fare clic su **Crea raccolta RemoteApp**.

![Creare una raccolta nel cloud in RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Viene avviata la creazione della raccolta che potrebbe richiedere fino a un'ora.

A questo punto è possibile aggiungere gli utenti.

## Condividere l'app con gli utenti

Dopo la creazione della raccolta, pubblicare Access per gli utenti e aggiungere gli utenti che devono potervi accedere.

Se durante la creazione della raccolta ci si è spostati dal nodo di Azure RemoteApp, ecco come ritornarvi dalla home page di Azure.

2. Fare clic sulla raccolta creata in precedenza per accedere ad altre opzioni e configurare la raccolta.
![Nuova raccolta nel cloud di RemoteApp](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. Nella scheda **Pubblicazione** fare clic su **Pubblica** nella parte inferiore della schermata e quindi fare clic su **Pubblica programmi del menu Start**.
![Pubblicare un programma di RemoteApp](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Selezionare dall'elenco le app da pubblicare. Per questa esercitazione si è scelto Access. Fare clic su **Completa**. Attendere che venga completata la pubblicazione delle app.
![Pubblicazione di Access in RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Al termine della pubblicazione dell'app, passare alla scheda **Accesso utente** per aggiungere tutti gli utenti che richiedono l'accesso alle app. Immettere i nomi utente (indirizzo di posta elettronica) degli utenti e quindi fare clic su **Salva**.

![Aggiungere utenti a RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. A questo punto, è necessario informare gli utenti che sono disponibili nuove app e indicare come accedervi. Per eseguire questa operazione, inviare agli utenti un messaggio di posta elettronica che punta all'URL di download del client Desktop remoto.
![URL di download del client per RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## Configurare l'accesso ad Access

Alcune app richiedono una configurazione aggiuntiva dopo la distribuzione tramite RemoteApp. In particolare, per Access si creerà una condivisione file in Azure a cui qualsiasi utente può accedere. Se non si vuole eseguire questa operazione, è possibile creare una [raccolta ibrida](remoteapp-create-hybrid-deployment.md) [invece della raccolta nel cloud], che consente agli utenti di accedere ai file e alle informazioni nella rete locale. Sarà quindi necessario comunicare agli utenti di connettere un'unità locale del computer al file system di Azure.

La prima parte viene eseguita come amministratore, quindi alcuni passaggi dovranno essere eseguiti dagli utenti.

1. Iniziare con la pubblicazione dell'interfaccia della riga di comando (cmd.exe). Nella scheda **Pubblicazione** selezionare **cmd** e quindi fare clic su **Pubblica > Pubblica i programmi usando il percorso**.
2. Immettere il nome dell'app e il percorso. Per questa esercitazione, usare "Esplora file" come nome e "%SYSTEMDRIVE%\\windows\\explorer.exe" come percorso.
![Pubblicare il file cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. È necessario creare un [account di archiviazione](../storage/storage-create-storage-account.md) di Azure. In questo caso è stato denominato "accessstorage", perciò selezionare un nome significativo. (Per citare in modo scorretto Highlander, può esistere un solo "accessstorage.")
![Account di archiviazione di Azure](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Tornare al dashboard per ottenere il percorso dell'account di archiviazione (percorso dell'endpoint), che verrà usato tra poco, quindi assicurarsi di copiarlo.
![Percorso dell'account di archiviazione](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Dopo avere creato l'account di archiviazione, è necessaria la chiave di accesso primaria. Fare clic su **Gestisci chiavi di accesso** e quindi copiare la chiave di accesso primaria.
6. Impostare ora il contesto dell'account di archiviazione e creare una nuova condivisione file per Access. Eseguire i cmdlet seguenti in una finestra di Windows PowerShell con privilegi elevati:

        $ctx=New-AzureStorageContext <account name> <account key>
    	$s = New-AzureStorageShare <share name> -Context $ctx

	Ecco i cmdlet da eseguire per questa condivisione:

	    $ctx=New-AzureStorageContext accessstorage <key>
    	$s = New-AzureStorageShare <share name> -Context $ctx


Di seguito sono elencati i passaggi che dovrà eseguire l'utente. Innanzitutto chiedere agli utenti di installare un [client RemoteApp](remoteapp-clients.md). Dovranno quindi connettere un'unità dall'account alla condivisione file di Azure creata dall'amministratore e aggiungere i file di Access. Ecco come eseguire questa operazione:

1. Nel client RemoteApp accedere alle app pubblicate. Avviare il programma cmd.exe.
2. Eseguire il comando seguente per eseguire il mapping di un'unità dal computer alla condivisione file:

		net use z: \<accountname>.file.core.windows.net<share name> /u:<user name> <account key>

	Se si imposta il parametro **/persistent** su sì, l'unità mappata verrà mantenuta tra le sessioni.
1. Avviare l'app Esplora file da RemoteApp. Copiare i file di Access che si desidera usare nell'app condivisa nella condivisione file.
![Inserimento dei file di Access in una condivisione di Azure](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Infine aprire Access e quindi il database appena condiviso. I dati saranno visualizzati in Access in esecuzione nel cloud.
![Database Access reale in esecuzione dal cloud](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

A questo punto è possibile usare Access su qualsiasi dispositivo, basta avere installato un client RemoteApp.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Dopo avere appreso come si crea una raccolta, provare a creare una [raccolta che usa Office 365](remoteapp-tutorial-o365anywhere.md). In alternativa è possibile creare una [raccolta ibrida](remoteapp-create-hybrid-deployment.md) in grado di accedere alla rete locale.

<!--Image references-->
 

<!---HONumber=AcomDC_0817_2016-->