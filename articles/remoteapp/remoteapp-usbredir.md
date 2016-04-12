<properties 
    pageTitle="Come reindirizzare i dispositivi USB in Azure RemoteApp | Microsoft Azure" 
    description="Informazioni su come usare il reindirizzamento per i dispositivi USB in Azure RemoteApp." 
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
    ms.date="03/28/2016" 
    ms.author="elizapo" />



# Come reindirizzare i dispositivi USB in Azure RemoteApp

Il reindirizzamento dei dispositivi consente agli utenti di usare i dispositivi USB collegati al computer o al tablet con le app in Azure RemoteApp. Ad esempio, se si condivide Skype tramite Azure RemoteApp, gli utenti devono poter usare le videocamere dei loro dispositivi.

Prima di proseguire, assicurarsi di leggere le informazioni sul reindirizzamento USB in [Uso del reindirizzamento in Azure RemoteApp](remoteapp-redirection.md). Tuttavia il comando nusbdevicestoredirect:s:* consigliato non funziona con le webcam USB e potrebbe non funzionare per alcune stampanti USB o dispositivi multifunzionali USB. Per impostazione predefinita e per motivi di sicurezza, per consentire agli utenti di usare tali dispositivi, l'amministratore di Azure RemoteApp dovrà abilitare il reindirizzamento in base al GUID della classe dispositivo o in base all'ID istanza del dispositivo.

Anche se questo articolo descrive il reindirizzamento della webcam, è possibile usare un approccio simile per reindirizzare le stampanti USB e altri dispositivi multifunzionali USB che non vengono reindirizzati dal comando **nusbdevicestoredirect:s:***.

## Opzioni di reindirizzamento per i dispositivi USB
Azure Remote App usa meccanismi molto simili a quelli disponibili per Servizi Desktop remoto per il reindirizzamento dei dispositivi USB. La tecnologia sottostante consente di scegliere il metodo di reindirizzamento corretto per un determinato dispositivo, per ottenere il reindirizzamento ottimale sia ad alto livello che con RemoteFX del dispositivo USB tramite il comando **usbdevicestoredirect:s:**. Questo comando include quattro elementi:

| Ordine di elaborazione | Parametro | Descrizione |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1 | * | Seleziona tutti i dispositivi che non vengono selezionati dal reindirizzamento ad alto livello. Nota: per impostazione predefinita, * non funziona per le webcam USB. |
| | {Device class GUID} | Seleziona tutti i dispositivi che corrispondono alla classe di installazione del dispositivo specificato. |
| | USB\\InstanceID | Seleziona un dispositivo USB specificato per l'ID istanza specificato. |
| 2 | -USB\\Instance ID | Rimuove le impostazioni di reindirizzamento per il dispositivo specificato. |

## Reindirizzamento di un dispositivo USB tramite il GUID della classe dispositivo
Ci sono due modi per trovare il GUID che può essere usato per il reindirizzamento della classe dispositivo.

La prima opzione consiste nell'usare le [classi di installazione dei dispositivi definite dal sistema disponibili per i fornitori](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Selezionare la classe più corrispondente al dispositivo collegato al computer locale. Per le videocamere digitali può essere una classe dispositivo di acquisizione immagini o una classe dispositivo di acquisizione video. Sarà necessario provare alcune classi dispositivo per trovare il GUID della classe corretta che funzioni con il dispositivo USB collegato localmente, in questo caso la webcam.

Un modo migliore o la seconda opzione, consiste nel seguire questi passaggi per trovare il GUID della classe dispositivo specifico:

1. Aprire Gestione dispositivi, individuare il dispositivo che verrà reindirizzato e fare clic con il pulsante destro del mouse, quindi aprire la finestra delle proprietà. ![Aprire Gestione dispositivi](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Nella scheda **Dettagli** scegliere la proprietà **GUID classe**. Il valore visualizzato è il GUID della classe per quel tipo di dispositivo. ![Proprietà della videocamera](./media/remoteapp-usbredir/ra-classguid.png)
3. Usare il valore GUID classe per reindirizzare i dispositivi corrispondenti.

Ad esempio:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

È possibile combinare più operazioni di reindirizzamento del dispositivo nello stesso cmdlet. Ad esempio, per reindirizzare l'archiviazione locale e una webcam USB, il cmdlet è simile al seguente:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Quando si imposta il reindirizzamento del dispositivo tramite il GUID classe, vengono reindirizzati tutti i dispositivi che corrispondono a tale GUID classe nella raccolta specificata. Ad esempio, se nella rete locale sono presenti più computer con le stesse webcam USB, è possibile eseguire un singolo cmdlet per reindirizzare tutte le webcam.

## Reindirizzamento di un dispositivo USB tramite il GUID dell'istanza dispositivo

Se si preferisce un controllo più accurato e si vuole controllare il reindirizzamento per dispositivo, è possibile usare il parametro di reindirizzamento **USB\\InstanceID**.

La parte più difficile di questo metodo è trovare l'ID istanza del dispositivo USB. È necessario l'accesso al computer e al dispositivo USB specifico. Seguire quindi questa procedura:

1. Abilitare il reindirizzamento del dispositivo nella sessione Desktop remoto, come descritto in [Come utilizzare i dispositivi e le risorse in una sessione di Desktop remoto](http://windows.microsoft.com/it-IT/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Aprire una connessione Desktop remoto e fare clic su **Mostra opzioni**.
3. Fare clic su **Salva con nome** per salvare le impostazioni di connessione correnti in un file RDP. ![Salvare le impostazioni come file con estensione RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Scegliere un nome file e un percorso, ad esempio "MyConnection.rdp" e "PC\\Documenti" e salvare il file.
5. Aprire il file MyConnection.rdp con un editor di testo e trovare l'ID istanza del dispositivo da reindirizzare.

A questo punto, è possibile usare l'ID istanza nel cmdlet seguente:

	Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB<Device InstanceID value>"



### Come contribuire al miglioramento 
Non tutti sanno che oltre alla classificazione di questo articolo e all'aggiunta di commenti di seguito, è possibile apportare modifiche all'articolo stesso. Mancano informazioni? Alcune informazioni non sono corrette? Qualcosa non è abbastanza chiaro? Scorrere verso l'alto e fare clic su **Modifica in GitHub** per apportare modifiche. Dopo che saranno state esaminate e approvate, le modifiche e i miglioramenti suggeriti dagli utenti saranno applicati all'articolo.

<!---HONumber=AcomDC_0330_2016-->