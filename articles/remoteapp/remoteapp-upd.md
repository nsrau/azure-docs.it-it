
<properties 
    pageTitle="Azure RemoteApp come salva dati e impostazioni? | Microsoft Azure"
	description="Informazioni su come Azure RemoteApp salva i dati dell'utente utilizzando il disco del profilo utente."
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/13/2016" 
    ms.author="elizapo" />

# Azure RemoteApp come salva dati e impostazioni?

Azure RemoteApp salva identità utente e personalizzazioni nei dispositivi e nelle sessioni. Tali dati utente vengono archiviati in un disco per ogni raccolta per utente, noto come disco del profilo utente (UPD). Il disco segue l'utente e garantisce che l'utente disponga di un'esperienza coerente, indipendentemente da dove si registrano i salvataggi

I dischi dei profili utente sono completamente trasparenti all'utente - gli utenti salvano i documenti nella loro cartella documenti (su quello che sembra essere un'unità locale) e modificano le impostazioni della loro applicazione come di consueto. Allo stesso tempo, tutte le impostazioni personali vengono mantenute quando ci si connette a RemoteApp di Azure da qualsiasi dispositivo. L'utente vede è i propri dati nella stessa posizione.

Ogni UPD dispone di 50GB di archiviazione permanente e contiene entrambe le impostazioni applicazione e dati utente.

Leggere le informazioni specifiche sui dati del profilo utente.

