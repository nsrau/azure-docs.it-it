<properties 
	pageTitle="Come creare una raccolta ibrida per Azure RemoteApp" 
	description="Informazioni su come creare una distribuzione di RemoteApp si connette alla rete interna." 
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
	ms.date="05/28/2015" 
	ms.author="elizapo"/>

# Come creare una raccolta ibrida per Azure RemoteApp

Sono disponibili due tipi di raccolte RemoteApp:

- Cloud: risiede completamente in Azure e viene creata tramite l'opzione **Creazione rapida** nel portale di gestione di Azure.  
- Ibrida: include una rete virtuale per l'accesso locale e viene creata con l'opzione **Crea con rete virtuale** nel portale di gestione.

Questa esercitazione illustra la creazione di una raccolta ibrida. Sono previsti otto passaggi:

1.	Decidere quale [immagine](remoteapp-imageoptions.md) utilizzare per la raccolta. È possibile creare un'immagine personalizzata oppure usare una delle immagini Microsoft incluse nella sottoscrizione.
2. Configurare la rete virtuale.
2.	Creare una raccolta RemoteApp.
2.	Collegare la raccolta alla rete virtuale.
3.	Aggiungere un'immagine modello alla raccolta.
4.	Configurare la sincronizzazione della directory. RemoteApp richiede l'integrazione con Azure Active Directory 1) configurando Azure Active Directory Sync con l'opzione Sincronizzazione password o 2) configurando Azure Active Directory Sync senza l'opzione Sincronizzazione password ma usando un dominio federato ad ADFS. Verificare le [informazioni di configurazione per Active Directory con RemoteApp](remoteapp-ad.md).
5.	Pubblicare app di RemoteApp.
6.	Configurare l'accesso utente.

**Prima di iniziare**

Prima di creare la raccolta, è necessario eseguire le operazioni seguenti:

