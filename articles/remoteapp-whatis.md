<properties title="What is RemoteApp?" pageTitle="Informazioni su RemoteApp" description="Learn about Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

#Informazioni su Azure RemoteApp

Azure RemoteApp integra in Azure le funzionalità del programma Microsoft RemoteApp locale supportate da Servizi Desktop remoto. Azure RemoteApp consente di fornire un accesso remoto sicuro alle applicazioni da molti dispositivi utente diversi.

Quando si sposta RemoteApp in Azure, si sfruttano l'archiviazione, la scalabilità e la portata globale di Azure senza doversi preoccupare di una configurazione locale complessa. Microsoft fornisce la manutenzione di Azure, garantendone l'affidabilità e consentendo così all'utente di concentrarsi su altre attività più importanti, come la creazione di app utili per la propria azienda. Un altro vantaggio di Azure RemoteApp è l'accessibilità. Gli utenti possono accedere ai programmi RemoteApp da dispositivi Windows, iOS, Mac OS X e Android. Possono usare le app sviluppate nell'ambiente che preferiscono, mentre per la gestione delle app viene usato il portale di gestione di Azure. 

Continuare a leggere l'articolo per altre informazioni su RemoteApp oppure, se si è già convinti, [provarlo subito](http://azure.microsoft.com/it-it/services/remoteapp/).

Azure RemoteApp fa parte dell'[infrastruttura VDI Microsoft](http://www.microsoft.com/it-it/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novità.** Per altre informazioni su Azure RemoteApp o per verificare la scalabilità di RemoteApp, è possibili partecipare al [webinar settimanale con gli esperti](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html).

##Opzioni di distribuzione di RemoteApp
Sono disponibili due tipi di distribuzione di RemoteApp:


- Una **distribuzione cloud** è ospitata nel cloud di Azure insieme a tutti i dati dei programmi. Gli utenti possono accedere alle app effettuando l'accesso con l'account Microsoft o con le credenziali aziendali sincronizzate o federate con Azure Active Directory.
- Una **distribuzione ibrida** è ospitata nel cloud di Azure insieme ai dati ma consente agli utenti di accedere anche ai dati e alle risorse archiviati nella rete locale. Gli utenti possono accedere alle app effettuando l'accesso con le credenziali aziendali sincronizzate o federate con Azure Active Directory.

###Distribuzione cloud

La [distribuzione cloud di RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-cloud-deployment/) consente di ospitare le applicazioni nel cloud in modalità autonoma. Una distribuzione cloud esiste solo nel cloud di Azure, anziché connettersi alla rete locale.

Come parte dell'anteprima di RemoteApp vengono offerte le app di Office 2013 preinstallate e pronte per essere condivise con gli utenti. Se si sceglie di usare il software disponibile, è possibile effettuare rapidamente il provisioning del servizio.

Un altro vantaggio nell'usare la distribuzione cloud con le app di Office 2013 consiste nel fatto che le app e il sistema operativo, in cui il servizio è integrato, vengono sempre aggiornati regolarmente e Microsoft Anti-Malware endpoint protection fornisce una protezione costante. Gli utenti possono accedere alle app usando gli account Microsoft o le credenziali aziendali. Tutto ciò di cui l'amministratore deve preoccuparsi è stabilire a chi consentire l'accesso e a quali app.

È anche possibile creare una distribuzione cloud per condividere un'applicazione personalizzata o un set di applicazioni per gli utenti. A tale scopo, è necessario [creare un'immagine modello personalizzata](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-custom-image/), ovvero la modalità di pubblicazione delle app in RemoteApp, e scegliere semplicemente tale immagine, anziché l'immagine di Office 2013, quando si crea la distribuzione. 

###Distribuzione ibrida
La [distribuzione ibrida di RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-hybrid-deployment/) consente di fornire agli utenti sia un set personalizzato di applicazioni che l'accesso ai dati e alle risorse nella rete locale. Diversamente dell'immagine personalizzata usata con la distribuzione cloud, l'immagine creata per una distribuzione ibrida esegue le app in un ambiente appartenente a un dominio, garantendo un accesso completo alla rete locale e ai dati.

Integrando Active Directory con Azure Active Directory (mediante DirSync), gli utenti possono usare le credenziali aziendali per accedere alle app e ai dati. Quando si usa un account aziendale in Active Directory, è possibile integrare i criteri aziendali nel cloud per controllare le app offerte tramite RemoteApp.

Finché l'immagine modello viene compilata in Windows Server 2012 R2 con il servizio ruolo Host sessione Desktop remoto, esistono alcuni limiti per le app che è possibile pubblicare per gli utenti. Se le app funzionano correttamente in tale ambiente di immagine modello, gli utenti finali possono accedervi mediante RemoteApp. 

###Aggiornamento della distribuzione
Una delle principali differenze tra le distribuzioni ibride e cloud è rappresentata dal modo in cui vengono gestiti gli aggiornamenti software. Con una distribuzione cloud che usa l'immagine di Office 2013 preinstallata, non è necessario preoccuparsi di eventuali aggiornamenti. Il servizio viene gestito automaticamente e gli aggiornamenti vengono applicati regolarmente sia alle app che al sistema operativo.

Per distribuzioni ibride, nonché per le distribuzioni cloud che usano un'immagine modello personalizzata, la gestione dell'immagine e delle app spetta all'utente. Per le immagini appartenenti a un dominio, è possibile controllare gli aggiornamenti usando strumenti quali Windows Update, Criteri di gruppo o System Center.

Dopo aver aggiornato l'immagine modello personalizzata, è possibile caricare la nuova immagine nel cloud di Azure e quindi aggiornare la distribuzione con la nuova immagine. Questa operazione può essere eseguita dalla pagina Avvio rapido di RemoteApp o dal Dashboard.

##Client supportati di RemoteApp
Come parte della versione di anteprima di RemoteApp di Azure, è stato rilasciato una nuova applicazione client di Microsoft RemoteApp per Windows e Windows RT, nonché gli aggiornamenti alle app di Desktop remoto Microsoft per iOS e Android. Gli utenti possono usare queste app sui dispositivi mobili o di calcolo per accedere ai nuovi programmi RemoteApp.

##Passaggi successivi
Per provarlo, vedere gli articoli seguenti che descrivono come iniziare a usare RemoteApp:

- [Come creare un'immagine modello personalizzata per RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-custom-image/)
- [Come creare una distribuzione cloud di RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-cloud-deployment/)
- [Come creare una distribuzione ibrida di RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-hybrid-deployment/)
