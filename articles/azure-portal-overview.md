<properties
	pageTitle="Panoramica del portale di anteprima di Microsoft Azure"
	description="Informazioni su come usare il portale di anteprima di Microsoft Azure."
	services=""
	documentationCenter=""
	authors="davidwrede"
	manager="dwrede"
	editor="jimbe"/>

<tags
	ms.service="na"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="04/28/2015"
	ms.author="dwrede"/>

# Panoramica del portale di anteprima di Microsoft Azure

Il portale di anteprima di Microsoft Azure è una posizione centrale in cui è possibile eseguire il provisioning e gestire le risorse di Azure. Questa esercitazione fornisce informazioni generali sul portale e mostra come usare alcune delle funzionalità chiave: - Un **Marketplace completo** che permette di esplorare migliaia di elementi offerti da Microsoft e da altri fornitori e che possono essere acquistati e/o sottoposti a provisioning. - Un'**esperienza di esplorazione unificata e scalabile** che semplifica l'individuazione delle risorse desiderate e l'esecuzione di diverse operazioni di gestione. - **Pagine di gestione coerenti** (o pannelli) che permettono di gestire la vasta gamma di servizi di Azure tramite una modalità coerente di esposizione di impostazioni, azioni, informazioni sulla fatturazione, monitoraggio dello stato, dati di utilizzo e altro ancora. - Un'**esperienza personale** che permette di creare una schermata di avvio personalizzata che mostra le informazioni desiderate a ogni accesso. È anche possibile personalizzare tutti i pannelli di gestione che includono riquadri.

 ![Orientamento dell'interfaccia utente del portale di Azure][UIOrientation]

## Prima di iniziare

Per eseguire questa esercitazione, è necessaria una sottoscrizione di Azure valida. Se non si ha una sottoscrizione, [iscriversi per una versione di prova gratuita](http://azure.microsoft.com/pricing/free-trial/). Dopo avere creato una sottoscrizione, sarà possibile accedere al portale all'indirizzo [https://portal.azure.com].

## Come creare un gruppo di risorse

Il Marketplace di Azure offre migliaia di elementi che possono essere creati da un'unica posizione. Si supponga di volere creare una nuova macchina virtuale Windows Server 2012. L'hub +NUOVO è il punto di partenza per accedere a un set predefinito di categorie disponibili nel Marketplace. Ogni categoria include un piccolo set di elementi disponibili, oltre a un collegamento al Marketplace completo che mostra tutte le categorie e permette di eseguire la ricerca. Per creare una nuova macchina virtuale Windows Server 2012, eseguire le operazioni seguenti:

1.	Windows Server 2012 è disponibile, quindi è possibile selezionarlo dalla categoria Calcolo.  
2.	Inserire alcuni input di base in un form.
3.	Fare clic su 'Crea' e il provisioning della macchina virtuale verrà avviato immediatamente.

L'hub di notifica avviserà l'utente quando la risorsa è stata creata e verrà visualizzato un pannello di gestione. È possibile passare alle risorse anche in seguito.

![Categorie del portale][PortalCategories]


## Come trovare le risorse

È possibile bloccare nella schermata iniziale le risorse a cui si accede spesso, ma potrebbe essere necessario passare a una risorsa a cui non si accede di frequente. Per ottenere tutte le risorse, usare l'hub di esplorazione mostrato di seguito. È possibile filtrare le risorse in base alla sottoscrizione, scegliere/ridimensionare colonne e passare ai pannelli di gestione facendo clic sui singoli elementi.

![Hub di esplorazione][BrowseHub]

## Come gestire e delegare l'accesso a una risorsa

Questo pannello permette di connettersi alla macchina virtuale usando il desktop remoto, monitorare le metriche delle prestazioni chiave, controllare l'accesso a questa macchina virtuale usando il controllo degli accessi in base al ruolo (RBAC, Role Based Access Control), configurare la VM ed eseguire altre importanti attività di gestione. La delega dell'accesso in base al ruolo è essenziale per la gestione su vasta scala. Per altre informazioni, fare clic [qui](role-based-access-control-configure.md). Per delegare l'accesso a una risorsa, eseguire le operazioni seguenti:

1.	Passare alla risorsa.
2.	Nella sezione Informazioni di base fare clic su 'Tutte le impostazioni'.
3.	Fare clic su 'Utenti' nell'elenco di impostazioni.
4.	Fare clic su 'Aggiungi' sulla barra dei comandi.
5.	Scegliere un utente e un ruolo.

![Gestione di una risorsa][ManageResource]

## Come personalizzare un pannello di risorse

Azure preconfigura i pannelli per le risorse, ma l'utente può personalizzare i riquadri nei pannelli. È possibile passare con facilità alla modalità di personalizzazione per aggiungere, rimuovere, ridimensionare o ridisporre i riquadri. Per personalizzare un pannello, eseguire le operazioni seguenti:

1.	Passare alla risorsa.
2.	Fare clic sui puntini di sospensione '…' nella parte superiore del pannello da personalizzare.
3.	Fare clic su 'Aggiungi parti'.
4.	Iniziare a trascinare e rilasciare le parti selezionate.  

![Personalizzazione dei pannelli][CustomizeBlades]

## Come ottenere assistenza

In caso di problemi, sono disponibili numerose opzioni per il supporto. Il portale include una pagina Guida e supporto, che può fornire le indicazioni necessarie. In base al [piano di supporto](http://azure.microsoft.com/support/plans/) attivato, è anche possibile creare ticket di supporto direttamente nel portale. Dopo la creazione del ticket di supporto, sarà possibile gestire il ciclo di vita del ticket dal portale. Per visualizzare la pagina Guida e supporto, passare a Sfoglia -> Guida e supporto.

![Guida e supporto][HelpSupport]

## Riepilogo

In questa esercitazione è stato illustrato quanto segue: - Come iscriversi, ottenere una sottoscrizione e passare al portale - Informazioni generali sull'interfaccia utente del portale e su come creare e visualizzare le risorse - Come creare una risorsa ed esplorare le risorse - Informazioni sulla struttura o sui pannelli di gestione e su come gestire in modo coerente diversi tipi di risorse - Come personalizzare il portale per evidenziare le informazioni rilevanti - Come controllare l'accesso alle risorse mediante il controllo degli accessi in base al ruolo - Come visualizzare la Guida e ottenere supporto

Il portale di anteprima di Microsoft Azure semplifica in modo radicale lo sviluppo e la gestione delle applicazioni nel cloud. Vedere il [blog sulla gestione](http://azure.microsoft.com/blog/topics/management/) per rimanere aggiornati, poiché Microsoft [esamina i commenti e i suggerimenti](http://feedback.azure.com/forums/223579-azure-preview-portal) e apporta miglioramenti costantemente. Il [blog di ScottGu](http://weblogs.asp.net/scottgu) è un'altra risorsa ottimale per ottenere informazioni su tutti gli aggiornamenti di Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png

<!---HONumber=July15_HO4-->