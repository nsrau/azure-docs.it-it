<properties
   pageTitle="Crittografare una macchina virtuale di Azure | Microsoft Azure"
   description="Questo documento illustra come crittografare una macchina virtuale di Azure dopo la ricezione di un avviso dal Centro sicurezza di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/03/2016"
   ms.author="tomsh"/>

# Crittografare una macchina virtuale di Azure
Centro sicurezza di Azure invia avvisi in caso di macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Raccomandazione di crittografare il disco](./media/security-center-disk-encryption\security-center-disk-encryption-fig1.png)

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

Per crittografare le macchine virtuali di Azure identificate da Centro sicurezza di Azure, seguire questa procedura:

- Installare e configurare Azure PowerShell. In questo modo sarà possibile eseguire i comandi di PowerShell per configurare i prerequisiti necessari per crittografare le macchine virtuali di Azure.
- Ottenere ed eseguire lo script di Azure PowerShell dei prerequisiti di Crittografia dischi di Azure.
- Crittografare le macchine virtuali.

Questo documento fornisce informazioni utili per eseguire la crittografia di macchine virtuali, anche se si conosce poco o nulla di Azure PowerShell. Questo documento presuppone l'uso di Windows 10 nel computer client da cui si esegue la configurazione di Crittografia dischi di Azure.
 
L'installazione dei prerequisiti e la configurazione della crittografia per le macchine virtuali di Azure possono essere eseguite in diversi modi. Se si ha buona familiarità con Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile adottare soluzioni alternative.

> [AZURE.NOTE] Per altre informazioni sulle soluzioni alternative per la configurazione della crittografia per macchine virtuali di Azure, vedere l'articolo relativo a [Crittografia dischi di Azure per macchine virtuali di Azure che eseguono Windows o Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

## Installare e configurare Azure PowerShell
È necessario che Azure PowerShell 1.2.1 o versione successiva sia installato nel computer. L'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) contiene tutti i passaggi necessari per effettuare il provisioning del computer per l'uso di Azure PowerShell. L'approccio più semplice consiste nell'eseguire l'installazione da WebPI menzionata nell'articolo. Anche se Azure PowerShell è già presente nel computer, installarlo nuovamente da WebPI per avere l'ultima versione di Azure PowerShell.


## Ottenere ed eseguire lo script di configurazione dei prerequisiti di Crittografia dischi di Azure.
Lo script di configurazione dei prerequisiti di Crittografia dischi di Azure configura tutti i prerequisiti necessari per la crittografia delle macchine virtuali di Azure.

1.	Lo [script di configurazione dei prerequisiti di Crittografia dischi di Azure](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1) è disponibile in GitHub.
2.	Nella pagina di GibHub, fare clic sul pulsante **Raw**.
3.	Premere **CTRL+A** per selezionare tutto il testo della pagina e quindi **CTRL+C** per copiarlo negli Appunti.
4.	Aprire il **Blocco note** e incollare il testo copiato. 
5.	Creare una nuova cartella nell'unità C: denominata **AzureADEScript**.
6.	Salvare il file del Blocco note facendo clic su **File** e quindi su **Salva con nome**. Nella casella di testo Nome file immettere **"ADEPrereqScript.ps1"** e fare clic su **Salva**. Assicurarsi di inserire le virgolette che racchiudono il nome, in caso contrario il file verrà salvato con estensione txt. 

Ora che il contenuto dello script è stato salvato, aprire lo script in PowerShell ISE:

