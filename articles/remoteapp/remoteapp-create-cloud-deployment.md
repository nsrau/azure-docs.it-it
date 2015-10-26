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
	ms.date="09/11/2015" 
	ms.author="elizapo"/>

# Come creare una raccolta cloud di Azure RemoteApp

Sono disponibili due tipi di raccolte Azure RemoteApp:

- Cloud: risiede completamente in Azure. È possibile scegliere di salvare tutti i dati nel cloud (raccolta solo cloud) o connettere la raccolta a una rete virtuale e salvare i dati in questa posizione.   
- Ibrida: include una rete virtuale per l'accesso in locale, è necessario l'uso di Azure Active Directory e un ambiente Active Directory locale.

Questa esercitazione illustra il processo di creazione di una raccolta nel cloud. i quattro passaggi della creazione di una distribuzione cloud.

1.	Creare una raccolta RemoteApp.
2.	Facoltativamente, configurare la sincronizzazione della directory. Se si usano Azure Active Directory e Active Directory, è necessario sincronizzare utenti, contatti e password dall'ambiente Active Directory locale con il tenant di Azure Active Directory.
5.	Pubblicare app di RemoteApp.
6.	Configurare l'accesso utente.

**Nota** *Questo argomento è in fase di rielaborazione. I passaggi sono stati aggiornati in modo da riflettere la nuova interfaccia utente, ma l'intero argomento non è ancora stato ripubblicato. È in corso la scrittura di alcuni articoli che renderanno più semplice l'individuazione delle opzioni di autenticazione e raccolta. Se quindi le informazioni contenute in questo articolo non risultano chiare, tenere presente che verrà proposta una soluzione al più presto. Grazie.*

**Prima di iniziare**

Prima di creare la raccolta, è necessario eseguire le operazioni seguenti:

- [Accedere](http://azure.microsoft.com/services/remoteapp/) ad Azure RemoteApp. 
- Raccogliere informazioni sugli utenti a cui concedere l'accesso. Le informazioni possono essere relative all'account Microsoft o all'account di lavoro Active Directory per gli utenti.
- Questa procedura presuppone che verrà usata una delle immagini modello fornite nell'ambito della sottoscrizione oppure che sia stata già caricata l'immagine modello da usare. Se è necessario caricare un'immagine modello diversa, è possibile effettuare questa operazione nella pagina Immagini modello. Fare clic su **carica un'immagine modello** e seguire i passaggi della procedura guidata. 
- Se si desidera usare l'immagine di Office 365 ProPlus, consultare le informazioni in [questo articolo](remoteapp-officesubscription.md).
- Se si vogliono offrire app personalizzate o programmi line-of-business, Creare una nuova [immagine](remoteapp-imageoptions.md) e usarla nella raccolta nel cloud.
- Determinare se è necessario connettersi a una rete virtuale. Se si sceglie di connettersi a una rete virtuale, assicurarsi che soddisfi le [linee guida sulle dimensioni](remoteapp-vnetsizing.md) e che [possa connettersi a RemoteApp](remoteapp-vnet.md). Vedere l’[articolo pianificazione di reti virtuali ](remoteapp-planvpn.md)per ulteriori informazioni.
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

Dopo aver creato la raccolta di RemoteApp, fare doppio clic sul nome della raccolta. Questo visualizzerà la pagina **Avvio rapido** - qui è possibile completare la configurazione della raccolta.

Per creare una **raccolta cloud + rete virtuale**, seguire questa procedura:

1. Nel portale di gestione andare alla pagina RemoteApp.
2. Fare clic su **Nuovo** > **Crea con rete virtuale**.
3. Immettere un nome per la raccolta.
4. Scegliere il piano che da usare: standard o di base.
5. Scegliere la rete virtuale creata. Per informazioni su come eseguire questa operazione, Per ora, i passaggi sono presenti nell’argomento[ibrido](remoteapp-create-hybrid-deployment.md).
6. Decidere se si desidera aggiungere la raccolta al dominio. In caso affermativo, è necessario usare Azure AD Connect per integrare Azure Active Directory e l'ambiente Active Directory. Questa procedura è descritta nel **passaggio 2** riportato di seguito.
6. Fare clic su **Crea raccolta RemoteApp**.


## Passaggio 2: Configurare la sincronizzazione della directory di Active Directory (facoltativo) ##

Se si vuole usare Active Directory, RemoteApp richiede la sincronizzazione della directory tra Azure Active Directory e l'istanza locale di Active Directory in modo da sincronizzare utenti, contatti e password con il tenant di Azure Active Directory. Per informazioni sulla pianificazione, vedere [Configurare Active Directory per Azure RemoteApp](remoteapp-ad.md). Per informazioni, è inoltre possibile passare direttamente a [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx).

## Passaggio 3: Pubblicare app di RemoteApp ##

Una app di RemoteApp è l'app o il programma da fornire ai propri utenti. Si trova nell'immagine modello caricata per la raccolta. Quando un utente accede a una app di RemoteApp, questa si comporta come se venisse eseguita nel suo ambiente locale, mentre in realtà viene eseguita in Azure.

Prima che gli utenti possano accedere alle app di RemoteApp, è necessario pubblicarle nel feed degli utenti finali, ovvero un elenco di app disponibili cui gli utenti accedono tramite il client di Desktop remoto.
 
È possibile pubblicare più app nella raccolta RemoteApp. Dalla pagina di pubblicazione di RemoteApp fare clic su **Pubblica** per aggiungere un programma. È possibile eseguire la pubblicazione dal menu Start dell'immagine modello oppure specificando il percorso nell'immagine modello dell'app. Se si sceglie l'aggiunta dal menu Start, scegliere l'app da pubblicare. Se si sceglie di fornire il percorso all'app, specificare il nome dell'app e il percorso in cui è installata nell'immagine modello.

## Passaggio 4: Configurare l'accesso utente ##

A questo punto, dopo aver creato la raccolta RemoteApp, è necessario aggiungere gli utenti che potranno usare le risorse remote. Se si usa Active Directory, gli utenti a cui viene fornito l'accesso devono esistere nel tenant di Active Directory associato alla sottoscrizione usata per creare la raccolta RemoteApp.

1.	Nella pagina Avvio rapido fare clic su **Configura accesso utente**. 
2.	Immettere l'account di lavoro (da Active Directory) o l'account Microsoft al quale concedere l'accesso.

	**Note:**

	Assicurarsi di usare il formato “utente@dominio.com”.

	Se si usa Office 365 ProPlus alla raccolta, è necessario usare le identità di Active Directory per gli utenti. Ciò consente di convalidare la licenza.

3.	Dopo la convalida degli utenti, fare clic su **Salva**.


## Passaggi successivi ##

La procedura è stata completata e la raccolta RemoteApp nel cloud è stata creata e distribuita. Il passaggio successivo consiste nel chiedere agli utenti di scaricare e installare il client Desktop remoto. L'URL del client è indicato nella pagina Avvio rapido di RemoteApp. Chiedere quindi agli utenti di connettersi al client e di accedere alle app pubblicate.

 

<!---HONumber=Oct15_HO3-->