<properties title="Azure RemoteApp FAQ" pageTitle="Domande frequenti su Azure RemoteApp" description="Informazioni su Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Domande frequenti su Azure RemoteApp
Finora sono state formulate le domande seguenti su Azure RemoteApp. Ve ne sono altre? Visitare il [forum RemoteApp](https://social.msdn.microsoft.com/Forums/azure/it-it/home?forum=AzureRemoteApp) per formulare le proprie.

##Che cos'è Azure RemoteApp?##


- **Che cos'è Azure RemoteApp?** RemoteApp è un servizio di Azure che offre le funzionalità di Microsoft RemoteApp in locale, con il supporto di Servizi Desktop remoto, da Azure. RemoteApp consente di fornire accesso remoto sicuro alle applicazioni da molti dispositivi utente diversi. Altre informazioni su [Azure RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-whatis/).
- **Quali sono le due opzioni di distribuzione disponibili?** Sono disponibili due tipi di distribuzioni (o raccolte) di RemoteApp: ibrido e cloud. Scoprire quale [opzione di distribuzione](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-whatis/) è la più adatta per la propria organizzazione.

##Configurazioni supportate ##


- **Le applicazioni personalizzate line-of-business (LOB) sono supportate?** Sì. Per usare un'applicazione personalizzata in Azure RemoteApp, creare un'[immagine modello personalizzata](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-custom-image/) e caricarla nella raccolta RemoteApp.
- **L'applicazione LOB personalizzata funzionerà in Azure RemoteApp?** Il modo migliore per comprendere il prodotto è testarlo. Esaminare i [requisiti di compatibilità con le applicazioni](http://www.microsoft.com/it-it/download/details.aspx?id=18704) e visitare il [centro di compatibilità di Desktop remoto](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Quale metodo di distribuzione (cloud o ibrido) è più adatto per la propria organizzazione?** Le raccolte ibride offrono un'esperienza più completa, se si vuole l'integrazione completa con single sign-on (SSO) e una connettività sicura per la rete locale. Le raccolte nel cloud consentono di isolare la distribuzione in modo semplice e flessibile usando più metodi di autenticazione. Altre informazioni sulle [opzioni di distribuzione](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-whatis/).
- **La raccolta ibrida richiede una rete virtuale. È possibile usare la rete esistente?** Non allo stato attuale, ma Microsoft è al corrente del fatto che gli utenti vogliono che venga offerta questa funzionalità. In attesa di una soluzione, è possibile connettere la rete virtuale esistente alla rete virtuale Azure RemoteApp seguendo [queste istruzioni](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx).
- **È possibile usare una macchina virtuale esistente o nel cloud come modello per la raccolta RemoteApp?** Non allo stato attuale, ma si tratta di una richiesta frequente sul [sito dedicato ai commenti e ai suggerimenti](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w).
- **Al momento si usa SQL o un altro database, in locale o in Azure. Che tipo di distribuzione è consigliabile usare?** Questo dipende dal luogo in cui risiede il database SQL o back-end. Se si trova in una rete privata, usare la raccolta ibrida. Se invece il database è esposto a Internet e consente connessioni client, è possibile usare la raccolta nel cloud.
- **Sono disponibili il mapping delle unità, la gestione delle porte USB e seriale, la condivisione degli Appunti e il reindirizzamento della stampante?** Tutte queste funzionalità sono supportate in Azure RemoteApp. Il reindirizzamento della stampante e la condivisione degli Appunti sono abilitati per impostazione predefinita. [È possibile contattare Microsoft](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20enable%20Redirection%20request) per abilitare il mapping di unità o il reindirizzamento della porta USB o di quella seriale (l'aggiunta di questo supporto nel portale è in corso, ma non ancora disponibile).
- **Per quanto riguarda l'autenticazione, quali metodi sono supportati?** La raccolta nel cloud supporta account Microsoft e account Azure Active Directory, che sono anche account di Office 365. La raccolta ibrida supporta solo gli account Azure Active Directory che sono stati sincronizzati (mediante uno strumento come DirSync) da una distribuzione di Windows Server Active Directory, e, più in particolare, sincronizzati con l'opzione di sincronizzazione password oppure con la federazione di Active Directory Federation Services (ADFS) configurata. È anche possibile configurare [l'autenticazione a più fattori (MFA)](http://azure.microsoft.com/it-it/documentation/services/multi-factor-authentication/).

	**Nota:** gli utenti di Azure Active Directory devono essere inclusi nel tenant associato alla sottoscrizione. È possibile visualizzare e modificare la sottoscrizione nella scheda **Impostazioni** del portale. Per altre informazioni, vedere l'articolo relativo alla [modifica del tenant di Azure Active Directory usato da RemoteApp](http://msdn.microsoft.com/it-it/3d6c4fd1-c981-4c57-9402-59fe31b11883).

- **Perché non è possibile fornire accesso all'account Azure Active Directory?** Gli utenti di Azure Active Directory devono essere nella directory associata alla sottoscrizione. È possibile visualizzare o modificare tale directory nella scheda Impostazioni nel portale. Per altre informazioni, vedere [Modifica del tenant di Azure Active Directory usato da RemoteApp](http://msdn.microsoft.com/it-it/3d6c4fd1-c981-4c57-9402-59fe31b11883).
- **Quali dispositivi e sistemi operativi sono supportati dalle applicazioni client?** Le applicazioni client sono disponibili per Windows 8.1, Windows 8, Windows 7 Service Pack 1, iOS, Mac OS X, Windows RT, dispositivi Android e Windows Phone. È inoltre supportata l'anteprima di Windows 10.
 
	[Scaricare](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) ora un client di RemoteApp.
- **Azure RemoteApp supporta i thin client?** Sì, sono supportati i thin client Windows Embedded indicati di seguito:
	- Windows Embedded Standard 7 con Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **Quale versione di Windows Server è supportata per Host sessione Desktop remoto (RDSH)?** Windows Server 2012 R2.

##Supporto, commenti e suggerimenti

- **È possibile provare gratuitamente questo servizio?** Sì. Per 30 giorni è disponibile una versione di valutazione. Al termine di tale valutazione, è possibile passare a un account a pagamento (che può essere usato in produzione) oppure interrompere l'uso del servizio. Avviare la valutazione gratuita visitando [manage.windowsazure.com](http://manage.windowsazure.com) - creare una nuova istanza di RemoteApp. Con la versione di valutazione, è possibile compilare 2 istanze di RemoteApp con 10 utenti per ogni istanza. Tenere presente che la versione di valutazione avrà una durata di soli 30 giorni.
- **Che cos'è il piano di supporto per RemoteApp?** Il supporto per la gestione della fatturazione e delle sottoscrizioni è offerto gratuitamente. Il supporto tecnico è disponibile tramite i [piani di servizio di Azure](http://azure.microsoft.com/support/plans/). È anche possibile ottenere supporto della community tramite il [forum di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/it-it/home?forum=AzureRemoteApp).
- **Quanto costa RemoteApp?** Vedere la pagina dedicata ai [dettagli dei prezzi di Azure RemoteApp ](http://azure.microsoft.com/it-it/pricing/details/remoteapp/).
- **Come è possibile inviare commenti e suggerimenti?** Visitare l'[area dedicata ai commenti e ai suggerimenti](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **A è possibile rivolgersi per altre informazioni su Azure RemoteApp?** Oltre al [forum di discussione](http://social.msdn.microsoft.com/Forums/windowsazure/it-it/home?forum=AzureRemoteApp), nel quale è possibile porre domande, è possibile iscriversi al webinar settimanale [Ask the Experts](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), in cui vengono trattati tutti gli argomenti relativi a RemoteApp.
- **Qual è la documentazione disponibile per RemoteApp?** È un'ottima domanda. Oltre al contenuto della Guida nel pannello della Guida, disponibile nel portale (è sufficiente fare clic su **?** in qualsiasi pagina del portale), sono anche disponibili gli articoli seguenti con tutte le informazioni necessarie per imparare a usare RemoteApp:
	- **Informazioni di base:**
		- [Che cos'è RemoteApp?](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-whatis/)
		- [Che cosa sono le immagini modello di RemoteApp?](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-images/)
		- [Come funzionano le licenze?](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-licensing/)
		- [Come funzionano RemoteApp e Office insieme?](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-o365/)
	- **Distribuzione:**
		- [Creare un'immagine modello personalizzata](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-custom-image/)
		- [Creare una raccolta ibrida](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [Creare una raccolta nel cloud](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-create-cloud-deployment/)
		- [Configurare Azure Active Directory per RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-ad/)
		- [Pubblicare un'app in RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-publish/)
	- **Gestione:**
		- [Aggiungere utenti](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-user/)
		- [Procedure consigliate per la configurazione e l'uso di RemoteApp](http://azure.microsoft.com/it-it/documentation/articles/remoteapp-bestpractices/)	

	Video Sono anche disponibili diversi video su RemoteApp. Alcuni di questi forniscono un'introduzione ([Introduzione a Azure RemoteApp](http://azure.microsoft.com/it-it/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) mentre altri forniscono procedure dettagliate per le attività di distribuzione ([distribuzione cloud](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [distribuzione ibrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Si raccomanda di accedere a queste utili risorse.


<!--HONumber=35.2-->