1.	Nel menu Start, fare clic su **Cortana**. Digitare **PowerShell** nella casella di testo di ricerca di **Cortana** per richiedere "PowerShell". 
2.	Fare clic con il pulsante destro del mouse su **Windows PowerShell ISE** e scegliere **Esegui come amministratore**.
3.	Nella finestra **Amministratore: Windows PowerShell ISE** fare clic su **Visualizza** e quindi su **Mostra riquadro di script**. 
4.	Se viene visualizzato il riquadro **Comandi** sul lato destro della finestra, fare clic sulla **"x"** nell'angolo superiore destro del riquadro per chiuderlo. Se la visualizzazione del testo è troppo piccola, premere **CTRL+Add**, dove Add indica il segno "+". Se la visualizzazione del testo è troppo grande, premere **CTRL+Subtract**, dove Subtract indica il segno "-". 
5.	Fare clic su **File** e quindi su **Apri**. Passare alla cartella **C:\\AzureADEScript** e fare doppio clic su **ADEPrereqScript**.
6.	Il contenuto di **ADEPrereqScript** dovrebbe essere visualizzato in PowerShell ISE. Per permettere di trovarli più facilmente, i vari componenti come ad esempio variabili, parametri e comandi sono contraddistinti dal colore. 

Verrà visualizzata una schermata simile alla figura seguente.

![Finestra di PowerShell ISE](./media/security-center-disk-encryption\security-center-disk-encryption-fig2.png)

Il riquadro superiore è detto "riquadro di script", mentre il riquadro inferiore è detto "console". Questi termini verranno usati più avanti nell'articolo.

## Eseguire il comando di PowerShell relativo ai prerequisiti di Crittografia dischi di Azure.

Dopo l'avvio, lo script dei prerequisiti di Crittografia dischi di Azure richiede le informazioni seguenti:

- **resourceGroupName**: nome del gruppo di risorse in cui si vuole inserire l'insieme di credenziali delle chiavi. Viene creato un nuovo gruppo di risorse con il nome immesso, se non esiste già. Se nella sottoscrizione è già disponibile un gruppo di risorse da usare, immetterne il nome.
- **keyVaultName**: nome dell'insieme di credenziali delle chiavi in cui inserire le chiavi di crittografia. Viene creato un nuovo insieme di credenziali delle chiavi con il nome immesso, se non esiste già. Se è già disponibile un insieme di credenziali delle chiavi da usare, immetterne il nome.
- **location**: percorso dell'insieme di credenziali delle chiavi. Assicurarsi che l'insieme di credenziali delle chiavi e le macchine virtuali da crittografare siano nello stesso percorso. Se non si conosce il percorso, più avanti in questo articolo vengono illustrati alcuni passaggi per determinarlo. 
- **aadAppName**: nome dell'applicazione Azure Active Directory che viene usata per la scrittura di segreti nell'insieme di credenziali delle chiavi. Viene creata una nuova applicazione con questo nome, se non esiste già. Se è già disponibile un'applicazione Azure Active Directory da usare, immetterne il nome.

> [AZURE.NOTE] Per informazioni sulla necessità di creare un'applicazione Azure Active Directory, vedere la sezione *Registrare un'applicazione con Azure Active Directory* nell'articolo [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md).

Per crittografare una macchina virtuale di Azure, seguire questa procedura:

