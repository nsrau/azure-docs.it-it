---
title: Procedure consigliate per le distribuzioni PaaS sicure-Microsoft Azure
description: Informazioni sulle procedure consigliate per la progettazione, la compilazione e la gestione di applicazioni cloud sicure in Azure e per comprendere i vantaggi della sicurezza di PaaS rispetto ad altri modelli di servizio cloud.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: ddcf5a1df31b4b36e25b2522ada21deab19fe032
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159884"
---
# <a name="securing-paas-deployments"></a>Protezione delle distribuzioni PaaS

In questo articolo vengono fornite informazioni che consentono di:

- Comprendere i vantaggi di sicurezza dell'hosting delle applicazioni nel cloud
- Valutare i vantaggi di sicurezza del modello di piattaforma distribuita come servizio (PaaS, Platform as a Service) rispetto ad altri modelli di servizio cloud
- Modificare l'attenzione verso la sicurezza, da un approccio di sicurezza del perimetro incentrato sulla rete a uno incentrato sulle identità
- Implementare le procedure consigliate per la sicurezza del modello PaaS

[Lo sviluppo di applicazioni sicure in Azure](abstract-develop-secure-apps.md) è una guida generale alle domande e ai controlli di sicurezza da considerare in ogni fase del ciclo di vita di sviluppo del software durante lo sviluppo di applicazioni per il cloud.

## <a name="cloud-security-advantages"></a>Vantaggi della sicurezza cloud
È importante comprendere la [divisione delle responsabilità](shared-responsibility.md) tra l'utente e Microsoft. In locale, si è proprietari dell'intero stack ma, con lo spostamento nel cloud, alcune responsabilità vengono trasferite a Microsoft.

