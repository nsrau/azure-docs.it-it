<properties 
	pageTitle="Che cos'è Azure RemoteApp? | Microsoft Azure" 
	description="Informazioni su come condividere applicazioni e risorse in qualsiasi dispositivo con Azure RemoteApp." 
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
	ms.topic="get-started-article" 
	ms.date="06/18/2016" 
	ms.author="elizapo"/>

# Informazioni su Azure RemoteApp

Azure RemoteApp integra in Azure le funzionalità del programma Microsoft RemoteApp locale supportate da Servizi Desktop remoto. Azure RemoteApp consente di fornire un accesso remoto sicuro alle applicazioni da molti dispositivi utente diversi. Sostanzialmente Azure RemoteApp ospita nel cloud le sessioni di Terminal Server non permanenti per poterle usare e condividere con gli utenti.

Con RemoteApp di Azure è possibile condividere app e risorse con utenti su quasi tutti i dispositivi. Ospitiamo le app nel cloud, ossia ci occupiamo dell'hardware e della scalabilità per soddisfare le richieste degli utenti. Si devono solo caricare le app che si desidera condividere e far poi usare agli utenti tali app. [Gli utenti riescono a mantenere i propri dispositivi](remoteapp-clients.md), mentre si gestisce tutto tramite il portale di Azure. Si ha anche la possibilità di utilizzare le credenziali aziendali, consentendo di garantire la sicurezza dei dati e delle applicazioni.

Continuare a leggere l'articolo per altre informazioni su Azure RemoteApp oppure, se si è già convinti, [provarlo subito](https://azure.microsoft.com/services/remoteapp/).

Domande su Azure RemoteApp? Vedere le [Domande frequenti](remoteapp-faq.md).

Azure RemoteApp fa parte dell'[infrastruttura VDI Microsoft](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novità** Per altre informazioni su Azure RemoteApp o per verificare la scalabilità di Azure RemoteApp, è possibile partecipare al [webinar settimanale con gli esperti](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## Raccolte di Azure RemoteApp
Sono disponibili due tipi di [raccolte Azure RemoteApp](remoteapp-collections.md):


- Una **raccolta nel cloud** è ospitata nel cloud insieme ai dati dei programmi. Gli utenti possono accedere alle app effettuando l'accesso con l'account Microsoft o con le credenziali aziendali sincronizzate o federate con Azure Active Directory.

	Scegliere una raccolta cloud quando l'applicazione che si desidera condividere non richiede una connessione a una risorsa della rete privata della propria azienda (ad esempio, tramite un dispositivo VPN). Se l'applicazione usa risorse in Internet, OneDrive o Azure, una raccolta cloud è consigliabile ed è inoltre la più rapida da creare.

- Una **raccolta ibrida** è ospitata nel cloud di Azure insieme ai dati ma consente agli utenti di accedere anche ai dati e alle risorse archiviati nella rete locale. Gli utenti possono accedere alle app effettuando l'accesso con le credenziali aziendali sincronizzate o federate con Azure Active Directory.

	Scegliere una raccolta ibrida se è necessaria una connessione alle risorse della rete privata della propria azienda, ad esempio, se l'applicazione deve accedere a uno dei seguenti elementi:

	- Server di file sulla rete intranet
	- Quicken
	- Database protetti da firewall

	Questa soluzione è generalmente più adatta a grandi aziende con molte risorse sulle proprie reti private, che non possono essere trasferite nel cloud.

I diversi insiemi hanno diverse opzioni, tra cui le reti, quindi scoprire [quale insieme](remoteapp-collections.md) è adatto alle proprie esigenze.


### Aggiornamento della raccolta
Una delle principali differenze tra le raccolte ibride e cloud è rappresentata dal modo in cui vengono gestiti gli aggiornamenti software. Con una raccolta cloud che usa l'immagine di Office 365 ProPlus o Office 2013 preinstallata, non è necessario preoccuparsi di eventuali aggiornamenti. Il servizio viene gestito automaticamente e gli aggiornamenti vengono applicati regolarmente sia alle app che al sistema operativo.

Per le raccolte ibride, nonché per le raccolte cloud che usano un'immagine modello personalizzata, la gestione dell'immagine e delle app spetta all'utente. Per le immagini appartenenti a un dominio, è possibile controllare gli aggiornamenti usando strumenti quali Windows Update, Criteri di gruppo o System Center.

Dopo aver aggiornato l'immagine modello personalizzata, è possibile caricare la nuova immagine nel cloud di Azure e quindi aggiornare la raccolta con la nuova immagine (questa operazione può essere eseguita dalla pagina **Avvio rapido** di RemoteApp o dal Dashboard).

Per altre informazioni vedere [Aggiornare la raccolta](remoteapp-update.md).

## Client supportati di RemoteApp
Azure RemoteApp è supportato nelle app client RemoteApp per Windows e Windows RT, nonché nelle app di Desktop remoto Microsoft per Mac, iOS e Android. Gli utenti possono usare queste app sui dispositivi mobili o di calcolo per accedere ai nuovi programmi di Azure RemoteApp.

Per altre informazioni sui client vedere [Accesso alle app in Azure RemoteApp](remoteapp-clients.md).

## Passaggi successivi
Per provarlo, vedere gli articoli seguenti che descrivono come iniziare a usare Azure RemoteApp:

- [Tipo di raccolta necessario per RemoteApp di Azure](remoteapp-collections.md)
- [Creare un'immagine di Azure RemoteApp](remoteapp-imageoptions.md)
- [Come creare una raccolta cloud di Azure RemoteApp](remoteapp-create-cloud-deployment.md)
- [Come creare una raccolta ibrida per Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Funzionamento delle licenze in Azure RemoteApp](remoteapp-licensing.md)
- [Procedure consigliate per l'uso di Azure RemoteApp](remoteapp-bestpractices.md)
- [Domande frequenti su Azure RemoteApp](remoteapp-faq.md)
 

### Come contribuire al miglioramento 
Non tutti sanno che oltre alla classificazione di questo articolo e all'aggiunta di commenti di seguito, è possibile apportare modifiche all'articolo stesso. Mancano informazioni? Alcune informazioni non sono corrette? Qualcosa non è abbastanza chiaro? Scorrere verso l'alto e fare clic su **Modifica in GitHub** o **Modifica** per apportare modifiche. Una volta esaminati e approvati, le modifiche e i miglioramenti suggeriti dagli utenti verranno applicati proprio qui.

<!---HONumber=AcomDC_0629_2016-->