>[AZURE.NOTE] È necessario disabilitare il disco del profilo utente? È ora possibile eseguire questa operazione: per informazioni dettagliate, consultare il post di blog di Pavithra relativo alla [disabilitazione dei dischi dei profili utente in Azure RemoteApp](http://blogs.msdn.com/b/rds/archive/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp.aspx).


## Come può un amministratore accedere ai dati?

Se è necessario accedere ai dati per uno degli utenti (per il ripristino di emergenza o se l'utente lascia la società), contattare [Azure RemoteApp](mailto:remoteappforum@microsoft.com) e fornire le informazioni di sottoscrizione per la raccolta e l'identità dell'utente. Il team di Azure RemoteApp fornirà un URL per il disco rigido virtuale. Scaricare il disco rigido virtuale e recuperare i documenti o i file necessari. Si noti che il disco rigido virtuale è di 50GB, pertanto ci può volere del tempo per scaricarlo.


## Si esegue il backup dei dati?

Sì, si salva una copia di backup dei dati utente per ogni posizione geografica. I dati sono di sola lettura ed è possibile accedervi allo stesso modo dei dati normali (contattare RemoteApp di Azure per ottenerli), se il data center principale è attivo. I dati vengono copiati in tempo reale nel percorso di backup e non si mantengono copie delle diverse versioni. Pertanto, nel danneggiamento dei dati, non sarà possibile ripristinare una versione valida nota in precedenza ma se il data center principale non è attivo, sarà possibile ottenere dati utente da un altro percorso.

## Gli utenti come visualizzano il UPD sul lato server?

Ogni utente dispone di proprie directory nel server associate al proprio UPD: c:\\Users\\username.

## Qual’è il miglior modo di utilizzare Outlook e UPD?

RemoteApp di Azure consente di salvare lo stato di Outlook (cassette postali, file PST) tra le sessioni. A tale scopo, è necessario che PST sia archiviato nei dati di profilo utente (c:\\users<nomeutente>). Questo è il percorso predefinito per i dati, quindi fino a quando non si modifica la posizione, i dati verranno mantenuti tra le sessioni.

È inoltre consigliabile utilizzare la modalità "cache" in Outlook e utilizzare la modalità "server/online" per la ricerca.

Per altre informazioni sull'uso di Outlook e Azure RemoteApp, consultare [questo articolo](remoteapp-outlook.md).

## Possiamo utilizzare soluzioni di dati condivisi?
Sì, RemoteApp di Azure supporta l'utilizzo di soluzioni dati condivisi - in particolare OneDrive for Business e Dropbox. Si noti che OneDrive Consumer (versione personale) e Box non sono supportati.

## Come funziona il reindirizzamento?
È possibile configurare Azure RemoteApp per consentire agli utenti di accedere ai dispositivi locali mediante il [reindirizzamento](remoteapp-redirection.md). I dispositivi locali saranno in grado di accedere ai dati sull’UPD.

## È possibile utilizzare l’UPD come condivisione di rete?
No. UPDs non può essere utilizzato come una condivisione di rete. Un UPD è disponibile per l’utente solo quando l'utente è attivamente connesso a RemoteApp di Azure.

## Se si elimina un utente da una raccolta, viene eliminato anche il suo UPD?

No, quando si elimina un utente, non si elimina automaticamente l’UPD - memorizziamo i dati fino a quando non si elimina la raccolta. 90 giorni dopo l'eliminazione della raccolta, eliminiamo tutti gli UPD.

Se si desidera eliminare un UPD da una raccolta, contattare RemoteApp di Azure - possiamo eliminare l’UPD per conto degli utenti.

## È possibile accedere agli UPD dei propri utenti (utenti correnti o eliminati)?

Sì, se si contatta [Azure RemoteApp](mailto:remoteappforum@microsoft.com), viene fornito un URL per l'accesso ai dati. Per scaricare i dati o file dal UPD prima della scadenza dell'accesso, si avranno a disposizione circa 10 ore.

## Gli UPD sono disponibili offline?

Attualmente non viene fornito l'accesso offline agli UPD, oltre alle 10 ore di accesso descritte in precedenza. Ciò significa che non è attualmente disponibile un modo per fornire un accesso per lunghi tempi per poter completare le attività più complesse, come l’esecuzione di un software antivirus nei UPD o l'accesso ai dati per un controllo.

## Vengono mantenute le impostazioni della chiave del Registro di sistema?
Sì, qualsiasi elemento scritto in HKEY\_Current\_User fa parte del UPD.

## È possibile disattivare gli UPD per una raccolta?

Sì, è possibile chiedere a RemoteApp di Azure di disabilitare gli UPD per una sottoscrizione, ma non è possibile eseguirla manualmente. Ciò significa che gli UPD verranno disabilitati per tutte le raccolte nella sottoscrizione.

È consigliabile disabilitare i dischi del profilo utente in una delle situazioni seguenti:

- È necessario disporre di accesso e controllo completi dei dati utente (a scopo di controllo e verifica, ad esempio negli istituti finanziari).
- Si dispone di soluzioni locali di gestione del profilo utente di terze parti e si desidera continuare a usarle nella distribuzione di Azure RemoteApp aggiunta al dominio. Questa opzione richiede il caricamento dell'agente del profilo nell'immagine gold. 
- Non è necessaria una risorsa di archiviazione di dati locale oppure tutti i dati sono archiviati nel cloud (ad esempio in OneDrive for Business) o in condivisione file e si vuole controllare il salvataggio dei dati in modalità locale tramite Azure RemoteApp.

Per altre informazioni, vedere il post di blog relativo alla [disabilitazione dei dischi dei profili utente in Azure RemoteApp](http://blogs.msdn.com/b/rds/archive/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp.aspx).

## È possibile limitare gli utenti nel salvataggio dei dati nell'unità di sistema?

Sì, ma è necessario impostarlo nell'immagine modello prima di creare la raccolta. Per bloccare l'accesso all'unità del sistema, attenersi alla procedura seguente:

1. Eseguire **gpedit.msc** nell'immagine modello.
2. Passare a **Configurazione utente > Modelli amministrativi > Componenti di Windows > Esplora**.
3. Selezionare le opzioni seguenti:
	- **Nascondi le unità specificate in risorse del Computer**
	- **Impedire l'accesso alle unità dal Computer locale**

## È possibile inizializzare gli UPD? Desidero inserire dei dati nell’UPD disponibile la prima volta che l'utente accede.

Sì, quando si crea l'immagine modello, è possibile aggiungere informazioni al profilo predefinito. Tale informazione viene quindi aggiunta al UPD.

## È possibile modificare le dimensioni dell’UPD a seconda della quantità di dati si desidera archiviare?

No, tutti gli UPD hanno 50 GB di spazio di archiviazione. Se si desidera archiviare diverse quantità di dati, procedere come segue:

1. Disabilitare gli UPD per la raccolta.
2. Configurare una condivisione di file per gli utenti.
3. Caricare la condivisione file utilizzando uno script di avvio. Per ulteriori informazioni sugli script di avvio in Azure RemoteApp, vedere di seguito.
4. Indirizzare gli utenti al salvataggio di tutti i dati nella condivisione file.

Inoltre, è possibile utilizzare App di sincronizzazione dati come OneDrive for Business.

## Come eseguire uno script di avvio in Azure RemoteApp

Se si desidera eseguire uno script di avvio, iniziare creando un'attività pianificata nell'immagine modello che si intende utilizzare per la raccolta. (Eseguire tale operazione*prima*di eseguire sysprep.)

![Creare un'attività di sistema](./media/remoteapp-upd/upd1.png)

![Creare un'attività di sistema che viene eseguita quando un utente accede](./media/remoteapp-upd/upd2.png)

Nella scheda **Generale**, assicurarsi di modificare l’**Account utente** in Sicurezza in "BUILTIN\\Users."

![Modificare l'account utente in un gruppo](./media/remoteapp-upd/upd4.png)

L'attività pianificata avvierà lo script di avvio, utilizzando le credenziali dell'utente. Pianificare l'attività affinché venga eseguita ogni volta che un utente accede.

![Impostare il trigger per l'attività come "al momento dell'accesso"](./media/remoteapp-upd/upd3.png)

È inoltre possibile usare [gli script di avvio basati su criteri di gruppo](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx).

## Come funziona l'inserimento di uno script di avvio nel menu Start? Funziona?

In altre parole, è possibile creare un file con estensione .bat che esegue uno script di configurazione, salvarlo nella cartella c:\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs\\StartUp e eseguire quindi lo script ogni volta che un utente avvia una sessione di RemoteApp?

No, tale opzione non è supportata da RemoteApp di Azure, che utilizza host sessione Desktop remoto che inoltre non supporta gli script di avvio nel menu Start.

## È possibile utilizzare mstsc.exe (il programma Desktop remoto) per configurare gli script di accesso?

No, tale opzione non è supportata da Azure RemoteApp.

## È possibile archiviare dati sulla VM in locale?

NO, i dati archiviati in un punto qualsiasi della VM diverso dall’UPD andranno persi. È molto probabile l'utente non riceva la stessa VM al successivo accesso ad Azure RemoteApp. Non viene mantenuta la persistenza utente-VM, pertanto l'utente non eseguirà l'accesso alla stessa VM e i dati andranno persi. Inoltre, quando si aggiorna la raccolta, le VM esistenti vengono sostituite con un nuovo set di VM, pertanto i dati archiviati sulla stessa VM andranno persi. Si consiglia di archiviare i dati nell'UPD, in un servizio di archiviazione condivisa come File di Azure, in un file server all'interno di una rete virtuale o nel cloud con OneDrive per Business o un altro sistema di archiviazione cloud supportato come DropBox.

## Come montare una condivisione di File di Azure in una VM, tramite PowerShell

È possibile utilizzare il cmdlet di Net-PSDrive per montare l'unità, come indicato di seguito:

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \<storage-account-name>.file.core.windows.net<share-name> -Credential :<storage-account-name>


È inoltre possibile salvare le credenziali eseguendo le operazioni seguenti:

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


In questo modo è possibile ignorare il parametro - Credential nel cmdlet New-PSDrive.

<!---HONumber=AcomDC_0622_2016-->