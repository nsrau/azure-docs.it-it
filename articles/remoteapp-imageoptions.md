<properties 
    pageTitle="Creare un'immagine di RemoteApp"
    description="Informazioni sulle opzioni disponibili per la creazione di immagini per RemoteApp" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/08/2015" 
    ms.author="elizapo" />



# Creare un'immagine di RemoteApp

Azure RemoteApp usa immagini per contenere le app condivise con gli utenti. Per creare una raccolta RemoteApp con una scelta di applicazioni personalizzata, nel cloud o ibrida, iniziare con la creazione di un'immagine contenente le applicazioni installate. Creare quindi una raccolta che usa l'immagine, assegnare utenti alla raccolta e pubblicare le app per gli utenti desiderati.

Per creare o usare le immagini sono disponibili diverse opzioni. Il [requisito](remoteapp-imagereqs.md) di base per un'immagine è che sia in esecuzione Windows Server 2012 R2 e che sia installato il ruolo Host sessione Desktop remoto (RDSH). Sarà interessante vedere come soddisfare questo requisito.

Per le immagini sono disponibili le opzioni seguenti:

- È possibile importare e usare un'[immagine basata su una macchina virtuale di Azure](remoteapp-image-on-azurevm.md). Questa opzione è utile per le applicazioni line-of-business che richiedono impostazioni personalizzate. È possibile personalizzare l'immagine per il funzionamento con l'applicazione.
- È possibile [creare e caricare un'immagine personalizzata](remoteapp-create-custom-image.md). Questa opzione è utile se si ha già un'immagine usata per la distribuzione locale di Servizi Desktop remoto.
- È possibile usare una delle [immagini modello](remoteapp-images.md) incluse nella sottoscrizione di RemoteApp. Queste immagini vengono create e gestite dal team di RemoteApp e contengono alcune applicazioni standard (come le applicazioni Office) che è possibile rendere disponibili agli utenti. Si noti che in un ambiente di produzione è possibile usare solo l'immagine di Office 365 Pro Plus.

Indipendentemente da dove si ottiene l'immagine o da come la si crea, è opportuno verificare di avere compreso i [requisiti per le app](remoteapp-appreqs.md) per garantire il corretto funzionamento dell'app in RemoteApp. Il passaggio successivo consiste nel creare una raccolta [nel cloud](remoteapp-create-cloud-deployment.md) o [ibrida](remoteapp-create-hybrid-deployment.md).

<!--HONumber=52-->
