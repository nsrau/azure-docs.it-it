<properties
	pageTitle="Come creare un'immagine modello personalizzata per Azure RemoteApp | Microsoft Azure"
	description="Informazioni su come creare un'immagine modello personalizzata per Azure RemoteApp. È possibile usare questo modello con una raccolta ibrida o cloud."
	services="remoteapp"
	documentationCenter=""
	authors="lizap"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="remoteapp"
	ms.workload="compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2015" 
	ms.author="elizapo"/>

# Come creare un'immagine modello personalizzata per Azure RemoteApp
Azure RemoteApp usa un'immagine modello di Windows Server 2012 R2 per ospitare tutti i programmi da condividere con gli utenti. Per creare un'immagine modello di RemoteApp personalizzata, è possibile iniziare con un'immagine esistente o crearne una nuova.


> [AZURE.TIP]E’ possibile creare un'immagine da una macchina virtuale di Azure. In questo modo viene ridotto il tempo necessario per importare l'immagine. Consultare i passaggi [qui](remoteapp-image-on-azurevm.md)

I requisiti per l'immagine che possono essere caricati e usati con l'app Azure RemoteApp sono i seguenti:


- La dimensione dell'immagine dev'essere un multiplo di MB. Se si prova a caricare un'immagine che non è un multiplo esatto, l'operazione non andrà a buon fine.
- L'immagine deve avere una dimensione massima di 127 GB.
- Deve essere inclusa in un file VHD (i file VHDX [dischi rigidi virtuali Hyper-V] non sono attualmente supportati).
- Il file VHD non deve essere una macchina virtuale di seconda generazione.
- Il file VHD può essere di dimensioni fisse o a espansione dinamica. È consigliabile un file VHD a espansione dinamica perché i tempi di caricamento di questo file in Azure sono inferiori rispetto a uno di dimensioni disse.
- Il disco deve essere inizializzato con lo stile di partizione MBR (Master Boot Record). Lo stile di partizione basato sulla tabella di partizione GUID (GPT) non è supportato.
- Il file VHD deve contenere una singola installazione di Windows Server 2012 R2. Può contenere più volumi, ma solo uno che contiene un'installazione di Windows.
- È necessario installare il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop.
- Il ruolo Gestore connessione Desktop remoto *non* deve essere installato.
- EFS (Encrypting File System) deve essere disabilitato.
- L'immagine deve essere preparata con SYSPREP usando i parametri **/oobe /generalize /shutdown** (NON usare il parametro **/mode:vm**).
- Il caricamento del disco VHD da una catena di snapshot non è supportato.


**Prima di iniziare**

Prima di creare il servizio, è necessario eseguire le seguenti operazioni:

