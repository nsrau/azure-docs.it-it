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
	ms.date="04/26/2016" 
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
-	[Come si ottiene il controllo delle versioni API con Gestione API?](#how-can-i-achieve-api-versioning-with-api-management)
-	[Come si configurano più ambienti di API, ad esempio sandbox e produzione?](#how-can-i-configure-multiple-environments-of-apis-for-example-sandbox-and-production)
-	[SOAP è supportato in Gestione API?](#is-soap-supported-in-api-management)
-	[L'indirizzo IP del gateway di Gestione API è costante? Può essere usato nelle regole del firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-	[È possibile configurare un server di autorizzazione OAUth 2.0 con la sicurezza ADFS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-	[Quale metodo di routing usa Gestione API quando è distribuita in più posizioni geografiche?](#what-routing-method-does-api-management-use-when-deployed-to-multiple-geographic-locations)



### Come porre una domanda al team di Gestione API?

-	È possibile inviare le domande al [forum MSDN di Gestione API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-	È anche possibile inviare un messaggio di posta elettronica all'indirizzo: `apimgmt@microsoft.com`.
-	Si può inviare una [richiesta di funzionalità](https://feedback.azure.com/forums/248703-api-management).

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
-	Creare la propria funzionalità di backup e ripristino usando l'[API REST di Gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx) per salvare e ripristinare entità specifiche dall'istanza del servizio.
-	Scaricare la configurazione del servizio tramite Git e ricaricarla in una nuova istanza. Per altre informazioni, vedere [Come salvare e configurare la configurazione del servizio Gestione API tramite Git](api-management-configuration-repository-git.md).

### È possibile gestire l'istanza di Gestione API a livello di codice?

Sì, è possibile gestire l'istanza usando l'[API REST di Gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx), l'[SDK della raccolta di gestione del servizio Gestione API di Microsoft Azure](http://aka.ms/apimsdk) e i cmdlet di PowerShell per la [distribuzione](https://msdn.microsoft.com/library/mt619282.aspx) e la [gestione dei servizi](https://msdn.microsoft.com/library/mt613507.aspx).

### Come aggiungere un utente al gruppo di amministratori?

Attualmente, gli amministratori sono limitati agli utenti che eseguono l'accesso come amministratori o coamministratori tramite il portale di Azure classico nella sottoscrizione di Azure che contiene l'istanza di Gestione API. Gli utenti creati nel portale di pubblicazione non possono essere designati come amministratori o aggiunti al gruppo di amministratori.


### Perché il criterio da aggiungere non è abilitato nell'editor dei criteri?

Se il criterio che si vuole aggiungere non è abilitato, verificare di essere nell'ambito corretto per il criterio. Ogni istruzione di criterio è progettata per essere usata in determinati ambiti e sezioni dei criteri. Per esaminare le sezioni dei criteri e gli ambiti di un criterio, vedere la sezione relativa all'**uso** del criterio nel [riferimento ai criteri](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### Come si ottiene il controllo delle versioni API con Gestione API?

-	Gestione API consente di configurare separatamente le API che rappresentano diverse versioni. Ad esempio, si possono configurare `MyAPI v1` e `MyAPI v2` come due API distinte e gli sviluppatori possono scegliere la versione che preferiscono usare.
-	È anche possibile configurare l'API con un URL del servizio che non include un segmento di versione, ad esempio: `https://my.api`. È quindi possibile configurare un segmento di versione per il modello [Riscrittura URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) di ogni operazione. Si può avere ad esempio un'operazione con un [modello di URL](api-management-howto-add-operations.md#url-template) di `/resource` e un modello [Riscrittura URL](api-management-howto-add-operations.md#rewrite-url-template) di `/v1/Resource`. In questo modo è possibile modificare il valore del segmento di versione in ogni operazione separatamente.
-	Per mantenere un segmento di versione "predefinito" nell'URL del servizio dell'API, in alcune operazioni è possibile specificare un criterio che usa l'[impostazione del servizio back-end](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) per modificare il percorso di richiesta del back-end.

### Come si configurano più ambienti di API, ad esempio sandbox e produzione?

Attualmente le opzioni disponibili sono:

-	È possibile ospitare API distinte nello stesso tenant
-	È possibile ospitare le stesse API in diversi tenant

### SOAP è supportato in Gestione API?

Attualmente, il supporto per SOAP è disponibile solo all'interno di Gestione API di Azure. La funzionalità è ancora in fase di analisi. A questo proposito, sarebbe molto interessante ricevere dai clienti esempi di WSDL con la descrizione delle funzionalità che ritengono necessarie. Per contattare Microsoft, usare le informazioni riportate nella sezione [Come porre una domanda al team di Gestione API?](#how-can-i-ask-a-question-to-the-api-management-team)

Se è necessario usare la funzionalità, vedere i suggerimenti di alcuni utenti delle community, ad esempio il [post relativo all'uso di un servizio WCF SOAP con HTTP nell'ambito di Gestione API di Azure](http://mostlydotnetdev.blogspot.com/2015/03/azure-api-management-apim-consuming.html).

Implementare la soluzione in questo modo richiede in parte la configurazione manuale dei criteri, non supporta l'importazione e l'esportazione di WSDL e gli utenti dovranno costituire il corpo delle richieste effettuate usando la console di test nel portale per sviluppatori.

### L'indirizzo IP del gateway di Gestione API è costante? Può essere usato nelle regole del firewall?

Nei livelli Standard e Premium l'indirizzo IP pubblico (VIP) del tenant di Gestione API è statico per la durata del tenant, con le eccezioni indicate di seguito. Si noti che ai tenant di livello Premium configurati per la distribuzione in più aree viene assegnato un indirizzo IP pubblico per ogni area.

L'indirizzo IP verrà modificato nelle circostanze seguenti:

-	Il servizio viene eliminato e ricreato
-	La sottoscrizione al servizio viene sospesa, ad esempio per mancato pagamento, e ripristinata
-	Viene aggiunta o rimossa una rete virtuale (supportata solo nel livello Premium)
-	L'indirizzo regionale viene modificato se l'area viene liberata e nuovamente immessa (distribuzione in più aree supportata solo nel livello Premium)

L'indirizzo IP (o gli indirizzi nel caso di una distribuzione in più aree) è reperibile nella pagina dei tenant nel portale classico di Azure.

### È possibile configurare un server di autorizzazione OAUth 2.0 con la sicurezza ADFS?

Per informazioni sulla configurazione di questo scenario, vedere [Uso di ADFS in Gestione API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### Quale metodo di routing usa Gestione API quando è distribuita in più posizioni geografiche? 

Gestione API usa il [metodo di routing del traffico delle prestazioni](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method). Il traffico in ingresso viene indirizzato al gateway API più vicino. Quando un'area diventa offline, il traffico in ingresso viene automaticamente indirizzato al gateway successivo più vicino. Per altre informazioni sui metodi di routing, vedere [Metodi di routing di Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).

<!---HONumber=AcomDC_0427_2016-->