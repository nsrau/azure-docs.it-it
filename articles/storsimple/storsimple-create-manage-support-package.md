<properties 
   pageTitle="Creare un pacchetto per il supporto StorSimple | Microsoft Azure"
   description="Informazioni su come creare, decrittografare e modificare un pacchetto per il supporto del dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />


# Creare e gestire un pacchetto di supporto StorSimple

## Panoramica

In questa esercitazione vengono descritte le diverse attività associate alla creazione e alla gestione di un pacchetto per il supporto. Un pacchetto per il supporto include tutti i registri pertinenti in formato compresso e crittografato e viene utilizzato per assistere il team del supporto tecnico Microsoft nella risoluzione dei problemi del dispositivo StorSimple.

In questa esercitazione sono incluse le istruzioni dettagliate per creare e gestire il pacchetto per il supporto tramite:

- La sezione **Pacchetto per il supporto** della pagina **Manutenzione** del servizio StorSimple Manager
- Windows PowerShell per StorSimple

Dopo aver letto questa esercitazione, si sarà in grado di:

- Creare un pacchetto di supporto
- Decrittografare e modificare un pacchetto per il supporto


## Creare un pacchetto per il supporto nel portale di Azure classico

Per risolvere eventuali problemi riscontrati con il servizio StorSimple Manager, è possibile creare e caricare un pacchetto per il supporto nel sito del supporto tecnico Microsoft tramite la pagina **Manutenzione** del servizio nel portale di Azure classico. È necessario fornire una passkey per il supporto per consentire il caricamento. La passkey per il supporto deve essere fornita dal supporto tecnico tramite posta elettronica. Viene creato un pacchetto per il supporto non crittografati e compresso (file con estensione cab). Questo pacchetto può quindi essere recuperato dal supporto tecnico nel sito di supporto quando il tecnico fornisce la passkey.

Eseguire i passaggi seguenti nel portale classico per creare un pacchetto per il supporto:

#### Creazione di un pacchetto per il supporto nel portale di Azure classico

1. Andare a **Dispositivi > Manutenzione**.

2. Nella sezione **Pacchetto per il supporto** fare clic su **Crea e carica il pacchetto per il supporto**.

3. Nella finestra di dialogo **Crea e carica il pacchetto per il supporto**, effettuare le operazioni seguenti:

	![Creare un pacchetto per il supporto](./media/storsimple-create-manage-support-package/IC740923.png)
											
	- Fornire la **passkey per il supporto**. Questa chiave deve essere inviata dal supporto tecnico Microsoft tramite posta elettronica.
 	
	- Selezionare la casella combinata per fornire il consenso a **caricare automaticamente il pacchetto per il supporto nel sito del supporto tecnico Microsoft**.
 	
	- Fare clic sull’icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-create-manage-support-package/IC740895.png).


## Creare un pacchetto per il supporto in Windows PowerShell per StorSimple

Se è necessario modificare i file di log prima di creare un pacchetto, è necessario creare il pacchetto tramite Windows PowerShell per StorSimple.

Per creare un pacchetto per il supporto in Windows PowerShell per StorSimple, procedere come segue:


#### Per creare un pacchetto per il supporto in Windows PowerShell per StorSimple

1. Digitare il comando seguente per avviare una sessione di Windows PowerShell come amministratore sul computer remoto utilizzato per connettersi al dispositivo StorSimple:

	`Start PowerShell`