- [Iscriversi](http://azure.microsoft.com/services/remoteapp/) a RemoteApp.
- Creare in Active Directory un account utente da usare come account del servizio RemoteApp. Limitare le autorizzazioni per l'account in modo che possa aggiungere computer al dominio. Per altre informazioni, vedere [Configurare Azure Active Directory per RemoteApp](remoteapp-ad.md).
- Raccogliere informazioni sulla rete locale: informazioni sull'indirizzo IP e dettagli sul dispositivo VPN.
- Installare il modulo [Azure PowerShell](../install-configure-powershell.md).
- Raccogliere informazioni sugli utenti a cui concedere l'accesso. Le informazioni possono essere relative all'account Microsoft o all'account di lavoro Active Directory per gli utenti.



## Creazione di un'immagine modello ##

Questi sono i passaggi di alto livello per creare una nuova immagine modello da zero:

1.	Procurarsi un DVD o un'immagine ISO di Windows Server 2012 R2 Update.
2.	Creare un file VHD.
4.	Installare Windows Server 2012 R2.
5.	Installare il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop.
6.	Installare funzionalità aggiuntive richieste dalle applicazioni.
7.	Installare e configurare le applicazioni. Per semplificare la condivisione delle applicazioni, aggiungere eventuali applicazioni o programmi che si desidera condividere nel menu **Start** dell'immagine, in particolare in **%systemdrive%\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programmi.
8.	Eseguire eventuali configurazioni aggiuntive di Windows richieste dalle applicazioni.
9.	Disabilitare EFS (Encrypting File System).
10.	**OBBLIGATORIO:** accedere a Windows Update e installare tutti gli aggiornamenti importanti.
9.	Preparare l'immagine con SYSPREP.

I passaggi dettagliati per creare una nuova immagine sono:

1.	Procurarsi un DVD o un'immagine ISO di Windows Server 2012 R2 Update.
2.	Creare un disco VHD usando Gestione disco.
	1.	Avviare Gestione disco (diskmgmt.msc).
	2.	Creare un file VHD a espansione dinamica di dimensioni non inferiori a 40 GB. Stimare la quantità di spazio necessaria per Windows, le applicazioni e le personalizzazioni. Per Windows Server con il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop installata sono necessari circa 10 GB di spazio.
		1.	Fare clic su **Azione > Crea file VHD**.
		2.	Specificare il percorso, le dimensioni e il formato del file VHD. Selezionare **A espansione dinamica**, quindi fare clic su **OK**.

			L'operazione dura alcuni secondi. Una volta completata la creazione del file VHD, dovrebbe essere visibile un nuovo disco senza lettera di unità e nello stato “Non inizializzato" nella console di Gestione disco.

		- Fare clic con il pulsante destro del mouse sul disco (non lo spazio non allocato), quindi scegliere **Inizializza disco**. Selezionare **MBR** (Master Boot Record) come stile di partizione e fare clic su **OK**.
		- Creare un nuovo volume: fare clic con il pulsante destro del mouse sullo spazio non allocato, quindi fare clic su **Nuovo volume semplice**. È possibile accettare i valori predefiniti nella procedura guidata, ma assicurarsi di assegnare una lettera di unità per evitare possibili problemi durante il caricamento dell'immagine modello.
		- Fare clic con il pulsante destro del mouse e scegliere **Scollega file VHD**.





1. Installare Windows Server 2012 R2:
	1. Creare una nuova macchina virtuale. Usare la Creazione guidata macchina virtuale in Console di gestione di Hyper-V o Hyper-V client.
		1. Nella pagina Specifica generazione, selezionare **Generazione 1**.
		2. Nella pagina Connessione disco rigido virtuale selezionare **Usa disco rigido virtuale esistente** e passare al file VHD creato nel passaggio precedente.
		2. Nella pagina Opzioni di installazione selezionare **Installa un sistema operativo da un CD/DVD-ROM di avvio**, quindi selezionare il percorso del supporto di installazione di Windows Server 2012 R2.
		3. Selezionare altre opzioni della procedura guidata necessarie per installare Windows e le applicazioni. Terminare la procedura guidata.
	2.  Una volta terminata la procedura guidata, modificare le impostazioni della macchina virtuale e apportare eventuali altre modifiche necessarie per installare Windows e i programmi, ad esempio il numero di processori virtuali, quindi fare clic su **OK**.
	4.  Connettersi alla macchina virtuale e installare Windows Server 2012 R2.
1. Installare il ruolo Host sessione Desktop remoto e la funzionalità Esperienza desktop:
	1. Avviare Server Manager.
	2. Fare clic su **Aggiungi ruoli e funzionalità** nella schermata iniziale oppure nel menu **Gestisci**.
	3. Nella pagina Prima di iniziare fare clic su **Avanti**.
	4. Selezionare **Installazione basata su ruoli o basata su funzionalità** e fare clic su **Avanti**.
	5. Selezionare il computer locale nell'elenco e fare clic su **Avanti**.
	6. Selezionare **Servizi Desktop remoto** e fare clic su **Avanti**.
	7. Espandere **Interfacce utente e infrastruttura** e selezionare **Esperienza desktop**.
	8. Fare clic su **Aggiungi funzionalità** e quindi su **Avanti**.
	9. Nella pagina Servizi Desktop remoto fare clic su **Avanti**.
	10. Fare clic su **Host sessione Desktop remoto**.
	11. Fare clic su **Aggiungi funzionalità** e quindi su **Avanti**.
	12. Nella pagina Conferma selezioni per l'installazione selezionare **Riavvia automaticamente il server di destinazione se necessario**, quindi fare clic su **Sì** nell'avviso di riavvio.
	13. Fare clic su **Installa**. Il computer verrà riavviato.
1.	Installare funzionalità aggiuntive richieste dalle applicazioni, ad esempio .NET Framework 3.5. Per installare le funzionalità, eseguire l'Aggiunta guidata ruoli e funzionalità.
7.	Installare e configurare i programmi e le applicazioni da pubblicare tramite RemoteApp.

>[AZURE.IMPORTANT]
>
>Installare il ruolo Host sessione Desktop remoto prima di installare le applicazioni in modo da garantire l'individuazione di problemi di compatibilità prima del caricamento dell'immagine in RemoteApp.
>
>Assicurarsi che un collegamento all'applicazione (file **lnk** ) venga visualizzato nel menu **Start** per tutti gli utenti (%systemdrive%\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programmi). Assicurarsi inoltre che l'icona visualizzata nel menu **Start** sia quella che si vuole far visualizzare agli utenti. In caso contrario, modificarla (non è *obbligatorio* aggiungere l'applicazione al menu Start, ma questo semplificherà notevolmente la pubblicazione dell'applicazione in RemoteApp. In caso contrario, sarà necessario fornire il percorso di installazione dell'applicazione al momento della pubblicazione.


8.	Eseguire eventuali configurazioni aggiuntive di Windows richieste dalle applicazioni.
9.	Disabilitare EFS (Encrypting File System). Eseguire il comando seguente a un prompt dei comandi con privilegi elevati:

		Fsutil behavior set disableencryption 1

	In alternativa, è possibile impostare o aggiungere il seguente valore DWORD nel Registro di sistema:

		HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.	Se si intende creare l'immagine all'interno di una macchina virtuale di Azure, è necessario rinominare il file **\\%windir%\\Panther\\Unattend.xml**, poiché impedisce l'esecuzione dello script di caricamento usato in seguito. Cambiare il nome del file in Unattend.old, al fine di disporre del file in caso di ripristinare la distribuzione a uno fase precedente.
10.	Andare a Windows Update e installare tutti gli aggiornamenti importanti. Potrebbe essere necessario eseguire più volte Windows Update per ottenere tutti gli aggiornamenti (a volte un aggiornamento installato richiede a sua volta un aggiornamento).
10.	Preparare l'immagine con SYSPREP. Eseguire il comando seguente a un prompt dei comandi con privilegi elevati:

	**C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

	**Nota:** non usare l'opzione **/mode:vm** del comando SYSPREP anche se si tratta di una macchina virtuale.


## Passaggi successivi ##
Ora che si dispone di un'immagine modello personalizzata, è necessario caricarla nelle propria raccolta di RemoteApp. Per creare la propria raccolta, consultare i seguenti articoli:


- [Come creare una raccolta ibrida di RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Come creare una raccolta RemoteApp nel cloud](remoteapp-create-cloud-deployment.md)
 

<!---HONumber=Oct15_HO3-->