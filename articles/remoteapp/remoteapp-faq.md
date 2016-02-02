<properties 
	pageTitle="Domande frequenti su RemoteApp di Azure | Microsoft Azure" 
	description="Vedere le risposte alle domande più frequenti su RemoteApp di Azure." 
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
	ms.date="10/23/2015" 
	ms.author="elizapo"/>

# Domande frequenti su Azure RemoteApp
Finora sono state formulate le domande seguenti su Azure RemoteApp. Ve ne sono altre? Visitare i [forum di RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) per formulare le proprie domande oppure inserire un commento di seguito.

## Informazioni su Azure RemoteApp ##


- **Che cos'è Azure RemoteApp?** RemoteApp è un servizio di Azure che consente di fornire un accesso remoto sicuro alle applicazioni da molti dispositivi utente diversi. Altre informazioni su [Azure RemoteApp](remoteapp-whatis.md).
- **Quali sono le opzioni di distribuzione?** Sono disponibili due tipi di distribuzioni di RemoteApp: ibrida e nel cloud. Quale è necessario dipende da numerosi fattori, ad esempio la necessità di aggiunta a un dominio. Tutte queste decisioni sono discusse [qui](remoteapp-collections.md).

## Suggerimenti rapidi sull'uso di Azure RemoteApp ##
- **Dopo quanto tempo si viene disconnessi? Per quanto tempo è possibile rimanere inattivi prima che venga eseguito automaticamente l'avvio?** 4 ore. Se un utente rimane inattivo per 4 ore, viene disconnesso automaticamente da Azure RemoteApp. Per verificare le altre impostazioni predefinite, vedere [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](azure-subscription-service-limits.md).
- **È possibile provare il servizio gratuitamente?** Sì. È disponibile una versione di valutazione gratuita della durata di 30 giorni. Al termine della valutazione, è possibile passare a un account a pagamento (che è possibile usare nell'ambiente di produzione) oppure interrompere l'uso del servizio. Avviare la versione di valutazione gratuita visitando [manage.windowsazure.com](http://manage.windowsazure.com) - creare una nuova istanza di RemoteApp. Con la versione di valutazione è possibile creare 2 istanze di RemoteApp con 10 utenti per istanza. Si noti che la versione di valutazione è valida soltanto per 30 giorni.
## Dettagli della sottoscrizione Azure RemoteApp ##

- **Quali sono i limiti del servizio?** Sarà possibile conoscere le impostazioni predefinite e i limiti di servizio di Azure RemoteApp in [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](azure-subscription-service-limits.md). Per altre domande contattare Microsoft.
- **Quanti utenti sono necessari?** È necessario un numero minimo di 20 utenti. Il numero MINIMO è 20. Verrà emessa una fattura per 20. 
- **Quanto costa RemoteApp?** Consultare i [dettagli dei prezzi di Azure RemoteApp](../../../pricing/details/remoteapp/).
- **Un tipo di raccolta costa più di un altro?** Sì, è possibile, a seconda delle esigenze di raccolta. Una raccolta ibrida richiede una connessione da RemoteApp di Azure alla rete locale. Se si utilizza una Route di rete virtuale/Express esistente, non esiste alcun costo aggiuntivo. Se invece si usa una nuova rete virtuale di Azure e un gateway o Express Route, verrà addebitato un costo per il [gateway VPN](../../../pricing/details/vpn-gateway) o per [Express Route](../../../pricing/details/expressroute/). Questo costo (vedere informazioni dettagliato sui collegamenti) è sopra il costo mensile della RemoteApp di Azure.

## Raccolte - cos'è supportato, cosa è opportuno utilizzare e altro
- **Le applicazioni personalizzate LOB (line-of-business) sono supportate?** Sì. Per usare un'applicazione personalizzata in Azure RemoteApp, creare un'[immagine modello personalizzata](remoteapp-create-custom-image.md) e caricarla nella raccolta RemoteApp.
- **L'applicazione LOB personalizzata funzionerà in Azure RemoteApp?** Il modo migliore per verificarlo è provarla. Verificare i [requisiti di compatibilità dell'applicazione](http://www.microsoft.com/download/details.aspx?id=18704) e il [centro di compatibilità di Desktop remoto](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Quale metodo di distribuzione (cloud o ibrido) è più adatto per la propria organizzazione?** Le raccolte ibride offrono un'esperienza più completa se si desidera integrazione completa con Single Sign-On (SSO) e connettività di rete protetta in locale. Le raccolte nel cloud offrono un modo semplice e rapido di isolare la distribuzione usando più metodi di autenticazione. Altre informazioni sulle [opzioni di distribuzione](remoteapp-whatis.md).
- **Al momento si usa SQL o un altro database, in locale o in Azure. Che tipo di distribuzione è consigliabile usare?** Questo dipende dal luogo in cui risiede il database SQL o back-end. Se si trova in una rete privata, usare la raccolta ibrida. Se invece il database è esposto a Internet e consente connessioni client, è possibile usare la raccolta nel cloud.
- **E per quanto riguarda il mapping delle unità, le porte USB e seriali, la condivisione degli Appunti e il reindirizzamento della stampante?** Tutte queste funzionalità sono supportate in Azure RemoteApp. Il reindirizzamento della stampante e la condivisione degli Appunti sono abilitati per impostazione predefinita. Altre informazioni sul reindirizzamento sono disponibili [qui](remoteapp-redirection.md). 


## Immagini modello
- **È possibile usare un cloud o una macchina virtuale esistente come modello per la propria raccolta RemoteApp?** Sì. È possibile creare un'immagine basata su una macchina virtuale di Azure, usare una delle immagini incluse nella sottoscrizione o o creare un'immagine personalizzata. Verificare le [opzioni per le immagini di RemoteApp](remoteapp-imageoptions.md).


## Opzioni di rete
- **La raccolta ibrida richiede una rete virtuale. È possibile usare la rete virtuale esistente?** È possibile se la rete virtuale esistente è una rete virtuale di Azure. Per altre informazioni vedere "Passaggio 1: Configurare la rete virtuale" nelle [istruzioni sulla raccolta ibrida](remoteapp-create-hybrid-deployment.md).
- **È possibile utilizzare una rete virtuale con una raccolta di cloud?** È possibile. Per altre informazioni, vedere [Creare una raccolta cloud](remoteapp-create-cloud-deployment.md), in particolare il passaggio 1.

## Opzioni di autenticazione



- **Per quanto riguarda l'autenticazione, quali metodi sono supportati?** La raccolta nel cloud supporta account Microsoft e account Azure Active Directory, che sono anche account di Office 365. La raccolta ibrida supporta solo gli account Azure Active Directory che sono stati sincronizzati (mediante uno strumento come [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) da una distribuzione di Windows Server Active Directory e, in particolare, sincronizzati con l'opzione di sincronizzazione password oppure con la federazione di Active Directory Federation Services (ADFS) configurata. È anche possibile configurare l'autenticazione a più fattori [(MFA, Multi-Factor Authentication)](../../services/multi-factor-authentication/).

>[AZURE.NOTE]Gli utenti di Azure Active Directory devono provenire dal tenant associato alla sottoscrizione (è possibile visualizzare e modificare la sottoscrizione nella scheda **Impostazioni** nel portale. Per altre informazioni, vedere l'articolo relativo alla [modifica del tenant di Azure Active Directory usato da RemoteApp](remoteapp-changetenant.md)).

- **Perché non è possibile consentire l'accesso all'account Azure Active Directory?** Gli utenti di Azure Active Directory devono provenire dalla directory associata alla sottoscrizione. È possibile visualizzare o modificare tale directory nella scheda Impostazioni nel portale. Per altre informazioni, vedere [Configurare il tenant di Azure Active Directory usato da RemoteApp](remoteapp-changetenant.md).

## Client - quali dispositivi si possono utilizzare per accedere a RemoteApp di Azure?
È possibile trovare informazioni interessanti sui client, inclusi i passaggi per l'installazione di diversi client, in [Accesso alle app in Azure RemoteApp](remoteapp-clients.md).

- **Quali dispositivi e sistemi operativi sono supportati dalle applicazioni client?** Per prima cosa, computer e tablet: 
	- Windows 10 (anteprima client)
	- Windows 8.1 e Windows 8
	- Windows 7 Service Pack 1
	- Mac OS X
	- Windows RT
	- Tablet Android
	- iPad e i telefoni:
	- iPhone
	- Telefono Android
	- Windows Phone
 
	[Scaricare](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) ora un client di RemoteApp.
- **Azure RemoteApp supporta i thin client?** Sì, sono supportati i thin client Windows Embedded seguenti:
	- Windows Embedded Standard 7
	- Windows Embedded 8 Standard
	- Windows Embedded 8.1 Industry Pro
	- Windows 10 IoT Enterprise

- **Quale versione di Windows Server è supportata per Host sessione Desktop remoto (RDSH)?** Windows Server 2012 R2.

##Supporto, commenti e suggerimenti


- **Qual è il piano di supporto per RemoteApp?** Il supporto per la fatturazione e la gestione delle sottoscrizioni viene fornito gratuitamente. Il supporto tecnico è disponibile tramite i [piani di servizio di Azure](../../../support/plans/). È anche possibile ottenere supporto gratuito dalla community tramite il [forum di discussione di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Come è possibile inviare commenti?** Visitare il [forum dei commenti](https://feedback.azure.com/forums/247748-azure-remoteapp/).
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

	Video Sono anche disponibili diversi video su RemoteApp. Alcuni di questi forniscono un'introduzione ([Introduzione a Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) mentre altri forniscono procedure dettagliate per le attività di distribuzione ([distribuzione cloud](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [distribuzione ibrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Si raccomanda di accedere a queste utili risorse.

 
### Come contribuire al miglioramento 
Non tutti sanno che oltre alla classificazione di questo articolo e all'aggiunta di commenti di seguito, è possibile apportare modifiche all'articolo stesso. Mancano informazioni? Alcune informazioni non sono corrette? Qualcosa non è abbastanza chiaro? Scorrere verso l'alto e fare clic su **Modifica in GitHub** per apportare modifiche. Dopo che saranno state esaminate e approvate, le modifiche e i miglioramenti suggeriti dagli utenti saranno applicati all'articolo.

<!---HONumber=AcomDC_0128_2016-->