[Il cloud offre vantaggi per la sicurezza](shared-responsibility.md#cloud security advantages). In un ambiente locale, le organizzazioni si ritrovano probabilmente con responsabilità non ancora gestite e con risorse limitate disponibili da investire nella sicurezza. Tutto questo porta a un ambiente in cui i malintenzionati sono in grado di sfruttare vulnerabilità a più livelli.

Le organizzazioni possono migliorare la i tempi di rilevazione delle minacce e i tempi di risposta usando le funzionalità di sicurezza basate sul cloud e l'intelligence cloud di un provider.  Passando le responsabilità al provider di servizi cloud, le organizzazioni possono godere di una sicurezza più ampia e possono così riallocare le relative risorse e i relativi budget ad altre priorità aziendali.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantaggi di sicurezza di un modello di servizio cloud PaaS
Esaminiamo i vantaggi di sicurezza di una distribuzione di Azure PaaS rispetto a quella locale.

![Vantaggi di sicurezza del modello PaaS](./media/paas-deployments/advantages-of-paas.png)

Microsoft consente di ridurre i rischi e le responsabilità comuni già a partire dal fondo dello stack, ovvero l'infrastruttura fisica. Poiché viene costantemente monitorato da Microsoft stessa, il cloud di Microsoft è estremamente resistente agli attacchi. Pertanto non ha senso che venga considerato un bersaglio. A meno che non disponga di parecchio denaro e parecchie risorse, un malintenzionato sceglierà più probabilmente altri obiettivi.  

Al centro dello stack non c'è differenza tra una distribuzione PaaS e una locale. I rischi sono simili a livello di applicazione e a livello di gestione dell'account e degli accessi. Nella sezione dell'articolo dedicata ai passaggi successivi verranno illustrate le procedure consigliate per eliminare o ridurre al minimo tali rischi.

Nella parte superiore dello stack, governance dei dati e Rights Management, è possibile ridurre un rischio grazie alla gestione delle chiavi, La gestione delle chiavi è illustrata nelle procedure consigliate. Anche se la gestione delle chiavi è una responsabilità aggiuntiva, le aree di una distribuzione di PaaS non devono più essere gestite, quindi è possibile spostare le risorse nella gestione delle chiavi.

La piattaforma di Azure offre inoltre una protezione avanzata dagli attacchi DDoS grazie a varie tecnologie basate su rete. Tuttavia, tutti i tipi di protezione da attacchi DDoS basati su rete hanno vari limiti per quanto riguarda collegamenti e data center. Per evitare le problematiche legate agli attacchi DDoS di portata elevata, è possibile sfruttare la funzionalità cloud centrale di Azure per usare rapidamente e automaticamente la scalabilità come difesa da questi attacchi. Negli articoli sulle procedure consigliate verranno illustrati in dettaglio i vari passaggi.

## <a name="modernizing-the-defenders-mindset"></a>Modernizzazione dell'approccio di difesa
Le distribuzioni PaaS impongono un cambiamento dell'approccio generale verso la sicurezza. Si passa da un controllo autonomo sostanzialmente totale alla condivisione di alcune responsabilità con Microsoft.

Un'altra differenza significativa tra le distribuzioni PaaS e quelle locali tradizionali consiste in una nuova visione di ciò che compone il perimetro di sicurezza primario. In passato, il perimetro di sicurezza locale primario era rappresentato dalla rete, con la maggior parte delle progettazioni di sicurezza locali che la usava come risorsa di sicurezza primaria. Per le distribuzioni PaaS, è consigliabile passare all'identità come perimetro di sicurezza primario.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adottare dei criteri di identità come perimetro di sicurezza primario
Una delle cinque caratteristiche essenziali del cloud computing consiste nell'ampio accesso alla rete, cosa che rende meno efficace un approccio incentrato su di essa. L'obiettivo della maggior parte del cloud computing è consentire l'accesso agli utenti a prescindere dalla loro posizione. La maggior parte di loro si troverà da qualche parte in Internet.

Nella figura seguente viene illustrata l'evoluzione del perimetro di sicurezza, da perimetro di rete a perimetro di identità. La sicurezza consiste sempre meno nel difendere la rete e sempre più nel difendere i dati e nel gestire la sicurezza di app e utenti. La differenza principale sta nel voler avvicinare il più possibile la sicurezza alle priorità dell'azienda.

![L'identità come nuovo perimetro di sicurezza](./media/paas-deployments/identity-perimeter.png)

Inizialmente, i servizi PaaS di Azure come i ruoli Web e SQL di Azure non offrivano difese con perimetri di rete tradizionali. Questo perché si presupponeva che l'esposizione a Internet fosse proprio lo scopo del ruolo Web e che l'autenticazione fosse essa stessa il nuovo perimetro, ad esempio BLOB o SQL di Azure.

Le procedure di sicurezza moderne presuppongono che un malintenzionato abbia violato il perimetro di rete. Pertanto, le procedure di difesa moderne si sono focalizzate sull'identità. Le organizzazioni devono stabilire un perimetro di sicurezza basato sull'identità con procedure consigliate di autenticazione e autorizzazione estremamente robuste.

I principi e i modelli per i perimetri di rete sono disponibili da molto tempo, tuttavia nel settore c'è meno esperienza riguardo l'uso dell'identità come perimetro di sicurezza primario. Ciò premesso, l'esperienza accumulata è comunque sufficiente per dare alcuni consigli generali la cui efficacia è comprovata sul campo, applicabili a quasi tutti i servizi PaaS.

Di seguito sono illustrate le procedure consigliate per la gestione del perimetro di identità.

**Procedura consigliata**: proteggere le chiavi e le credenziali per proteggere la distribuzione PaaS.   
**Dettagli**: la perdita delle chiavi e delle credenziali è un problema comune. È possibile usare una soluzione centralizzata in cui le chiavi e i segreti possono essere archiviati in moduli di protezione hardware (HSM). [Azure Key Vault](../../key-vault/key-vault-overview.md) protegge le chiavi e i segreti crittografando le chiavi di autenticazione, le chiavi dell'account di archiviazione, le chiavi di crittografia dei dati, i file PFX e le password usando chiavi protette da HSM.

**Procedura consigliata**: non inserire le credenziali e altri segreti nel codice sorgente o GitHub.   
**Dettagli**: un rischio ben peggiore della perdita di chiavi e credenziali consiste negli accessi non autorizzati. Gli utenti malintenzionati possono sfruttare le tecnologie bot per trovare chiavi e segreti archiviati in repository di codice come GitHub. Si consiglia pertanto di non inserire chiavi e segreti in questi archivi di codice pubblici.

**Procedura consigliata**: proteggere le interfacce di gestione della macchina virtuale nei servizi ibridi PaaS e IaaS usando un'interfaccia di gestione che consenta all'utente di gestire direttamente in remoto le macchine virtuali.   
**Dettagli**: possono essere usati i protocolli di gestione remota come [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607) e [Comunicazione remota di PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting). In generale, è consigliabile non abilitare l'accesso remoto diretto alle macchine virtuali da Internet.

Se possibile, usare approcci alternativi come l'uso di reti private virtuali in una rete virtuale di Azure. Se non sono disponibili soluzioni alternative, assicurarsi di usare passphrase complesse e l'autenticazione a due fattori (ad esempio [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)).

**Procedura consigliata**: usare piattaforme di autenticazione e autorizzazione robuste.   
**Dettagli**: usare le identità federate in Azure AD invece degli archivi utente personalizzati. Quando si usano identità federate, è possibile sfruttare un approccio basato sulla piattaforma e delegare ai partner la gestione delle identità autorizzate. Un approccio con identità federate è particolarmente importante quando i dipendenti vengono rimossi e le modifiche devono essere applicate in più sistemi di identità e autorizzazioni.

Usare i meccanismi di autenticazione e autorizzazione forniti dalla piattaforma invece di un codice personalizzato. poiché sviluppare un codice di autenticazione personalizzato può dare luogo a errori. La maggior parte degli sviluppatori non sarà esperta in sicurezza e probabilmente non conoscerà tutte le sfaccettature e gli ultimi sviluppi legati ad autenticazione e autorizzazione. Il codice commerciale, ad esempio quello di Microsoft, è spesso soggetto a rigorose analisi di sicurezza.

Usare l'autenticazione a due fattori. L'autenticazione a due fattori è lo standard attuale per l'autenticazione e l'autorizzazione, in quanto permette di evitare le lacune di sicurezza intrinseche nei tipi di autenticazione basati su nome utente e password. L'accesso a entrambe le interfacce di gestione di Azure, ovvero il portale e PowerShell da remoto e ai servizi per i clienti deve essere progettato e configurato per l'uso di [Azure Multi-Factor Authentication (MFA)](/azure/active-directory/authentication/multi-factor-authentication).

Usare protocolli di autenticazione standard come OAuth2 e Kerberos. Questi protocolli sono stati ampiamente analizzati e sono probabilmente implementati come parte delle librerie della piattaforma per autenticazione e autorizzazione.

## <a name="use-threat-modeling-during-application-design"></a>Usare la modellazione delle minacce durante la progettazione delle applicazioni
[Security Development Lifecycle](https://www.microsoft.com/en-us/sdl) di Microsoft specifica che i team devono sviluppare un processo denominato modellazione delle minacce durante la fase di progettazione. Per semplificare questo processo, Microsoft ha creato [SDL Threat Modeling Tool](/azure/security/azure-security-threat-modeling-tool). Modellando la progettazione dell'applicazione ed enumerando le minacce [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) in tutti i limiti di trust, sarà possibile rilevare errori di progettazione sin dall'inizio.

Nella tabella seguente sono elencate le minacce STRIDE e alcuni esempi di mitigazioni dei rischi che usano le funzionalità di Azure. Queste mitigazioni dei rischi non funzioneranno in tutte le situazioni.

| Threat | Proprietà di sicurezza | Potenziali mitigazioni della piattaforma di Azure |
| --- | --- | --- |
| Spoofing | Authentication | Richiede connessioni HTTPS. |
| Manomissione | Integrità | Convalida dei certificati SSL. |
| Ripudio | Non ripudio | Abilitazione del [monitoraggio e diagnostica](/azure/architecture/best-practices/monitoring) di Azure. |
| Diffusione di informazioni | Riservatezza | Crittografare i dati sensibili inattivi tramite [certificati di servizio](/rest/api/appservice/certificates). |
| Denial of Service | Disponibilità | Monitorare le metriche delle prestazioni per le potenziali condizioni di Denial of service. Implementare i filtri di connessione. |
| Elevazione dei privilegi | Authorization | Usare [Privileged Identity Management](/azure/active-directory/privileged-identity-management/subscription-requirements). |

## <a name="develop-on-azure-app-service"></a>Sviluppare nel Servizio app di Azure
[Servizio app di Azure](/azure/app-service/overview) è una soluzione PaaS che consente di creare applicazioni Web e per dispositivi mobili per qualsiasi piattaforma o dispositivo e di connettersi ai dati ovunque, nel cloud o in locale. Il servizio app include le funzionalità Web e per dispositivi mobili prima fornite separatamente come Siti Web di Azure e Servizi mobili di Azure. Include anche nuove funzionalità per l'automazione dei processi aziendali e l'hosting di API cloud. Il servizio app è un singolo servizio integrato che offre un set completo di funzionalità per scenari Web, per dispositivi mobili e di integrazione.

Di seguito sono illustrate le procedure consigliate per l'uso della cache locale del servizio app.

**Procedura consigliata**: [eseguire l'autenticazione tramite Azure Active Directory](/azure/app-service/overview-authentication-authorization).   
**Dettagli**: il servizio app fornisce un servizio OAuth 2.0 per il provider di identità. OAuth 2.0 è incentrato sulla semplicità di sviluppo client fornendo i flussi di autorizzazione specifici per le applicazioni Web, applicazioni desktop e telefoni cellulari. Azure AD usa OAuth 2.0 per consentire all'utente di autorizzare l'accesso alle applicazioni per dispositivi mobili e alle applicazioni Web.

**Procedura consigliata**: limitare l'accesso in base al principio di necessità e al principio dei privilegi minimi in materia di sicurezza.   
**Dettagli**: la limitazione degli accessi è fondamentale per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati. Il controllo degli accessi in base al ruolo (RBAC) può essere usato per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. Per altre informazioni sulla concessione agli utenti dell'accesso alle applicazioni, vedere la sezione relativa all'[introduzione alla gestione degli accessi](/azure/role-based-access-control/overview).

**Procedura consigliata**: proteggere le chiavi.   
**Dettagli**: Azure Key Vault consente di proteggere i segreti e le chiavi di crittografia usati da servizi e applicazioni cloud. Con Key Vault è possibile crittografare chiavi e segreti (ad esempio, chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dati, file PFX e password) usando chiavi protette da moduli di protezione hardware (HSM). Per una maggiore sicurezza, puoi importare o generare le chiavi in moduli di protezione hardware. Per ulteriori informazioni, vedere [Azure Key Vault](/azure/key-vault/key-vault-overview). È anche possibile utilizzare Azure Key Vault per gestire i certificati TLS con il rinnovo automatico.

**Procedura consigliata**: limitare gli indirizzi IP di origine in ingresso.   
**Dettagli**: L'[ambiente del servizio app](/azure/app-service/environment/intro) ha una funzionalità di integrazione di rete virtuale che consente di limitare gli indirizzi IP di origine in ingresso tramite gruppi di sicurezza di rete (NSG). Le reti virtuali consentono di posizionare le risorse di Azure in una rete instradabile non Internet di cui si controlla l'accesso. Per altre informazioni, vedere [Integrare un'app in una rete virtuale di Azure](/azure/app-service/web-sites-integrate-with-vnet).

**Procedura consigliata**: monitorare lo stato di sicurezza degli ambienti del Servizio app di Azure.   
**Dettagli**: usare il Centro sicurezza per monitorare gli ambienti del servizio app. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea degli [elementi consigliati](../../security-center/security-center-virtual-machine-protection.md) utili per definire il processo di configurazione dei controlli necessari.

> [!NOTE]
> La funzionalità di monitoraggio del Servizio app di Azure è disponibile in anteprima e solo per il [livello Standard](/azure/security-center/security-center-pricing) del Centro sicurezza.
>
>

## <a name="install-a-web-application-firewall"></a>Installare un web application firewall
Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni vulnerabilità note. Per citarne alcuni, tra i più comuni troviamo gli attacchi SQL injection e gli attacchi di scripting intersito. Impedire questo tipo di attacchi nel codice dell'applicazione può essere un'operazione complessa e potrebbe richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un Web application firewall centralizzato semplifica notevolmente la gestione della sicurezza e offre agli amministratori delle applicazioni migliori garanzie contro le minacce o le intrusioni. Una soluzione WAF è anche in grado di reagire più velocemente a una minaccia alla sicurezza tramite l'applicazione di patch su una vulnerabilità nota in una posizione centrale, anziché proteggere ogni singola applicazione Web. È possibile convertire facilmente i gateway applicazione esistenti in un gateway applicazione con Web application firewall.

[Web application firewall (WAF)](/azure/frontdoor/waf-overview) è una funzionalità del gateway applicazione che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. WAF si basa su regole di set di regole principali [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 o 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Monitorare le prestazioni delle applicazioni
Il monitoraggio comporta la raccolta e l'analisi dei dati per determinare le prestazioni, l'integrità e la disponibilità dell'applicazione. Una strategia di monitoraggio efficace aiuta a comprendere il funzionamento dettagliato dei componenti dell'applicazione. Contribuisce ad aumentare il tempo di attività tramite notifiche degli aspetti critici, in modo da poterli risolvere prima che diventino effettivi problemi. Consente inoltre di rilevare le anomalie che potrebbero essere correlate alla sicurezza.

Usare [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) per monitorare disponibilità, prestazioni e utilizzo dell'applicazione, indipendentemente dal fatto che sia ospitata nel cloud o in locale. Con Application Insights è anche possibile identificare e diagnosticare rapidamente gli errori nell'applicazione senza attendere che vengano segnalati da un utente. Con le informazioni raccolte, è possibile prendere decisioni informate sulla manutenzione dell'applicazione e sui miglioramenti da apportare.

Application Insights include strumenti estensivi per l'interazione con i dati raccolti dal servizio stesso. Application Insights archivia questi dati in un repository comune. Può sfruttare le funzionalità condivise, ad esempio gli avvisi, i dashboard e l'analisi approfondita con il linguaggio di query kusto.

## <a name="perform-security-penetration-testing"></a>Eseguire test di penetrazione della sicurezza
La convalida delle difese di sicurezza è importante quanto il test di qualsiasi altra funzionalità. Eseguire [test di penetrazione](pen-testing.md) in una parte standard del processo di compilazione e distribuzione. Pianificare test di sicurezza e analisi delle vulnerabilità regolari sulle applicazioni distribuite e monitorare le porte aperte, gli endpoint e gli attacchi.

Il test fuzzy è un metodo per individuare gli errori del programma (errori del codice) fornendo dati di input in formato non valido alle interfacce di programma (punti di ingresso) che analizzano e utilizzano questi dati. Il rilevamento dei rischi per la [sicurezza di Microsoft](https://www.microsoft.com/en-us/security-risk-detection/) è uno strumento basato sul cloud che è possibile usare per cercare bug e altre vulnerabilità di sicurezza nel software prima di distribuirlo in Azure. Lo strumento è progettato per rilevare le vulnerabilità prima di distribuire il software, in modo da non dover applicare patch a un bug, gestire arresti anomali o rispondere a un attacco dopo il rilascio del software.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati illustrati i vantaggi di sicurezza di una distribuzione PaaS di Azure e le procedure consigliate per le applicazioni cloud. Il passaggio successivo è costituito dall'approfondimento delle procedure consigliate per proteggere le soluzioni PaaS Web e mobili usando servizi di Azure specifici. Si inizierà dal Servizio app di Azure, il database SQL di Azure e SQL Data Warehouse di Azure e Archiviazione di Azure. Non appena saranno disponibili le procedure consigliate per altri servizi Azure, nell'elenco seguente verranno inseriti i relativi collegamenti:

- [Informazioni sul servizio app di Azure](paas-applications-using-app-services.md)
- [Database SQL di Azure e SQL Data Warehouse di Azure](paas-applications-using-sql.md)
- [Archiviazione di Azure](paas-applications-using-storage.md)
- Cache Redis di Azure
- Service Bus di Azure
- Web application firewall

Vedere [sviluppo di applicazioni sicure in Azure](abstract-develop-secure-apps.md) per domande di sicurezza e controlli da considerare in ogni fase del ciclo di vita di sviluppo del software durante lo sviluppo di applicazioni per il cloud.

Per altre procedure consigliate per la sicurezza da usare in fase di progettazione, distribuzione e gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com
