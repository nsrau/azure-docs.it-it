<properties 
	pageTitle="Come creare una raccolta cloud di Azure RemoteApp | Microsoft Azure" 
	description="Informazioni su come creare una distribuzione di Azure RemoteApp che salva i dati nel cloud Azure." 
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
	ms.date="08/15/2016" 
	ms.author="elizapo"/>

# Come creare una raccolta cloud di Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l'[annuncio](https://go.microsoft.com/fwlink/?linkid=821148).

Sono disponibili due tipi di [raccolte di Azure RemoteApp](remoteapp-collections.md):

- Cloud: risiede completamente in Azure. È possibile scegliere di salvare tutti i dati nel cloud (raccolta solo cloud) o connettere la raccolta a una rete virtuale e salvare i dati in questa posizione.
- Ibrida: include una rete virtuale per l'accesso in locale, è necessario l'uso di Azure Active Directory e un ambiente Active Directory locale.

Questa esercitazione illustra il processo di creazione di una raccolta nel cloud. i quattro passaggi della creazione di una distribuzione cloud.

1.	Creare una raccolta di Azure RemoteApp.
2.	Facoltativamente, configurare la sincronizzazione della directory. Se si usano Azure AD e Active Directory, è necessario sincronizzare utenti, contatti e password dall'ambiente Active Directory locale con il tenant di Azure AD.
5.	Pubblicare le app.
6.	Configurare l'accesso utente.


**Prima di iniziare**

Prima di creare la raccolta, è necessario eseguire le operazioni seguenti:

