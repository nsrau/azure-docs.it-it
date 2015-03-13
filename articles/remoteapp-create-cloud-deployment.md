<properties 
	pageTitle="Come creare una raccolta RemoteApp nel cloud" 
	description="Informazioni su come creare una distribuzione di RemoteApp che salva i dati nel cloud Azure." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/17/2015" 
	ms.author="elizapo"/>

#Come creare una raccolta RemoteApp nel cloud

Sono disponibili due tipi di raccolte RemoteApp: 

- Cloud: risiede completamente in Azure e viene creata tramite l'opzione **Creazione rapida** nel portale di gestione di Azure.  
- Ibrida: include una rete virtuale per l'accesso locale e viene creata con l'opzione **Crea con VPN** nel portale di gestione.

Questa esercitazione illustra il processo di creazione di una raccolta nel cloud. Sono previsti quattro passaggi: 

1.	Creare una raccolta RemoteApp.
2.	Facoltativamente, configurare la sincronizzazione della directory. RemoteApp richiede questa configurazione per sincronizzare utenti, contatti e password dall'ambiente Active Directory locale con il tenant di Azure Active Directory.
5.	Pubblicare app di RemoteApp.
6.	Configurare l'accesso utente.

**Operazioni preliminari**

Prima di creare la raccolta, è necessario eseguire le operazioni seguenti:

- Iscriversi a RemoteApp. È possibile effettuare tale iscrizione all'indirizzo [http://azure.microsoft.com/services/remoteapp/](http://azure.microsoft.com/services/remoteapp/).
- Raccogliere informazioni sugli utenti a cui concedere l'accesso. Le informazioni possono essere relative all'account Microsoft o all'account di lavoro Active Directory per gli utenti.
- Questa procedura presuppone che verrà usata una delle immagini modello fornite nell'ambito della sottoscrizione oppure che sia stata già caricata l'immagine modello da usare. Se è necessario caricare un'immagine modello diversa, è possibile effettuare questa operazione nella pagina Immagini modello. Fare clic su **Carica un'immagine modello** e seguire i passaggi della procedura guidata. 
- Se si vogliono offrire app personalizzate o programmi line-of-business, Creare una nuova [immagine modello personalizzata](http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/) e usarla nella distribuzione cloud.

## **Passaggio 1: Creare una raccolta RemoteApp** ##



1. Nel [portale di gestione di Microsoft Azure](http://manage.windowsazure.com) passare alla pagina RemoteApp.
2. Fare clic su **Nuovo > Creazione rapida**.

3. Immettere un nome per la raccolta e selezionare l'area.
4. Scegliere il piano che da usare: standard o di base.
5. Scegliere il modello da usare per questa raccolta. 

	**Suggerimento:** la sottoscrizione a RemoteApp viene fornita con [immagini modello](http://azure.microsoft.com/documentation/articles/remoteapp-images/) che contengono Office 365 o programmi di Office 2013 (per l'uso in valutazione), alcuni pubblicati (ad esempio Word) e altri pronti per la pubblicazione. È anche possibile creare una nuova [immagine modello personalizzata](http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/) e usarla nella raccolta nel cloud.


1. Fare clic su **Crea raccolta RemoteApp**.
	
	**Importante:** il provisioning della raccolta può richiedere fino a 30 minuti.

Dopo la creazione del servizio RemoteApp, passare alla pagina **Avvio rapido** per continuare con la procedura di configurazione.


## **Passaggio 2: Configurare la sincronizzazione della directory (facoltativo)** ##

Se si vuole usare Active Directory, RemoteApp richiede la sincronizzazione della directory tra Azure Active Directory e l'istanza locale di Active Directory in modo da sincronizzare utenti, contatti e password con il tenant di Azure Active Directory. Per informazioni sulla pianificazione, vedere la pagina relativa alla [configurazione di Active Directory per Azure RemoteApp](http://azure.microsoft.com/documentation/articles/remoteapp-ad/).

## **Passaggio 3: Pubblicare app di RemoteApp** ##

Una app di RemoteApp è l'app o il programma da fornire ai propri utenti. Si trova nell'immagine modello caricata per la raccolta. Quando un utente accede a una app di RemoteApp, questa si comporta come se venisse eseguita nel suo ambiente locale, mentre in realtà viene eseguita in Azure. 

Prima che gli utenti possano accedere alle app di RemoteApp, è necessario pubblicarle nel feed degli utenti finali, ovvero un elenco di app disponibili cui gli utenti accedono tramite il client di Desktop remoto.
 
È possibile pubblicare più app nella raccolta RemoteApp. Dalla pagina di pubblicazione di RemoteApp fare clic su **Pubblica** per aggiungere un programma. È possibile eseguire la pubblicazione dal menu Start dell'immagine modello oppure specificando il percorso nell'immagine modello dell'app. Se si sceglie l'aggiunta dal menu Start, scegliere l'app da pubblicare. Se si sceglie di fornire il percorso all'app, specificare il nome dell'app e il percorso in cui è installata nell'immagine modello.

## **Passaggio 4: Configurare l'accesso utente** ##

A questo punto, dopo aver creato la raccolta RemoteApp, è necessario aggiungere gli utenti che potranno usare le risorse remote. Se si usa Active Directory, gli utenti a cui viene fornito l'accesso devono esistere nel tenant di Active Directory associato alla sottoscrizione usata per creare la raccolta RemoteApp.

1.	Nella pagina Avvio rapido fare clic su **Configura accesso utente**. 
2.	Immettere l'account di lavoro (da Active Directory) o l'account Microsoft al quale concedere l'accesso.

	**Note:** 

	Assicurarsi di usare il formato "utente@dominio.com".

	Se si usa Office 365 ProPlus alla raccolta, è necessario usare le identità di Active Directory per gli utenti. Ciò consente di convalidare la licenza. 

3.	Dopo che gli utenti vengono convalidati, fare clic su **Salva**.


## Passaggi successivi ##

La procedura è stata completata e la raccolta RemoteApp nel cloud è stata creata e distribuita. Il passaggio successivo consiste nel chiedere agli utenti di scaricare e installare il client Desktop remoto. L'URL del client è indicato nella pagina Avvio rapido di RemoteApp. Chiedere quindi agli utenti di connettersi al client e di accedere alle app pubblicate.


<!--HONumber=35.2-->

<!--HONumber=46--> 