2. Nella sessione di Windows PowerShell, connettersi allo spazio di esecuzione della console SSAdmin del dispositivo:


	- Al prompt dei comandi digitare: 
			
		`$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
		
		
	1. Nella finestra di dialogo visualizzata, digitare la password di amministratore del dispositivo. La password predefinita è:
	 
		`Password1`

		![Sessione di PowerShell per spazio di esecuzione SSAdminConsole](./media/storsimple-create-manage-support-package/IC740962.png)

	2. Fare clic su **OK**.
	1. Al prompt dei comandi digitare: 
		
		`Enter-PSSession $MS`


3. Nella sessione aperta, digitare il comando appropriato.


	- Per le condivisioni di rete protette da password, digitare:

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

		Verrà richiesta la password, il percorso della cartella di rete condivisa e la passphrase di crittografia perché il pacchetto per il supporto è crittografato. Quando vengono specificati questi valori, verrà creato un pacchetto per il supporto nella cartella specificata.
											

	- Per le cartelle condivise di rete aperte (quelle che non sono protette da password), non è necessario il parametro `-Credential`. Digitare quanto segue:

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

		Verrà creato il pacchetto per il supporto per entrambi i controller nella cartella di rete condivisa specificata. È un file compresso e crittografato che può essere inviato al supporto tecnico Microsoft per la risoluzione dei problemi. Per ulteriori informazioni, vedere [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).


### Ulteriori informazioni sul cmdlet Export-HcsSupportPackage
Nella tabella seguente sono riportati i diversi parametri che possono essere utilizzati con il cmdlet Export-HcsSupportPackage.

| Numero No. | Parametro | Obbligatorio/Facoltativo | Descrizione |
|--------|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Path | Obbligatorio | Consente di specificare il percorso della cartella di rete condivisa in cui verrà inserito il pacchetto per il supporto. |
| 2 | EncryptionPassphrase | Obbligatorio | Consente di fornire una passphrase per crittografare il pacchetto per il supporto. |
| 3 | Credenziali | Facoltativo | Utilizzare questo parametro per specificare le credenziali di accesso per la cartella di rete condivisa. |
| 4 | Force | Facoltativo | Consente di ignorare il passaggio di conferma della passphrase di crittografia. |
| 5 | PackageTag | Facoltativo | Consente di specificare una directory del percorso in cui verrà inserito il pacchetto per il supporto. Il valore predefinito è [nome dispositivo]-[data e ora correnti:aaaa-MM-gg-HH-mm-ss]. |
| 6 | Scope | Facoltativo | Specificare come **Cluster** (impostazione predefinita) per creare un pacchetto per il supporto per entrambi i controller. Per creare un pacchetto solo per il controller corrente, specificare **Controller**. |


## Modificare un pacchetto per il supporto

Dopo aver creato un pacchetto per il supporto, potrebbe essere necessario modificare il pacchetto per rimuovere le informazioni specifiche del cliente, ad esempio i nomi dei volumi, gli indirizzi IP dei dispositivi e i nomi di backup dai file di log.

> [AZURE.IMPORTANT]È possibile solo modificare un pacchetto per il supporto che è stato generato tramite Windows PowerShell per StorSimple. Non è possibile modificare un pacchetto creato nel portale di Azure classico con il servizio StorSimple Manager.

Per modificare un pacchetto per il supporto prima di caricarlo nel sito del supporto tecnico Microsoft, è necessario decrittografare il pacchetto per supporto, modificare i file e crittografarlo nuovamente. Per modificare un pacchetto per il supporto, procedere come segue:

#### Per modificare un pacchetto per il supporto in Windows PowerShell per StorSimple

1. Creare un pacchetto per il supporto come descritto in [Creare un pacchetto per il supporto in Windows PowerShell per StorSimple](#create-a-support-package-in-windows-powershell-for-storsimple):

2. [Scaricare lo script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente nel client.

3. Importare il modulo Windows PowerShell. È necessario specificare il percorso della cartella locale in cui è stato scaricato lo script. Per importare il modulo, digitare:
 
	`Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Aprire la cartella del pacchetto per il supporto. Si noti che tutti i file hanno l'estensione *.aes* e sono compressi e crittografati. Aprire i file. Per aprire i file, digitare:

	`Open-HcsSupportPackage <Path to the folder that contains support package files>`

	I file vengono decompressi e decrittografati. Si noterà che per tutti i file sono ora visualizzate le estensioni di file effettive.
	
	![Modificare il pacchetto per il supporto 3](./media/storsimple-create-manage-support-package/IC750706.png)


5. Quando viene richiesta la passphrase di crittografia, digitare la passphrase utilizzata quando è stato creato il pacchetto per il supporto.

    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:EncryptionPassphrase: ****
	
6. Passare alla cartella che contiene i file di log. Dal momento che i file di log sono ora decompressi e decrittografati, avranno le estensioni di file originali. Modificare questi file per rimuovere eventuali informazioni specifiche del cliente, ad esempio i nomi dei volumi e gli indirizzi IP dei dispositivi e salvare i file.

7. Chiudere i file. Alla chiusura, viene eseguita la compressione dei file con Gzip e quindi la crittografia dei file con AES-256. Sono procedure per la sicurezza e la velocità durante il trasferimento del pacchetto per il supporto in rete. Per chiudere i file, digitare:

	`Close-HcsSupportPackage <Path to the folder that contains support package files>`

	![Modificare il pacchetto per il supporto 2](./media/storsimple-create-manage-support-package/IC750707.png)

8. Quando richiesto, specificare una passphrase di crittografia del pacchetto per il supporto modificato.

	    cmdlet Close-HcsSupportPackage at command pipeline position 1
    	Supply values for the following parameters:EncryptionPassphrase: ****

9. Annotare la nuova passphrase in modo che sia possibile condividerla con il supporto tecnico Microsoft quando richiesto.


### Esempio: modifica dei file in un pacchetto per il supporto in una condivisione protetta da password

Di seguito è riportato un esempio che illustra come decrittografare, modificare e crittografare nuovamente un pacchetto per il supporto.

![Modificare il pacchetto per il supporto 1](./media/storsimple-create-manage-support-package/IC750708.png)

    	PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1
    
    	PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Close-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32>

## Passaggi successivi

- Informazioni su come [utilizzare i pacchetti per il supporto e i registri del dispositivo per risolvere i problemi di distribuzione del dispositivo](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Informazioni su come [utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0121_2016-->