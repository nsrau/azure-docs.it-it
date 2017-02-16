---
title: Domande frequenti su Gestione API di Azure | Microsoft Docs
description: Risposte alle domande comuni, modelli e procedure consigliate in Gestione API di Azure.
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: a0580f8d303c7ce33a65f0ce6faecf2492f851b0
ms.openlocfilehash: f4f3ee081d7c89fb5bc01552cb2e3ce01dd5381a

---
# <a name="azure-api-management-faqs"></a>Domande frequenti su Gestione API di Azure
Risposte alle domande comuni, modelli e procedure consigliate per Gestione API di Azure.

## <a name="contact-us"></a>Contatti
* [Come si rivolge una domanda al team di Gestione API di Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)


## <a name="frequently-asked-questions"></a>Domande frequenti
* [Che cosa significa se una funzionalità è in anteprima?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Come si protegge la connessione tra il gateway di Gestione API e i servizi back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Come si copia l'istanza del servizio Gestione API in una nuova istanza?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [È possibile gestire l'istanza di Gestione API a livello di codice?](#can-i-manage-my-api-management-instance-programmatically)
* [Come si aggiunge un utente al gruppo di amministratori?](#how-do-i-add-a-user-to-the-administrators-group)
* [Perché il criterio da aggiungere non è disponibile nell'editor dei criteri?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Come si usa il controllo delle versioni API in Gestione API?](#how-do-i-use-api-versioning-in-api-management)
* [Come si configurano più ambienti in una sola API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [È possibile usare SOAP con Gestione API?](#can-i-use-soap-with-api-management)
* [L'indirizzo IP del gateway di Gestione API è costante? Può essere usato nelle regole del firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [È possibile configurare un server di autorizzazione OAuth 2.0 con la sicurezza AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [Quale metodo di routing usa Gestione API nelle distribuzioni in più posizioni geografiche?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [È possibile usare un modello di Azure Resource Manager per creare un'istanza del servizio Gestione API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [È possibile usare un certificato SSL autofirmato per un back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Perché si ottiene un errore di autenticazione quando si cerca di clonare un repository GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Gestione API funziona con ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [È possibile spostare un servizio Gestione API da una sottoscrizione a un'altra?](#can-i-move-an-api-management-service-from-one-subscription-to-another)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Come si rivolge una domanda al team di Gestione API di Microsoft Azure?
È possibile contattare Microsoft in uno dei modi seguenti:

* Pubblicare le domande sul [forum MSDN di Gestione API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Inviare un messaggio di posta elettronica all'indirizzo <mailto:apimgmt@microsoft.com>.
* Inviare una richiesta di funzionalità nel [forum dei commenti e suggerimenti su Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Che cosa significa se una funzionalità è in anteprima?
Quando una funzionalità è in anteprima, significa che si stanno raccogliendo attivamente commenti e suggerimenti dagli utenti sulla funzionalità. Una funzionalità in anteprima è completa dal punto di vista funzionale, ma è possibile che venga apportata una modifica di rilievo in risposta ai commenti e suggerimenti dei clienti. È consigliabile non far dipendere l'ambiente di produzione da una funzionalità in anteprima. Per inviare commenti e suggerimenti sulle funzionalità in anteprima, usare una delle opzioni di contatto elencate in [Come si rivolge una domanda al team di Gestione API di Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Come si protegge la connessione tra il gateway di Gestione API e i servizi back-end?
Esistono diverse opzioni per proteggere la connessione tra il gateway di Gestione API e i servizi back-end. È possibile:

* Usare l'autenticazione HTTP di base. Per altre informazioni, vedere [Configurare le impostazioni API](api-management-howto-create-apis.md#configure-api-settings).
* Usare l'autenticazione reciproca SSL come descritto in [Come proteggere i servizi back-end usando l'autenticazione con certificati client in Gestione API di Azure](api-management-howto-mutual-certificates.md).
* Usare gli elenchi di IP consentiti nel servizio back-end. Se si usa un'istanza di Gestione API di livello Standard o Premium, l'indirizzo IP del gateway rimane costante. È possibile impostare l'elenco elementi consentiti per autorizzare questo indirizzo IP. È possibile ottenere l'indirizzo IP dell'istanza di Gestione API nel dashboard del portale di Azure.
* Connettere l'istanza di Gestione API a una rete virtuale di Azure. Per altre informazioni, vedere [Come configurare connessioni VPN in Gestione API di Azure](api-management-howto-setup-vpn.md).

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Come si copia l'istanza del servizio Gestione API in una nuova istanza?
Esistono diverse opzioni per copiare un'istanza di Gestione API in una nuova istanza. È possibile:

* Usare la funzione di backup e ripristino di Gestione API. Per altre informazioni, vedere [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](api-management-howto-disaster-recovery-backup-restore.md).
* Creare una propria funzionalità di backup e ripristino usando l'[API REST di Gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Usare l'API REST per salvare e ripristinare le entità dall'istanza del servizio desiderata.
* Scaricare la configurazione del servizio usando Git e quindi caricarla in una nuova istanza. Per altre informazioni, vedere [Come salvare e configurare la configurazione del servizio Gestione API tramite Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>È possibile gestire l'istanza di Gestione API a livello di codice?
Sì, è possibile gestire Gestione API a livello di codice usando:

* L'[API REST di Gestione API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* [Microsoft Azure ApiManagement Service Management Library SDK](http://aka.ms/apimsdk).
* I cmdlet di PowerShell per la [distribuzione del servizio](https://msdn.microsoft.com/library/mt619282.aspx) e per la [gestione del servizio](https://msdn.microsoft.com/library/mt613507.aspx).

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Come si aggiunge un utente al gruppo di amministratori?
Ecco come è possibile aggiungere un utente al gruppo di amministratori:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al gruppo di risorse con l'istanza di Gestione API che si vuole aggiornare.
3. In Gestione API assegnare il ruolo **Api Management Contributor** (Collaboratore Gestione API) all'utente.

Ora il nuovo collaboratore aggiunto può usare i [cmdlet](https://msdn.microsoft.com/library/mt613507.aspx) di Azure PowerShell. Ecco come accedere come amministratore:

1. Usare il cmdlet `Login-AzureRmAccount` per accedere.
2. Impostare il contesto sulla sottoscrizione che contiene il servizio usando `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Ottenere un l'URL Single Sign-On usando `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Usare l'URL per accedere al portale di amministrazione.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Perché il criterio da aggiungere non è disponibile nell'editor dei criteri?
Se il criterio che si vuole aggiungere è in grigio o ombreggiato nell'editor dei criteri, assicurarsi che l'ambito del criterio sia corretto. Ogni istruzione di criterio è progettata per essere usata in ambiti e sezioni dei criteri specifici. Per esaminare le sezioni dei criteri e gli ambiti di un criterio, vedere la sezione sull'utilizzo in [API Management policies](https://msdn.microsoft.com/library/azure/dn894080.aspx) (Criteri di Gestione API).

### <a name="how-do-i-use-api-versioning-in-api-management"></a>Come si usa il controllo delle versioni API in Gestione API?
Esistono alcune opzioni per usare il controllo delle versioni API in Gestione API:

* In Gestione API è possibile configurare le API per rappresentare versioni diverse. Ad esempio, se esistono due API diverse, MyAPIv1 e MyAPIv2, uno sviluppatore può scegliere la versione che vuole usare.
* È anche possibile configurare l'API con un URL del servizio che non include un segmento di versione, ad esempio https://my.api. Configurare quindi un segmento di versione nel modello di [URL di riscrittura](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) di ogni operazione. Ad esempio, è possibile avere un'operazione con un [modello di URL](api-management-howto-add-operations.md#url-template) denominato /resource e un modello di [URL di riscrittura](api-management-howto-add-operations.md#rewrite-url-template) denominato /v1/Resource. È possibile modificare il valore del segmento di versione separatamente per ogni operazione.
* Per mantenere un segmento di versione "predefinito" nell'URL del servizio dell'API, in alcune operazioni specificare un criterio che usa l'[impostazione del servizio back-end](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) per modificare il percorso di richiesta del back-end.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Come si configurano più ambienti in una sola API?
Per configurare più ambienti, ad esempio un ambiente di test e un ambiente di produzione, in una sola API, esistono due opzioni. È possibile:

* Ospitare API diverse nello stesso tenant.
* Ospitare le stesse API in tenant diversi.

### <a name="can-i-use-soap-with-api-management"></a>È possibile usare SOAP con Gestione API?
Ora è disponibile il supporto per il [pass-through SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/). Gli amministratori possono importare il file WSDL del servizio SOAP e Gestione API di Azure creerà un front-end SOAP. Per i servizi SOAP sono disponibili la documentazione del portale per sviluppatori, la console di test, i criteri e l'analisi.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>L'indirizzo IP del gateway di Gestione API è costante? Può essere usato nelle regole del firewall?
Con i piani Standard e Premium, l'indirizzo IP (indirizzo VIP) pubblico del tenant di Gestione API è statico per la durata del tenant, con alcune eccezioni. L'indirizzo IP viene modificato in queste circostanze:

* Il servizio viene eliminato e quindi ricreato.
* La sottoscrizione al servizio viene sospesa, ad esempio per mancato pagamento, e quindi ripristinata.
* Si aggiunge o si rimuove la rete virtuale di Azure. È possibile usare la rete virtuale solo con il piano Premium.

Per le distribuzioni in più aree, l'indirizzo dell'area viene modificato se l'area viene liberata e quindi reintegrata. È possibile usare la distribuzione in più aree solo con il piano Premium.

Ai tenant nel piano Premium configurati per la distribuzione in più aree viene assegnato un indirizzo IP pubblico per ogni area.

È possibile ottenere l'indirizzo IP (o gli indirizzi nel caso di una distribuzione in più aree) nella pagina dei tenant nel portale di Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>È possibile configurare un server di autorizzazione OAuth 2.0 con la sicurezza AD FS?
Per informazioni su come configurare un server di autorizzazione OAuth 2.0 con la sicurezza di Active Directory Federation Services (AD FS), vedere [Using ADFS in API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/) (Uso di AD FS in Gestione API).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Quale metodo di routing usa Gestione API nelle distribuzioni in più posizioni geografiche?
Gestione API usa il [metodo di routing del traffico relativo alle prestazioni](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method) nelle distribuzioni in più posizioni geografiche. Il traffico in ingresso viene instradato al gateway API più vicino. Quando un'area diventa offline, il traffico in ingresso viene automaticamente indirizzato al gateway successivo più vicino. Altre informazioni sui metodi di routing sono disponibili in [Metodi di routing di Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>È possibile usare un modello di Azure Resource Manager per creare un'istanza del servizio Gestione API?
Sì. Vedere i modelli di avvio rapido del [Servizio Gestione API di Azure](http://aka.ms/apimtemplate).

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>È possibile usare un certificato SSL autofirmato per un back-end?
Sì. Ecco come usare un certificato autofirmato Secure Sockets Layer (SSL) per un back-end:

1. Creare un'entità di [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) usando Gestione API.
2. Impostare la proprietà **skipCertificateChainValidation** su **true**.
3. Se non si vuole più consentire certificati autofirmati, eliminare l'entità di back-end o impostare la proprietà **skipCertificateChainValidation** su **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Perché si ottiene un errore di autenticazione quando si cerca di clonare un repository GIT?
Se si usa Git Credential Manager o se si vuole clonare un repository Git usando Visual Studio, potrebbe verificarsi un problema noto con la finestra di dialogo Credenziali di Windows. Nella finestra di dialogo il limite per la lunghezza della password è di 127 caratteri e la password generata da Microsoft viene troncata. Microsoft sta lavorando per abbreviare la password. Per ora, per la clonazione del repository Git, usare GIT Bash.

### <a name="does-api-management-work-with-azure-expressroute"></a>Gestione API funziona con ExpressRoute?
Sì. Gestione API funziona con ExpressRoute.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>È possibile spostare un servizio Gestione API da una sottoscrizione a un'altra?
Sì. Per informazioni, vedere [Move resources to a new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione).




<!--HONumber=Dec16_HO3-->


