<properties 
	pageTitle="Domande frequenti su Azure RemoteApp" 
	description="Domande frequenti su Azure RemoteApp." 
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
	ms.date="08/06/2015" 
	ms.author="elizapo"/>

# Domande frequenti su Azure RemoteApp
Finora sono state formulate le domande seguenti su Azure RemoteApp. Ve ne sono altre? Visitare i [forum di RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) per formulare le proprie domande.

## Informazioni su Azure RemoteApp ##


- **Che cos'è Azure RemoteApp?** RemoteApp è un servizio di Azure che consente di fornire un accesso remoto sicuro alle applicazioni da molti dispositivi utente diversi. Altre informazioni su [Azure RemoteApp](remoteapp-whatis.md).
- **Quali sono i due tipi di opzioni di distribuzione?** Sono disponibili due tipi di distribuzioni (o raccolte) di RemoteApp: ibrida e cloud. Valutare la migliore [opzione di distribuzione](remoteapp-whatis.md) per la propria organizzazione.

## Configurazioni supportate ##

- **Quali sono i limiti del servizio?** Sarà possibile conoscere le impostazioni predefinite e i limiti di servizio di Azure RemoteApp in [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](.\azure-subscription-service-limits.md). Per altre domande contattare Microsoft.
- **Quanti utenti sono necessari?** È necessario un numero minimo di 20 utenti. Il numero MINIMO è 20. Verrà emessa una fattura per 20. 
- **Le applicazioni personalizzate LOB (line-of-business) sono supportate?** Sì. Per usare un'applicazione personalizzata in Azure RemoteApp, creare un'[immagine modello personalizzata](remoteapp-create-custom-image.md) e caricarla nella raccolta RemoteApp.
- **L'applicazione LOB personalizzata funzionerà in Azure RemoteApp?** Il modo migliore per verificarlo è provarla. Verificare i [requisiti di compatibilità dell'applicazione](http://www.microsoft.com/download/details.aspx?id=18704) e il [centro di compatibilità di Desktop remoto](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Quale metodo di distribuzione (cloud o ibrido) è più adatto per la propria organizzazione?** Le raccolte ibride offrono un'esperienza più completa se si desidera integrazione completa con Single Sign-On (SSO) e connettività di rete protetta in locale. Le raccolte nel cloud offrono un modo semplice e rapido di isolare la distribuzione usando più metodi di autenticazione. Altre informazioni sulle [opzioni di distribuzione](remoteapp-whatis.md).
- **La raccolta ibrida richiede una rete virtuale. È possibile usare la rete virtuale esistente?** È possibile se la rete virtuale esistente è una rete virtuale di Azure. Per altre informazioni vedere "Passaggio 1: configurare la rete virtuale" nelle [istruzioni sulla raccolta ibrida](remoteapp-create-hybrid-deployment.md).
- **È possibile usare un cloud o una macchina virtuale esistente come modello per la propria raccolta RemoteApp?** Sì. È possibile creare un'immagine basata su una macchina virtuale di Azure, usare una delle immagini incluse nella sottoscrizione o o creare un'immagine personalizzata. Verificare le [opzioni per le immagini di RemoteApp](remoteapp-imageoptions.md).
- **Al momento si usa SQL o un altro database, in locale o in Azure. Che tipo di distribuzione è consigliabile usare?** Questo dipende dal luogo in cui risiede il database SQL o back-end. Se si trova in una rete privata, usare la raccolta ibrida. Se invece il database è esposto a Internet e consente connessioni client, è possibile usare la raccolta nel cloud.
- **E per quanto riguarda il mapping delle unità, le porte USB e seriali, la condivisione degli Appunti e il reindirizzamento della stampante?** Tutte queste funzionalità sono supportate in Azure RemoteApp. Il reindirizzamento della stampante e la condivisione degli Appunti sono abilitati per impostazione predefinita. Altre informazioni sul reindirizzamento sono disponibili [qui](remoteapp-redirection.md). 


- **Per quanto riguarda l'autenticazione, quali metodi sono supportati?** La raccolta nel cloud supporta account Microsoft e account Azure Active Directory, che sono anche account di Office 365. La raccolta ibrida supporta solo gli account Azure Active Directory che sono stati sincronizzati (mediante uno strumento come [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) da una distribuzione di Windows Server Active Directory e, in particolare, sincronizzati con l'opzione di sincronizzazione password oppure con la federazione di Active Directory Federation Services (ADFS) configurata. È anche possibile configurare l'autenticazione a più fattori [(MFA, Multi-Factor Authentication)](../../services/multi-factor-authentication/).

	**Nota:** gli utenti di Azure Active Directory devono provenire dal tenant associato alla sottoscrizione (è possibile visualizzare e modificare la sottoscrizione nella scheda **Impostazioni** nel portale. Per altre informazioni, vedere l'articolo relativo alla [modifica del tenant di Azure Active Directory usato da RemoteApp](remoteapp-changetenant.md)).

- **Perché non è possibile consentire l'accesso all'account Azure Active Directory?** Gli utenti di Azure Active Directory devono provenire dalla directory associata alla sottoscrizione. È possibile visualizzare o modificare tale directory nella scheda Impostazioni nel portale. Per altre informazioni, vedere [Configurare il tenant di Azure Active Directory usato da RemoteApp](remoteapp-changetenant.md).
- **Quali dispositivi e sistemi operativi sono supportati dalle applicazioni client?** Le applicazioni client sono disponibili per Windows 8.1, Windows 8, Windows 7 Service Pack 1, iOS, Mac OS X, Windows RT, dispositivi Android e Windows Phone. È supportata anche l'anteprima di Windows 10.
 
	[Scaricare](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) ora un client di RemoteApp.
- **Azure RemoteApp supporta i thin client?** Sì, sono supportati i thin client Windows Embedded seguenti:
	- Windows Embedded Standard 7 con Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **Quale versione di Windows Server è supportata per Host sessione Desktop remoto (RDSH)?** Windows Server 2012 R2.

##Supporto, commenti e suggerimenti

- **È possibile provare il servizio gratuitamente?** Sì. È disponibile una versione di valutazione gratuita della durata di 30 giorni. Al termine della valutazione, è possibile passare a un account a pagamento (che è possibile usare nell'ambiente di produzione) oppure interrompere l'uso del servizio. Avviare la versione di valutazione gratuita visitando [manage.windowsazure.com](http://manage.windowsazure.com) - creare una nuova istanza di RemoteApp. Con la versione di valutazione è possibile creare 2 istanze di RemoteApp con 10 utenti per istanza. Si noti che la versione di valutazione è valida soltanto per 30 giorni.
- **Qual è il piano di supporto per RemoteApp?** Il supporto per la fatturazione e la gestione delle sottoscrizioni viene fornito gratuitamente. Il supporto tecnico è disponibile tramite i [piani di servizio di Azure](../../../support/plans/). È anche possibile ottenere supporto gratuito dalla community tramite il [forum di discussione di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Quanto costa RemoteApp?** Consultare i [dettagli dei prezzi di Azure RemoteApp](../../../pricing/details/remoteapp/).
- **Come è possibile inviare commenti?** Visitare il [forum dei commenti](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **A chi è possibile rivolgersi per altre informazioni su Azure RemoteApp?** Oltre al [forum di discussione](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), nel quale è possibile porre domande, è possibile iscriversi al webinar settimanale [Ask the Experts](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), in cui vengono trattati tutti gli argomenti relativi a RemoteApp.
- **Qual è la documentazione disponibile per RemoteApp?** È un'ottima domanda. Oltre al contenuto della Guida nel pannello della Guida, disponibile nel portale (è sufficiente fare clic su **?** in qualsiasi pagina del portale), sono disponibili gli articoli seguenti con tutte le informazioni necessarie per imparare a usare RemoteApp:
	- **Per iniziare:**
		- [Che cos'è RemoteApp?](remoteapp-whatis.md)
		- [Cosa è incluso nelle immagini modello di RemoteApp?](remoteapp-images.md)
		- [Come funzionano le licenze?](remoteapp-licensing.md)
		- [Come funzionano RemoteApp e Office insieme?](remoteapp-o365.md)
		- [Come funziona il reindirizzamento in RemoteApp](remoteapp-redirection.md)?
	- **Distribuzione:**
		- [Creare un'immagine modello personalizzata](remoteapp-create-custom-image.md)
		- [Creare una raccolta ibrida](remoteapp-create-hybrid-deployment.md)
		- [Creare una raccolta nel cloud](remoteapp-create-cloud-deployment.md)
		- [Configurare Azure Active Directory per RemoteApp](remoteapp-ad.md)
		- [Pubblicare un'app in RemoteApp](remoteapp-publish.md)
	- **Gestione:**
		- [Aggiungere utenti](remoteapp-user.md)
		- [Procedure consigliate per la configurazione e l'uso di RemoteApp](remoteapp-bestpractices.md)	

	Video Sono anche disponibili diversi video su RemoteApp. Alcuni di questi forniscono un'introduzione ([Introduzione a Azure RemoteApp](http://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) mentre altri forniscono procedure dettagliate per le attività di distribuzione ([distribuzione cloud](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [distribuzione ibrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Si raccomanda di accedere a queste utili risorse.

 

<!---HONumber=August15_HO7-->