1.	Se PowerShell ISE è stato chiuso, aprire un'istanza di PowerShell ISE con privilegi elevati. Seguire le istruzioni riportate in precedenza in questo articolo se PowerShell ISE non è già aperto. Se lo script è stato chiuso, aprire **ADEPrereqScript.ps1** facendo clic su **File**, **Apri** e quindi selezionare lo script nella cartella **C:\\AzureADEScript**. Se le istruzioni contenute in questo articolo sono state seguite dall'inizio, è sufficiente andare al passaggio successivo. 
2.	Nella console di PowerShell ISE nel riquadro inferiore della finestra di PowerShell ISE spostare lo stato attivo sulla posizione dello script digitando **cd c:\\AzureADEScript** e quindi premere **INVIO**.
3.	Impostare i criteri di esecuzione del computer in modo che sia possibile eseguire lo script. Digitare **Set-ExecutionPolicy Unrestricted** nella console e quindi premere INVIO. Se viene visualizzata una finestra di dialogo che informa sugli effetti della modifica dei criteri di esecuzione, fare clic su **Sì, tutti** oppure su **Sì**. Se l'opzione **Sì, tutti** viene visualizzata, selezionarla. Se l'opzione **Sì, tutti** non viene visualizzata, fare clic su **Sì**. 
4.	Accedere all'account Azure. Nella console digitare **Login-AzureRmAccount** e premere **INVIO**. Viene visualizzata una finestra di dialogo in cui inserire le credenziali. Assicurarsi di avere i diritti necessari per modificare le macchine virtuali, in caso contrario non sarà possibile crittografarle. Se non si è certi, chiedere al proprietario della sottoscrizione o all'amministratore. Vengono visualizzate le informazioni relative ad **ambiente**, **account**, **TenantId**, **SubscriptionId** e **CurrentStorageAccount**. Copiare il **SubscriptionId** nel Blocco note. Sarà necessario nel passaggio 6.
5.	Trovare la sottoscrizione a cui appartiene la macchina virtuale e il relativo percorso. Passare a [https://portal.azure.com](ttps://portal.azure.com) ed eseguire l'accesso. Sul lato sinistro della pagina, fare clic su **Macchine virtuali**. Verrà visualizzato un elenco delle macchine virtuali e delle sottoscrizioni a cui appartengono.

	![Macchine virtuali](./media/security-center-disk-encryption\security-center-disk-encryption-fig3.png)

6.	Tornare alla finestra di PowerShell ISE. Impostare il contesto della sottoscrizione in cui verrà eseguito lo script. Nella console digitare **Select-AzureRmSubscription –SubscriptionId <your_subscription_Id>**, sostituendo **< your_subscription_Id >** con l'ID sottoscrizione effettivo, e premere **INVIO**. Vengono visualizzate le informazioni relative ad ambiente, **account**, **TenantId**, **SubscriptionId** e **CurrentStorageAccount**.
7.	A questo punto è possibile eseguire lo script. Fare clic sul pulsante **Esegui script** o preme **F5** sulla tastiera.

	![Esecuzione dello script di PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig4.png)

8.	Lo script richiede **resourceGroupName:** immettere il nome del *gruppo di risorse* da usare e quindi premere **INVIO**. Se non è disponibile, immettere un nome da usare per un nuovo gruppo di risorse. Se è già disponibile un *gruppo di risorse* da usare, ad esempio quello in cui si trova la macchina virtuale, immetterne il nome.
9.	Lo script richiede **keyVaultName:** immettere il nome dell'*insieme di credenziali delle chiavi* da usare e quindi premere INVIO. Se non è disponibile, immettere un nome da usare per un nuovo insieme di credenziali delle chiavi. Se è già disponibile un *insieme di credenziali delle chiavi* da usare, immetterne il nome.
10.	Lo script richiede **location:** immettere il percorso in cui si trova la macchina virtuale da crittografare, quindi premere **INVIO**. Se non si ricorda il percorso, tornare al passaggio 5.
11.	Lo script richiede **aadAppName:** immettere il nome dell'*applicazione Azure Active Directory* da usare, quindi premere **INVIO**. Se non è disponibile, immettere un nome da usare per una nuova applicazione. Se è già disponibile un'*applicazione Azure Active Directory* da usare, immetterne il nome.
12.	Viene visualizzata una finestra di dialogo di accesso. Fornire le credenziali. È necessario accedere nuovamente, anche se è già stato eseguito l'accesso.
13.	Lo script viene eseguito e al termine verrà richiesto di copiare i valori di **aadClientID**, **aadClientSecret**, **diskEncryptionKeyVaultUrl** e **keyVaultResourceId**. Copiare i singoli valori negli Appunti e incollarli nel Blocco note. 
14.	Tornare alla finestra di PowerShell ISE, posizionare il cursore alla fine dell'ultima riga e premere **INVIO**.

L'output dello script sarà simile alla schermata seguente:

![Output di PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig5.png)

## Crittografare la macchina virtuale di Azure 

A questo punto è possibile crittografare la macchina virtuale. Se la macchina virtuale si trova nello stesso gruppo di risorse dell'insieme di credenziali delle chiavi, è possibile passare alla sezione Passaggi di crittografia. Se la macchina virtuale non si trova nello stesso gruppo di risorse dell'insieme di credenziali delle chiavi, è necessario immettere quanto segue nella console di PowerShell ISE:

**$resourceGroupName = <'Virtual\_Machine\_RG'>**

Sostituire **< Virtual_Machine_RG >** con il nome del gruppo di risorse in cui sono contenute le macchine virtuali, racchiuso tra virgolette singole, e quindi premere **INVIO**. Per verificare che sia stato immesso il nome del gruppo di risorse corretto, digitare quanto segue nella console di PowerShell ISE:

**$resourceGroupName**

Premere **INVIO**. Viene visualizzato il nome del gruppo di risorse in cui si trovano le macchine virtuali. Ad esempio:

![Output di PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig6.png)

### Passaggi di crittografia

In primo luogo, è necessario specificare in PowerShell il nome della macchina virtuale da crittografare. Nella console digitare quanto segue:

**$vmName = <'your\_vm\_name'>**

Sostituire **<'your\_vm\_name'>** con il nome della macchina virtuale, racchiuso tra virgolette singole, e quindi premere **INVIO**.

Per verificare che sia stato immesso il nome della macchina virtuale corretto, digitare:

**$vmName**

Premere **INVIO**. Viene visualizzato il nome della macchina virtuale da crittografare. Ad esempio:

![Output di PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig7.png)

Il comando per la crittografia della macchina virtuale può essere eseguito in due modi. Il primo metodo consiste nel digitare il comando seguente nella console di PowerShell ISE:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId
~~~

Dopo aver digitato il comando, premere **INVIO**.

Il secondo metodo consiste nel fare clic nel riquadro di script, vale a dire il riquadro superiore di PowerShell ISE, e scorrere fino alla fine dello script. Evidenziare il comando riportato sopra, fare clic su di esso con il pulsante destro del mouse e selezionare **Esegui selezione** oppure premere **F8** sulla tastiera.

![PowerShell ISE](./media/security-center-disk-encryption\security-center-disk-encryption-fig8.png)

Indipendentemente dal metodo usato, verrà visualizzata una finestra di dialogo che informa che il completamento dell'operazione richiede 10-15 minuti. Fare clic su **Sì**.

Durante l'esecuzione del processo di crittografia, è possibile tornare al portale di Azure e visualizzare lo stato della macchina virtuale. Sul lato sinistro della pagina fare clic su **Macchine virtuali**. Nel pannello **Macchine virtuali** fare clic sul nome della macchina virtuale di cui è in corso la crittografia. Nel pannello visualizzato lo **Stato** è indicato come **Aggiornamento**. Ciò dimostra che la crittografia è in corso.

![Altri dettagli sulla macchina virtuale](./media/security-center-disk-encryption\security-center-disk-encryption-fig9.png)

Tornare alla finestra di PowerShell ISE. Al termine dell'esecuzione dello script, viene visualizzato quanto riportato nella figura seguente.

![Output di PowerShell](./media/security-center-disk-encryption\security-center-disk-encryption-fig10.png)

Per verificare che la macchina virtuale ora è crittografata, tornare al portale di Azure e fare clic su **Macchine virtuali** sul lato sinistro della pagina. Fare clic sul nome della macchina virtuale appena crittografata. Nel pannello **Impostazioni** fare clic su **Dischi**.

![Opzioni delle impostazioni](./media/security-center-disk-encryption\security-center-disk-encryption-fig11.png)

Nel pannello **Dischi** si noterà che la **crittografia** è **abilitata**.

![Proprietà dei dischi](./media/security-center-disk-encryption\security-center-disk-encryption-fig12.png)


## Passaggi successivi

In questo documento si è appreso come crittografare una macchina virtuale di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'utilizzo del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure

<!---HONumber=AcomDC_0309_2016-->