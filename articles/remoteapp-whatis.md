<properties 
	pageTitle="Informazioni su RemoteApp" 
	description="Informazioni su Azure RemoteApp." 
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
	ms.date="04/08/2015" 
	ms.author="elizapo"/>

# Informazioni su Azure RemoteApp

Azure RemoteApp integra in Azure le funzionalità del programma Microsoft RemoteApp locale supportate da Servizi Desktop remoto. Azure RemoteApp consente di fornire un accesso remoto sicuro alle applicazioni da molti dispositivi utente diversi.

Quando si sposta RemoteApp in Azure, si sfruttano l'archiviazione, la scalabilità e la portata globale di Azure senza doversi preoccupare di una configurazione locale complessa. Microsoft fornisce la manutenzione di Azure, garantendone l'affidabilità e consentendo così all'utente di concentrarsi su altre attività più importanti, come la creazione di app utili per la propria azienda. Un altro vantaggio di Azure RemoteApp è l'accessibilità. Gli utenti possono accedere ai programmi RemoteApp da dispositivi Windows, iOS, Mac OS X e Android. Possono usare le app sviluppate nell'ambiente che preferiscono, mentre per la gestione delle app viene usato il portale di gestione di Azure.

Continuare a leggere l'articolo per altre informazioni su RemoteApp oppure, se si è già convinti, [provarlo subito](http://azure.microsoft.com/services/remoteapp/).

Domande su Azure RemoteApp? Vedere le [Domande frequenti](remoteapp-faq.md).

Azure RemoteApp fa parte dell'[infrastruttura VDI Microsoft](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novità** Per altre informazioni su Azure RemoteApp o per verificare la scalabilità di RemoteApp, è possibile partecipare al [webinar settimanale con gli esperti](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## Opzioni di distribuzione di RemoteApp
Sono disponibili due tipi di raccolte RemoteApp:


- Una **raccolta nel cloud** è ospitata nel cloud di Azure insieme a tutti i dati dei programmi. Gli utenti possono accedere alle app effettuando l'accesso con l'account Microsoft o con le credenziali aziendali sincronizzate o federate con Azure Active Directory.
- Una **raccolta ibrida** è ospitata nel cloud di Azure insieme ai dati ma consente agli utenti di accedere anche ai dati e alle risorse archiviati nella rete locale. Gli utenti possono accedere alle app effettuando l'accesso con le credenziali aziendali sincronizzate o federate con Azure Active Directory.

### Raccolta cloud

La [raccolta nel cloud di RemoteApp](remoteapp-create-cloud-deployment.md) consente di ospitare le applicazioni nel cloud in modalità autonoma. Una raccolta cloud esiste solo nel cloud di Azure, anziché connettersi alla rete locale.

Nella versione di valutazione di RemoteApp vengono offerte le app di Office 365 ProPlus o Office 2013 preinstallate e pronte per essere condivise con gli utenti. Se si sceglie di usare il software disponibile, è possibile effettuare rapidamente il provisioning del servizio.

Un altro vantaggio dell'uso della raccolta cloud con le app di Office consiste nel fatto che le app e il sistema operativo, su cui è basato il servizio, vengono aggiornati regolarmente e la protezione degli endpoint di Microsoft Antimalware assicura una difesa costante. Gli utenti possono accedere alle app usando gli account Microsoft o le credenziali aziendali. Tutto ciò di cui l'amministratore deve preoccuparsi è stabilire a chi consentire l'accesso e a quali app.

È anche possibile creare una raccolta cloud per condividere un'applicazione personalizzata o un set di applicazioni per gli utenti. A tale scopo è necessario [creare un'immagine modello personalizzata](remoteapp-imageoptions.md), che rappresenta il modo in cui vengono pubblicate le app in RemoteApp, e scegliere semplicemente tale immagine, anziché l'immagine di Office 2013, quando si crea la raccolta.

####Quando scegliere la raccolta nel cloud

Una raccolta nel cloud è adatta quando l'applicazione non richiede la connettività alle risorse presenti sulla rete privata aziendale tramite un dispositivo VPN. Se l'applicazione usa solo risorse su Internet, OneDrive o Azure, questa soluzione è consigliabile e più semplice da configurare.

### Raccolta ibrida
La [raccolta ibrida di RemoteApp](remoteapp-create-hybrid-deployment.md) consente di fornire agli utenti sia un set personalizzato di applicazioni che l'accesso ai dati e alle risorse nella rete locale. Diversamente dell'immagine personalizzata usata con la raccolta cloud, l'immagine creata per una raccolta ibrida esegue le app in un ambiente appartenente a un dominio, garantendo un accesso completo alla rete locale e ai dati.

Integrando Active Directory con Azure Active Directory (mediante DirSync), gli utenti possono usare le credenziali aziendali per accedere alle app e ai dati. Quando si usa un account aziendale in Active Directory, è possibile integrare i criteri aziendali nel cloud per controllare le app offerte tramite RemoteApp.

Finché l'immagine modello viene compilata in Windows Server 2012 R2 con il servizio ruolo Host sessione Desktop remoto, esistono alcuni limiti per le app che è possibile pubblicare per gli utenti. Se le app funzionano correttamente in tale ambiente di immagine modello, gli utenti finali possono accedervi mediante RemoteApp.

####Quando scegliere una raccolta ibrida

Una raccolta ibrida è più adatta se è necessaria la connettività alla rete privata. Alcuni esempi sono:

- Server di file privati
- Quicken
- Database privati
- e così via.

Questa soluzione è generalmente più adatta a grandi aziende con molte risorse sulle proprie reti private, che non possono essere trasferite nel cloud.

### Aggiornamento della raccolta
Una delle principali differenze tra le raccolte ibride e cloud è rappresentata dal modo in cui vengono gestiti gli aggiornamenti software. Con una raccolta cloud che usa l'immagine di Office 365 ProPlus o Office 2013 preinstallata, non è necessario preoccuparsi di eventuali aggiornamenti. Il servizio viene gestito automaticamente e gli aggiornamenti vengono applicati regolarmente sia alle app che al sistema operativo.

Per le raccolte ibride, nonché per le raccolte cloud che usano un'immagine modello personalizzata, la gestione dell'immagine e delle app spetta all'utente. Per le immagini appartenenti a un dominio, è possibile controllare gli aggiornamenti usando strumenti quali Windows Update, Criteri di gruppo o System Center.

Dopo aver aggiornato l'immagine modello personalizzata, è possibile caricare la nuova immagine nel cloud di Azure e quindi aggiornare la raccolta con la nuova immagine (questa operazione può essere eseguita dalla pagina **Avvio rapido** di RemoteApp o dal Dashboard).

## Client supportati di RemoteApp
Azure RemoteApp è supportato nelle app client RemoteApp per Windows e Windows RT, nonché nelle app di Desktop remoto Microsoft per Mac, iOS e Android. Gli utenti possono usare queste app sui dispositivi mobili o di calcolo per accedere ai nuovi programmi RemoteApp.

## Passaggi successivi
Per provarlo, vedere gli articoli seguenti che descrivono come iniziare a usare RemoteApp:

- [Creare un'immagine di RemoteApp](remoteapp-imageoptions.md)
- [Come creare una raccolta RemoteApp nel cloud](remoteapp-create-cloud-deployment.md)
- [Come creare una raccolta ibrida di RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Funzionamento delle licenze in RemoteApp](remoteapp-licensing.md)
- [Procedure consigliate per l'uso di Azure RemoteApp](remoteapp-bestpractices.md)
- [Domande frequenti su Azure RemoteApp](remoteapp-faq.md)

<!--HONumber=54-->