- [Iscriversi](http://azure.microsoft.com/services/remoteapp/) a RemoteApp. 
- Creare in Active Directory un account utente da usare come account del servizio RemoteApp. Limitare le autorizzazioni per l'account in modo che possa aggiungere computer al dominio.
- Raccogliere informazioni sulla rete locale: informazioni sull'indirizzo IP e dettagli sul dispositivo VPN.
- Installare il modulo [Azure PowerShell](../install-configure-powershell.md).
- Raccogliere informazioni sugli utenti a cui concedere l'accesso. È necessario il nome dell'entità utente Azure Active Directory (ad esempio, name@contoso.com) per ogni utente.
- Scegliere un'immagine modello. Un'immagine modello di RemoteApp contiene le app e i programmi da pubblicare per gli utenti. Per altre informazioni, vedere [Opzioni immagine di RemoteApp](remoteapp-imageoptions.md). 
- [Configurare Active Directory per RemoteApp](remoteapp-ad.md).



## Passaggio 1: configurare la rete virtuale
È possibile distribuire una raccolta ibrida di RemoteApp che usa una rete virtuale di Azure esistente oppure è possibile creare una nuova rete virtuale. Una rete virtuale consente agli utenti di accedere ai dati nella rete locale mediante le risorse remote di RemoteApp. L'uso di una rete virtuale di Azure offre alla raccolta accesso di rete diretto ad altri servizi e macchine virtuali di Azure distribuiti in tale rete virtuale.

### Creare una rete virtuale di Azure e aggiungerla alla distribuzione di Active Directory

Iniziare creando un [rete virtuale](https://msdn.microsoft.com/library/azure/dn631643.aspx) nella scheda **Rete** nel portale di gestione di Azure. È necessario connettere la rete virtuale alla distribuzione di Active Directory sincronizzata con il tenant di Azure Active Directory.

Per altre informazioni, vedere [Informazioni sulle impostazioni della rete virtuale nel portale di gestione](https://msdn.microsoft.com/library/azure/jj156074.aspx).

### Assicurarsi che la rete virtuale sia pronta per RemoteApp
Prima di creare la raccolta di RemoteApp, verificare che la nuova rete virtuale sia pronta. A questo scopo, eseguire le seguenti operazioni:

1. Creare una macchina virtuale di Azure all'interno della subnet della rete virtuale appena creata per RemoteApp.
2. Connettersi alla macchina virtuale tramite Desktop remoto (fare clic su **Connetti**).
3. Collegarla alla stessa distribuzione di Active Directory che si desidera usare per RemoteApp.

Ha funzionato? La rete e la subnet virtuali sono pronte per RemoteApp.

Altre informazioni sulla creazione di macchine virtuali di Azure e su come collegarle a Desktop remoto sono disponibili [qui](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## Passaggio 2: Creare una raccolta RemoteApp ##



1. Nel [portale di gestione di Azure](http://manage.windowsazure.com) passare alla pagina RemoteApp.
2. Fare clic su **Nuovo > Crea con rete virtuale**.
3. Immettere un nome per la raccolta.
4. Scegliere il piano che da usare: standard o di base.
5. Fare clic su **Crea raccolta RemoteApp**.

Dopo la creazione della raccolta RemoteApp, passare alla pagina **Avvio rapido** di RemoteApp per continuare con la procedura di configurazione.

## Passaggio 3: Collegare la raccolta alla rete virtuale ##

 
1. Nella pagina **Avvio rapido** fare clic su **Collega una rete virtuale**.
2. Selezionare la rete virtuale che si desidera usare dall'elenco a discesa.
3. Selezionare l'area geografica da usare e assicurarsi che nel campo venga visualizzata la sottoscrizione corretta. 
5. Fare clic su **Aggiungi dominio locale** nella pagina **Avvio rapido**. Aggiungere l'account del servizio RemoteApp al dominio di Active Directory locale. È necessario disporre di nome di dominio, unità organizzativa, nome utente e password dell'account del servizio. 

	Si tratta delle informazioni raccolte se è stata seguita la procedura descritta in [Configurare Active Directory per Azure RemoteApp](remoteapp-ad.md).


## Passaggio 4: Creare un collegamento a un'immagine di RemoteApp ##

Un'immagine modello di RemoteApp contiene i programmi da condividere con gli utenti. È possibile creare una nuova [immagine modello](remoteapp-imageoptions.md) o creare un collegamento a un'immagine esistente (già importata o caricata in Azure RemoteApp). È anche possibile creare un collegamento a [immagini modello](remoteapp-images.md) di RemoteApp che contengono applicazioni Office 365 o Office 2013 (per l'uso in valutazione).

Se si carica la nuova immagine, è necessario immettere il nome e scegliere il percorso dell'immagine. Nella pagina successiva della procedura guidata verrà visualizzato un insieme di cmdlet di PowerShell. Copiarli ed eseguirli da un prompt di Windows PowerShell con privilegi elevati per caricare l'immagine specificata.

Se si crea un collegamento a un'immagine modello esistente, è sufficiente specificare il nome, il percorso e la sottoscrizione di Azure associata all'immagine.



## Passaggio 5: Configurare la sincronizzazione della directory di Active Directory ##

RemoteApp richiede l'integrazione con Azure Active Directory 1) configurando Azure Active Directory Sync con l'opzione Sincronizzazione password o 2) configurando Azure Active Directory Sync senza l'opzione Sincronizzazione password ma usando un dominio federato ad ADFS. Per informazioni sulla pianificazione e le procedure dettagliate, vedere [Roadmap sulla sincronizzazione della directory](http://msdn.microsoft.com//library/azure/hh967642.aspx).

## Passaggio 6: Pubblicare app di RemoteApp ##

Una app di RemoteApp è l'app o il programma da fornire ai propri utenti. Si trova nell'immagine modello caricata per la raccolta. Quando un utente accede a un'app, questa si comporta come se venisse eseguita nel suo ambiente locale, mentre in realtà viene eseguita in Azure.

Prima che gli utenti possano accedere alle app di RemoteApp, è necessario pubblicarle nel feed degli utenti finali, ovvero un elenco di app disponibili a cui gli utenti accedono tramite il client di Desktop remoto.
 
È possibile pubblicare più app nella raccolta RemoteApp. Dalla pagina di pubblicazione di RemoteApp fare clic su **Pubblica** per aggiungere un'app. È possibile eseguire la pubblicazione dal menu Start dell'immagine modello oppure specificando il percorso nell'immagine modello dell'app. Se si sceglie di aggiungere il programma dal menu Start, scegliere il programma da aggiungere. Se si sceglie di fornire il percorso all'app, specificare il nome dell'app e il percorso in cui è installata nell'immagine modello.

## Passaggio 7: Configurare l'accesso utente ##

A questo punto, dopo aver creato la raccolta RemoteApp, è necessario aggiungere gli utenti che potranno usare le risorse remote. Gli utenti o i gruppi a cui viene fornito l'accesso devono esistere nel tenant di Active Directory associato alla sottoscrizione usata per creare la raccolta RemoteApp.

1.	Nella pagina Avvio rapido fare clic su **Configura accesso utente**. 
2.	Immettere l'account di lavoro (da Active Directory) o l'account Microsoft al quale concedere l'accesso.

	**Note:**

	Assicurarsi di usare il formato “utente@dominio.com”.

	Se si usa Office 365 ProPlus alla raccolta, è necessario usare le identità di Active Directory per gli utenti. Ciò consente di convalidare la licenza.


3.	Dopo la convalida degli utenti, fare clic su **Salva**.


## Passaggi successivi ##
La procedura è stata completata e la raccolta ibrida RemoteApp è stata creata e distribuita. Il passaggio successivo consiste nel chiedere agli utenti di scaricare e installare il client Desktop remoto. L'URL del client è indicato nella pagina Avvio rapido di RemoteApp. Chiedere quindi agli utenti di connettersi al client e di accedere alle app pubblicate.


 

<!---HONumber=August15_HO6-->