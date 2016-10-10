<properties
	pageTitle="Documentazione di Azure per enti pubblici | Microsoft Azure"
	description="Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>  

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>  


#  Panoramica della documentazione di Azure per enti pubblici

##  Introduzione alla documentazione di Azure per enti pubblici

Questo sito descrive le funzionalità di [Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/) e offre indicazioni generali applicabili a tutti i clienti. Prima di includere dati regolamentati in modo specifico nella sottoscrizione di Azure per enti pubblici, è consigliabile acquisire familiarità con le funzionalità di Azure per enti pubblici e contattare il team dell'account in caso di domande.

Per informazioni aggiornate sui servizi di Azure per enti pubblici coperti da regolamenti e accreditamenti specifici, è consigliabile vedere la [pagina Conformità nel sito Microsoft Trust Center](http://www.microsoft.com/it-IT/TrustCenter/Compliance/default.aspx). Possono essere disponibili altri servizi Microsoft, che tuttavia non rientrano nell'ambito dei servizi di Azure per enti pubblici presi in considerazione né vengono affrontati in questo documento. È possibile che i servizi di Azure per enti pubblici consentano di usare anche una varietà di risorse, applicazioni o i servizi aggiuntivi forniti da terze parti o da Microsoft nell'ambito di condizioni per l'utilizzo o informative sulla privacy distinte, che esulano dal contesto di questo documento. È responsabilità dell'utente verificare le condizioni di tutte le offerte di "componenti aggiuntivi", ad esempio quelle del Marketplace, per garantire che soddisfino le esigenze relative alla conformità.

Azure per enti pubblici è disponibile per gli enti che gestiscono dati soggetti a determinati requisiti e normative, come NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS, in cui l'uso di Azure per enti pubblici è richiesto per la conformità alle normative. I clienti di Azure per enti pubblici sono soggetti alla convalida dell'idoneità.

Gli enti che hanno domande sull'idoneità per Azure per enti pubblici possono contattare il team dell'account.

##  Principi per la protezione dei dati dei clienti in Azure per enti pubblici

Azure Government offre una gamma di funzionalità e servizi che è possibile usare per creare soluzioni cloud per soddisfare le esigenze di gestione dei dati regolamentati o controllati. Una soluzione dei clienti compatibile non è altro che l'efficace implementazione delle funzionalità predefinite di Azure per enti pubblici, associate a una procedura di sicurezza dei dati affidabile. Quando si ospita una soluzione in Azure per enti pubblici, Microsoft gestisce molti di questi requisiti a livello di infrastruttura cloud.

Il diagramma seguente illustra il modello di difesa avanzata di Azure. Ad esempio, Microsoft offre la protezione di base da attacchi DDoS per l'infrastruttura cloud, unitamente alle funzionalità dei clienti, come le appliance di sicurezza, per le esigenze riguardanti gli attacchi DDoS specifiche delle applicazioni dei clienti.

![testo alternativo](./media/azure-government-Defenseindepth.png)  

Questa pagina descrive i principi fondamentali per la protezione dei servizi e delle applicazioni e offre indicazioni e procedure consigliate su come applicare questi principi. In altre parole, illustra in che modo è consigliabile che i clienti usino al meglio Azure per enti pubblici per soddisfare gli obblighi e le responsabilità necessari per una soluzione che gestisce informazioni soggette alla normativa ITAR.

Ecco i principi globali per la protezione dei dati dei clienti:
* Protezione dei dati mediante la crittografia
* Gestione dei segreti
* Isolamento per limitare l'accesso ai dati

##  Protezione dei dati dei clienti mediante la crittografia

La mitigazione dei rischi e la conformità agli obblighi normativi determinano la crescente attenzione verso la crittografia dei dati e la relativa importanza. Usare un'implementazione di crittografia efficace per migliorare le attuali misure di sicurezza della rete e dell'applicazione e ridurre il rischio complessivo dell'ambiente cloud.

### <a name="Overview"></a>Crittografia dei dati inattivi
La crittografia dei dati inattivi si applica alla protezione del contenuto dei clienti conservato nello spazio di archiviazione su disco. Esistono diversi modi possibili a questo scopo:

### <a name="Overview"></a>Crittografia del servizio di archiviazione

La crittografia del servizio di archiviazione viene abilitata a livello di account di archiviazione, determinando la crittografia automatica di BLOB in blocchi e BLOB di pagine al momento della scrittura nella risorsa di archiviazione di Azure. Quando si leggono i dati dall'archiviazione di Azure, verranno decrittografati dal servizio di archiviazione prima di essere restituiti. Usare questo approccio per proteggere i dati senza dover modificare o aggiungere codice alle applicazioni.

### <a name="Overview"></a>Crittografia dischi di Azure
Usare Crittografia dischi di Azure per crittografare i dischi dati e del sistema operativo usati da una macchina virtuale di Azure. L'integrazione con l'insieme di credenziali delle chiavi di Azure consente il controllo e facilita la gestione delle chiavi di crittografia dei dischi.

### <a name="Overview"></a>Crittografia lato client
La crittografia lato client è incorporata nelle librerie client di archiviazione per .NET e Java, che possono utilizzare le API dell'insieme di credenziali delle chiavi di Azure, rendendone semplice l'implementazione. Usare l'insieme di credenziali delle chiavi di Azure per ottenere l'accesso ai segreti nello stesso insieme di credenziali delle chiavi Azure per utenti specifici usando Azure Active Directory.

### <a name="Overview"></a>Crittografia in transito

La crittografia di base disponibile per la connettività ad Azure per enti pubblici supporta il protocollo TLS (Transport Level Security) 1.2 e i certificati X.509. Per le connessioni di rete in modalità infrastruttura tra i data center di Azure per enti pubblici vengono usati anche gli algoritmi di crittografia FIPS (Federal Information Processing Standard) 140-2 livello 1. Le macchine virtuali Windows Server 2012 R2 e Windows 8 e versioni successive e le condivisioni file di Azure possono usare SMB 3.0 per la crittografia tra la macchina virtuale e la condivisione file. Usare la crittografia lato client per crittografare i dati prima che siano trasferiti nella risorsa di archiviazione in un'applicazione client e per decrittografarli dopo il trasferimento dalla risorsa di archiviazione.

### <a name="Overview"></a>Procedure consigliate per a crittografia

* Macchine virtuali IaaS: usare la Crittografia dischi di Azure. Attivare Crittografia del servizio di archiviazione per crittografare i file VHD usati per eseguire il backup di questi dischi nella risorsa di archiviazione di Azure, ma vengono crittografati solo dati appena scritti. Ciò significa che se si crea una macchina virtuale e quindi si abilita Crittografia del servizio di archiviazione nell'account di archiviazione che contiene il file VHD, verranno crittografate solo le modifiche, non il file VHD originale.
* Crittografia lato client: è il metodo più sicuro per crittografare i dati, perché li crittografa prima della trasmissione e crittografa i dati inattivi. Richiede tuttavia l'aggiunta di codice alle applicazioni tramite l'archiviazione, un'operazione che si potrebbe non voler eseguire. In questi casi, è possibile usare HTTPS per i dati in transito e Crittografia del servizio di archiviazione per crittografare i dati inattivi. La crittografia lato client comporta anche un carico maggiore sul client ed è necessario considerare questo aspetto nei piani di scalabilità, soprattutto se viene crittografata e trasferita una grande quantità di dati.

Per altre informazioni sulle opzioni di crittografia in Azure, vedere il [Guida alla sicurezza di Archiviazione di Azure](/storage-security-guide).

##  Protezione dei dati dei clienti tramite la gestione dei segreti

La gestione delle chiavi di protezione è fondamentale della protezione dei dati nel cloud. I clienti devono cercare di semplificare la gestione delle chiavi e mantenere il controllo delle chiavi usate dai servizi e dalle applicazioni cloud per crittografare i dati.

### <a name="Overview"></a>Procedure consigliate per la gestione dei segreti

* Usare l'insieme di credenziali delle chiavi per ridurre al minimo il rischio che i segreti vengano esposti tramite file di configurazione codificati, script o nel codice sorgente. L'insieme di credenziali delle chiavi di Azure consente di crittografare le chiavi, ad esempio le chiavi di crittografia per Crittografia dischi di Azure, e i segreti, ad esempio le password, archiviandoli nei moduli di protezione hardware convalidati per FIPS 140-2 livello 2. Per una maggiore sicurezza, è possibile importare o generare le chiavi in questi moduli di protezione hardware.
* È opportuno che il codice dell'applicazione e i modelli contengano solo i riferimenti URI ai segreti, ovvero i segreti effettivi non sono presenti nei repository di codice sorgente, configurazione o codice. In tal modo si impediscono gli attacchi di phishing chiave nei repository interni o esterni, ad esempio harvest-bot in GitHub.
* Utilizzare controlli degli accessi in base al ruolo sicuri all'interno dell'insieme di credenziali delle chiavi. Se un operatore attendibile lascia la società o si trasferisce in un nuovo gruppo all'interno della società, è consigliabile impedirgli di accedere ai segreti.

Per alte informazioni, vedere l'articolo sull'[insieme di credenziali delle chiavi per Azure per enti pubblici](/azure-government/azure-government-tech-keyvault)

##  Isolamento per limitare l'accesso ai dati

L'isolamento riguarda l'uso di limiti, segmentazione e contenitori per limitare l'accesso ai dati solo a utenti, servizi e applicazioni autorizzati. Ad esempio, la separazione tra i tenant è un meccanismo di sicurezza essenziali per le piattaforme cloud multi-tenant, come Microsoft Azure. L'isolamento logico consente di evitare che un tenant interferisca con le operazioni di qualsiasi altro tenant.

### <a name="Overview"></a>Isolamento dell'ambiente
L'ambiente di Azure per enti pubblici è un'istanza separata dal resto della rete Microsoft. Questa separazione si ottiene tramite una serie di controlli fisici e logici, tra cui la protezione con barriere fisiche usando dispositivi biometrici e fotocamere digitali. Uso di credenziali specifiche e dell'autenticazione a più fattori da parte del personale Microsoft che richiede l'accesso logico all'ambiente di produzione. Tutta l'infrastruttura dei servizi per Azure per enti pubblici si trova all'interno degli Stati Uniti.

#### <a name="Overview"></a>Isolamento per ogni cliente
Azure implementa il controllo di accesso alla rete e la separazione tramite l'isolamento VLAN, gli ACL, i servizi di bilanciamento del carico e i filtri IP

I clienti possono isolare ulteriormente le proprie risorse tra sottoscrizioni, gruppi di risorse, reti virtuali e subnet.

Per ulteriori informazioni sull'isolamento in Microsoft Azure, vedere la[sezione Isolamento della Guida alla sicurezza di Azure](/azure-security-getting-started/#isolation).

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

<!---HONumber=AcomDC_0928_2016-->