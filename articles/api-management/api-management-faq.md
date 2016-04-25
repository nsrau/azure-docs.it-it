<properties 
	pageTitle="Domande frequenti su Gestione API di Azure | Microsoft Azure" 
	description="Risposte alle domande più comuni, modelli e procedure consigliate per Gestione API di Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
	ms.author="sdanie"/>

# Domande frequenti su Gestione API di Azure

Risposte alle domande più comuni, modelli e procedure consigliate per Gestione API di Azure.

## Domande frequenti

-	[Come porre una domanda al team di Gestione API?](#how-can-i-ask-a-question-to-the-api-management-team)
-	[Che cosa significa se una funzionalità è disponibile in anteprima?](#what-does-it-mean-if-a-feature-is-in-preview)
-	[Quali sono le opzioni supportate per proteggere la connessione tra il gateway di Gestione API e i servizi back-end?](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[Come copiare un'istanza di Gestione API in una nuova istanza?](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[È possibile gestire l'istanza di Gestione API a livello di codice?](#can-i-manage-my-api-management-instance-programmatically)
-	[Come aggiungere un utente al gruppo di amministratori?](#how-can-i-add-a-user-to-the-administrators-group)
-	[Perché il criterio da aggiungere non è abilitato nell'editor dei criteri?](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)

### Come porre una domanda al team di Gestione API?

-	È possibile inviare le domande nel [forum MSDN di Gestione API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-	È anche possibile inviare un messaggio di posta elettronica all'indirizzo: `apimgmt@microsoft.com`.

### Che cosa significa se una funzionalità è disponibile in anteprima?

Completata dal punto di vista funzionale, questa funzionalità è disponibile in anteprima perché ancora in attesa di commenti e suggerimenti. Poiché è possibile che vengano apportate modifiche sostanziali basate sui suggerimenti dei clienti, si consiglia di non dipendere dall'uso di questa funzionalità negli ambienti di produzione. Se si hanno commenti e suggerimenti sulle funzionalità di anteprima, inviarli usando uno dei metodi descritti in [Come porre una domanda al team di Gestione API?](#how-can-i-ask-a-question-to-the-api-management-team).

### Quali sono le opzioni supportate per proteggere la connessione tra il gateway di Gestione API e i servizi back-end?

Esistono diverse opzioni supportate.

1. Usare l'autenticazione HTTP di base. Per altre informazioni, vedere [Configurare le impostazioni API](api-management-howto-create-apis.md#configure-api-settings).
2. Usare l'autenticazione reciproca SSL come descritto in [Come proteggere i servizi back-end usando l'autenticazione con certificati client in Gestione API di Azure](api-management-howto-mutual-certificates.md).
3. Usare gli elenchi di IP consentiti nel servizio back-end. Se si usa un'istanza di Gestione API del piano tariffario Standard o Premium, l'indirizzo IP del gateway rimane costante ed è possibile configurare l'elenco degli IP consentiti per autorizzare questo indirizzo IP. È possibile recuperare l'indirizzo IP dell'istanza di Gestione API nel **Dashboard** del portale di Azure classico.
4. È possibile connettere l'istanza di Gestione API a una rete virtuale di Azure (classico). Per altre informazioni, vedere [Come configurare connessioni VPN in Gestione API di Azure](api-management-howto-setup-vpn.md).

### Come copiare un'istanza di Gestione API in una nuova istanza?

Sono disponibili diverse opzioni che è possibile usare per copiare un'istanza del servizio Gestione API in una nuova istanza.

-	Usare la funzionalità di backup e ripristino di Gestione API. Per altre informazioni, vedere [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](api-management-howto-disaster-recovery-backup-restore.md).
-	Creare la propria funzionalità di backup e ripristino usando l'[API REST di Gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx) per salvare e ripristinare le entità desiderate dall'istanza del servizio.
-	Scaricare la configurazione del servizio tramite Git e ricaricarla in una nuova istanza. Per altre informazioni, vedere [Come salvare e configurare la configurazione del servizio Gestione API tramite Git](api-management-configuration-repository-git.md).

### È possibile gestire l'istanza di Gestione API a livello di codice?

Sì, è possibile gestire l'istanza usando l'[API REST di Gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx) e i cmdlet di PowerShell per la[distribuzione](https://msdn.microsoft.com/library/mt619282.aspx) e la [gestione dei servizi](https://msdn.microsoft.com/library/mt613507.aspx).

### Come aggiungere un utente al gruppo di amministratori?

Attualmente, gli amministratori sono limitati agli utenti che eseguono l'accesso come amministratori o coamministratori tramite il portale di Azure classico nella sottoscrizione di Azure che contiene l'istanza di Gestione API. Gli utenti creati nel portale di pubblicazione non possono essere designati come amministratori o aggiunti al gruppo di amministratori.


### Perché il criterio da aggiungere non è abilitato nell'editor dei criteri?

Se il criterio che si vuole aggiungere non è abilitato, verificare di essere nell'ambito corretto per il criterio. Ogni istruzione di criterio è progettata per essere usata in determinati ambiti e sezioni dei criteri. Per esaminare le sezioni dei criteri e gli ambiti di un criterio, controllare la sezione relativa all'**utilizzo** del criterio nel [riferimento criteri](https://msdn.microsoft.com/library/azure/dn894080.aspx).

<!---HONumber=AcomDC_0413_2016-->