- [Iscrizione](https://azure.microsoft.com/services/remoteapp/) ad Azure RemoteApp.
- Raccogliere informazioni sugli utenti a cui concedere l'accesso. Le informazioni possono essere relative all'account Microsoft o all'account di lavoro Active Directory per gli utenti.
- Questa procedura presuppone che verrà usata una delle immagini modello fornite nell'ambito della sottoscrizione oppure che sia stata già caricata l'immagine modello da usare. Se è necessario caricare un'immagine modello diversa, è possibile effettuare questa operazione nella pagina Immagini modello. Fare clic su **carica un'immagine modello** e seguire i passaggi della procedura guidata.
- Se si desidera usare l'immagine di Office 365 ProPlus, Consultare le informazioni in [questo articolo](remoteapp-officesubscription.md).
- Se si vogliono offrire app personalizzate o programmi line-of-business, Creare una nuova [immagine](remoteapp-imageoptions.md) e usarla nella raccolta nel cloud.
- Determinare se è necessario connettersi a una rete virtuale. Se si sceglie di connettersi a una rete virtuale, assicurarsi che soddisfi le [linee guida sulle dimensioni](remoteapp-vnetsizing.md) e che [possa connettersi a RemoteApp](remoteapp-vnet.md). Per altre informazioni, vedere l'[articolo sulla pianificazione della rete virtuale](remoteapp-planvnet.md).
- Se si usa una rete virtuale, decidere se si desidera aggiungerla al dominio locale di Active Directory.

## Passaggio 1: Creare una raccolta cloud con o senza una rete virtuale##


Utilizzare la procedura seguente per **creare una raccolta solo cloud**:

1. Nel portale di gestione andare alla pagina RemoteApp.
2. Fare clic su **Nuovo > Creazione rapida**.
3. Immettere un nome per la raccolta e selezionare l'area.
4. Scegliere il piano che da usare: standard o di base.
5. Scegliere il modello da usare per questa raccolta.

	**Suggerimento:** la sottoscrizione per RemoteApp include [immagini modello](remoteapp-images.md) che contengono le applicazioni Office 365 o Office 2013 (per l'uso in valutazione), alcune pubblicate (ad esempio Word) e altre pronte per la pubblicazione. È anche possibile creare una nuova [immagine](remoteapp-imageoptions.md) e usarla nella raccolta nel cloud.


1. Fare clic su **Crea raccolta RemoteApp**.
	
	**Importante:** il provisioning della raccolta può richiedere fino a 30 minuti.

Dopo avere creato la raccolta di Azure RemoteApp, fare doppio clic sul nome della raccolta. Verrà visualizzata la pagina **Avvio rapido** dove è possibile completare la configurazione della raccolta.

Per creare una **raccolta cloud + rete virtuale**, seguire questa procedura:

1. Nel portale di gestione di Azure passare alla pagina di Azure RemoteApp.
2. Fare clic su **Nuovo** > **Crea con rete virtuale**.
3. Immettere un nome per la raccolta.
4. Scegliere il piano che da usare: standard o di base.
5. Scegliere la rete virtuale creata. Per informazioni su come eseguire questa operazione, Per ora, i passaggi sono presenti nell'argomento[ibrido](remoteapp-create-hybrid-deployment.md).
6. Decidere se si desidera aggiungere la raccolta al dominio. In caso affermativo, è necessario usare Azure AD Connect per integrare Azure Active Directory e l'ambiente Active Directory. Questa procedura è descritta nel **passaggio 2** riportato di seguito.
6. Fare clic su **Crea raccolta RemoteApp**.


## Passaggio 2: Configurare la sincronizzazione della directory di Active Directory (facoltativo) ##

Se si vuole usare Active Directory, Azure RemoteApp richiede la sincronizzazione della directory tra Azure Active Directory e l'istanza locale di Active Directory in modo da sincronizzare utenti, contatti e password con il tenant di Azure Active Directory. Per informazioni sulla pianificazione, vedere [Configurare Active Directory per Azure RemoteApp](remoteapp-ad.md). Per informazioni, è inoltre possibile passare direttamente ad [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/).

## Passaggio 3: Pubblicare le app ##

Un'app di Azure RemoteApp è l'app o il programma da fornire ai propri utenti. Si trova nell'immagine modello caricata per la raccolta. Quando un utente accede a un'app, sembra che l'app venga eseguita nell'ambiente locale, mentre in realtà viene eseguita in una macchina virtuale in Azure.

Prima che gli utenti possano accedere alle app, è necessario pubblicarle. La pubblicazione delle app consente agli utenti di accedere tramite il client di Desktop remoto.
 
È possibile pubblicare più app nella raccolta di Azure RemoteApp. Nella pagina di pubblicazione fare clic su **Pubblica** per aggiungere un programma. È possibile eseguire la pubblicazione dal menu **Start** dell'immagine modello oppure specificando il percorso nell'immagine modello dell'app. Se si sceglie l'aggiunta dal menu **Start**, scegliere l'app da pubblicare. Se si sceglie di fornire il percorso all'app, specificare il nome dell'app e il percorso in cui è installata nell'immagine modello.

## Passaggio 4: Configurare l'accesso utente ##

A questo punto, dopo avere creato la raccolta, è necessario aggiungere gli utenti che potranno usare le risorse remote. Se si usa Active Directory, gli utenti a cui viene fornito l'accesso devono esistere nel tenant di Active Directory associato alla sottoscrizione usata per creare la raccolta.

1.	Nella pagina Avvio rapido fare clic su **Configura accesso utente**.
2.	Immettere l'account di lavoro (da Active Directory) o l'account Microsoft al quale concedere l'accesso.

	**Note:**

	Assicurarsi di usare il formato “utente@dominio.com”.

	Se si usa Office 365 ProPlus alla raccolta, è necessario usare le identità di Active Directory per gli utenti. Ciò consente di convalidare la licenza.

3.	Dopo la convalida degli utenti, fare clic su **Salva**.


## Passaggi successivi ##

La procedura è stata completata e la raccolta di Azure RemoteApp nel cloud è stata creata e distribuita. Il passaggio successivo consiste nel chiedere agli utenti di scaricare e installare il client Desktop remoto. L'URL del client è indicato nella pagina Avvio rapido di Azure RemoteApp. Chiedere quindi agli utenti di connettersi al client e di accedere alle app pubblicate.

### Come contribuire al miglioramento 
Non tutti sanno che oltre alla classificazione di questo articolo e all'aggiunta di commenti di seguito, è possibile apportare modifiche all'articolo stesso. Mancano informazioni? Alcune informazioni non sono corrette? Qualcosa non è abbastanza chiaro? Scorrere verso l'alto e fare clic su **Modifica in GitHub** per apportare modifiche. Dopo che saranno state esaminate e approvate, le modifiche e i miglioramenti suggeriti dagli utenti saranno applicati all'articolo.

<!---HONumber=AcomDC_0817_2016-->