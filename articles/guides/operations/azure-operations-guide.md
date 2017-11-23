---
title: Guida introduttiva per gli operatori IT di Azure | Microsoft Docs
description: Guida introduttiva per gli operatori IT di Azure
services: 
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: nepeters
ms.openlocfilehash: 1180001c9fe74aab6b51c5b5969b80a8c7e1302f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="introduction-to-cloud-computing-and-microsoft-azure"></a>Introduzione al cloud computing e a Microsoft Azure

Questa guida presenta i concetti principali relativi alla distribuzione e alla gestione di un'infrastruttura di Microsoft Azure. Se non si ha familiarità con il cloud computing o con Azure, questa guida offre un'introduzione rapida ai concetti, alla distribuzione e ai dettagli di gestione. Molte sezioni della guida descrivono un'operazione, ad esempio la distribuzione di una macchina virtuale, e quindi offrono un collegamento per l'approfondimento dei dettagli tecnici.


## <a name="cloud-computing-overview"></a>Panoramica del cloud computing

Il cloud computing rappresenta un'alternativa moderna al centro dati locale tradizionale. I fornitori di cloud pubblico, che rendono disponibile e gestiscono l'intera infrastruttura informatica e il software di gestione sottostante, offrono una vasta gamma di servizi cloud. In questo caso, un servizio cloud può corrispondere a una macchina virtuale, a un server Web o a un motore di database ospitato nel cloud. I clienti di provider di servizi cloud prendono in lease i servizi cloud in base alle proprie esigenze. In questo modo, la spesa in conto capitale della manutenzione dell'hardware viene convertita in spesa operativa. Un servizio cloud offre anche i vantaggi seguenti:

-   Rapidità di distribuzione di ambienti di calcolo di grandi dimensioni

-   Rapidità di deallocazione dei sistemi non più necessari

-   Facilità di distribuzione di sistemi tradizionalmente complessi come i sistemi di bilanciamento del carico

-   Possibilità di garantire una capacità di calcolo flessibile o di eseguire un ridimensionamento quando necessario

-   Maggiore convenienza in termini economici degli ambienti di elaborazione

-   Possibilità di accedere ovunque con un portale basato sul Web o con un programma di automazione

-   Possibilità di usufruire di servizi basati sul cloud in grado di soddisfare la maggior parte delle esigenze di calcolo e delle applicazioni

Un'infrastruttura locale garantisce il controllo completo sull'hardware e sul software distribuito. Di conseguenza, in passato le decisioni relative all'approvvigionamento hardware portavano principalmente all'ampliamento dell'infrastruttura, ad esempio all'acquisto di un server con più core per soddisfare l'esigenza di massime prestazioni. Purtroppo, a parte i periodi di domanda elevata, un'infrastruttura di questo tipo viene sottoutilizzata. Con Azure è possibile limitare la distribuzione all'infrastruttura effettivamente necessaria, ampliandola o riducendola in qualsiasi momento. In questo modo l'attenzione si sposta sulla scalabilità orizzontale tramite la distribuzione di nodi di calcolo aggiuntivi per soddisfare le esigenze di prestazioni. La scalabilità orizzontale dei servizi cloud è più economica rispetto a un ampliamento tramite l'acquisto di hardware costoso.

Microsoft ha distribuito numerosi centri dati di Azure in tutto il mondo e prevede di distribuirne altri. Microsoft, poi, sta aumentando i cloud sovrani in aree quali Cina e Germania. Una distribuzione di centri dati di questo tipo è alla portata solo delle aziende globali di maggiori dimensioni. Tramite Azure, quindi, è più facile per le aziende di qualsiasi dimensione distribuire i propri servizi vicino ai propri clienti.

Per le piccole imprese, Azure rappresenta un punto di ingresso a costo contenuto, con la possibilità di un ridimensionamento rapido man mano che le esigenze di calcolo aumentano. Ciò consente di evitare un immediato consistente investimento nell'infrastruttura e offre la flessibilità necessaria per riprogettare i sistemi quando necessario. L'uso del cloud computing si adatta perfettamente al modello "scale-fast, fail-fast" della crescita iniziale.

Per altre informazioni sulle aree di Azure disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-is-classified-into-three-categories-saas-paas-and-iaas"></a>Il cloud computing è suddiviso in tre categorie: SaaS, PaaS e IaaS.

#### <a name="saas-software-as-a-service"></a>SaaS: Software as a Service

Un sistema SaaS (Software as a Service, software come un servizio) è costituito da software ospitato e gestito in modo centralizzato. Un sistema di questo tipo si basa in genere su un'architettura multi-tenant, in base alla quale viene usata un'unica versione dell'applicazione per tutti i clienti. È possibile scalare orizzontalmente questa architettura in più istanze, per garantire prestazioni ottimali in tutte le posizioni. Il software SaaS, in genere, viene concesso in licenza tramite una sottoscrizione mensile o annuale.

Microsoft Office 365 è un buon esempio di offerta SaaS. Pagando una quota di sottoscrizione mensile o annuale, i sottoscrittori possono usufruire di Microsoft Exchange, Microsoft OneDrive e delle altre applicazioni della famiglia di prodotti Microsoft Office come servizi. I sottoscrittori hanno sempre a disposizione la versione più recente delle applicazioni e non devono occuparsi della gestione del server di Exchange. Rispetto all'installazione e all'aggiornamento annuale di Office, questa soluzione è più economica e meno impegnativa.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platform as a Service 

L'architettura PaaS (Platform as a Service, piattaforma distribuita come servizio) consente di distribuire le applicazioni in un ambiente messo a disposizione dal fornitore del servizio cloud. La gestione dell'infrastruttura viene interamente svolta dal fornitore. È quindi possibile concentrarsi sullo sviluppo di applicazioni.

Azure offre diverse soluzioni PaaS, ad esempio la funzionalità App Web del servizio app di Azure e Servizi cloud di Azure (ruoli Web e di lavoro). In entrambi i casi gli sviluppatori hanno a disposizione diverse modalità di distribuzione delle applicazioni senza dover conoscere i dettagli alla base del funzionamento di tali modalità. Gli sviluppatori non devono creare le macchine virtuali (VM, Virtual Machine), accedere a ognuna di queste tramite Remote Desktop Protocol (RDP) o installare l'applicazione. Devono solo premere un pulsante (o qualcosa del genere) e gli strumenti forniti da Microsoft effettueranno il provisioning delle VM (Virtual Machine, macchina virtuale) e quindi distribuiranno e installeranno l'applicazione all'interno di queste.

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastructure as a service

Un fornitore di cloud IaaS (Infrastructure as a Service, infrastruttura distribuita come servizio) si occupa del funzionamento e della gestione di tutte le risorse fisiche di calcolo e di tutto il software necessari per consentire la virtualizzazione di computer. Un cliente di questo servizio distribuisce macchine virtuali all'interno di centri dati ospitati. Anche se le macchine virtuali si trovano in un centro dati situato altrove, il consumer IaaS ha il controllo della configurazione e della gestione di queste.

Azure include diverse soluzioni IaaS, ad esempio macchine virtuali e set di scalabilità di macchine virtuali, nonché la relativa infrastruttura di rete. Le macchine virtuali sono una scelta frequente per la migrazione iniziale dei servizi in Azure, poiché consentono di applicare il modello di migrazione "lift-and-shift". È possibile configurare una VM in base all'infrastruttura di esecuzione dei servizi nel centro dati attuale e quindi eseguire la migrazione del software nella nuova VM. Può essere necessario eseguire aggiornamenti di configurazione, ad esempio inserendo gli URL di altri servizi o di altre risorse di archiviazione, ma in questo modo è possibile eseguire la migrazione di molte applicazioni.

I set di scalabilità di macchine virtuali, definiti a un livello superiore rispetto a quello delle macchine virtuali di Azure, rappresentano un modo semplice per distribuire cluster di VM identiche. I set di scalabilità di macchine virtuali supportano anche il ridimensionamento automatico. È quindi possibile distribuire automaticamente nuove VM quando necessario. I set di scalabilità di macchine virtuali rappresentano pertanto la piattaforma ideale per l'hosting di cluster di elaborazione di microservizi di livello superiore, come Azure Service Fabric e il servizio contenitore di Azure.

## <a name="azure-services"></a>Servizi di Azure

La piattaforma di cloud computing di Azure offre numerosi servizi, tra i quali i seguenti.

### <a name="compute-services"></a>Servizi di calcolo

Servizi di hosting ed esecuzione di carichi di lavoro delle applicazioni:

-   Macchine virtuali di Azure, sia Linux che Windows

-   Servizi app (App Web, App per dispositivi mobili, App per la logica, app per le API e app per le funzioni)

-   Azure Batch (per processi di calcolo batch e paralleli su larga scala)

-   Azure Service Fabric

-   Servizio contenitore di Azure

### <a name="data-services"></a>Servizi dati

Servizi per l'archiviazione e la gestione dei dati:

-   Archiviazione di Azure (comprende i servizi BLOB, Code, Tabella e File di Azure)

-   Database SQL di Azure

-   Azure DocumentDB

-   Microsoft Azure StorSimple

-   Cache Redis di Azure

### <a name="application-services"></a>Servizi per le applicazioni

Servizi per la compilazione e il funzionamento delle applicazioni:

-   Azure Active Directory (Azure AD)

-   Bus di servizio di Azure per la connessione di sistemi distribuiti

-   Azure HDInsight per l'elaborazione di Big Data

-   Utilità di pianificazione di Azure

-   Servizi multimediali di Azure

### <a name="network-services"></a>Servizi di rete

Servizi di rete sia all'interno di Azure che tra Azure e i centri dati locali:

-   Rete virtuale di Azure

-   Azure ExpressRoute

-   DNS fornito da Azure

-   Gestione traffico di Azure

-   Rete per la distribuzione di contenuti di Azure

Per la documentazione dettagliata sui servizi di Azure, vedere la [documentazione dei servizi di Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Concetti chiave di Azure

### <a name="datacenters-and-regions"></a>Centri dati e aree


Azure è una piattaforma cloud globale disponibile a livello generale in molte aree del mondo. Quando si esegue il provisioning di un servizio, di un'applicazione o di una macchina virtuale in Azure, viene chiesto di selezionare un'area. L'area selezionata rappresenta un centro dati speciﬁco in cui viene eseguita l'applicazione. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

Uno dei vantaggi dell'uso di Azure è la possibilità di distribuire le applicazioni in più centri dati in tutto il mondo. La scelta dell'area può influire sulle prestazioni dell'applicazione. La scelta ottimale corrisponde all'area più vicina alla maggior parte dei clienti, per ridurre la latenza nelle richieste di rete. È anche possibile selezionare un'area in base ai requisiti legali per la distribuzione dell'app in determinati paesi.

### <a name="azure-portal"></a>Portale di Azure


Il portale di Azure è un'applicazione basata sul Web che può essere usata per creare, gestire e rimuovere risorse e servizi di Azure. Il Portale di Azure, disponibile all'indirizzo https://portal.azure.com, include un dashboard personalizzabile e gli strumenti per la gestione delle risorse di Azure e fornisce le informazioni relative alla fatturazione e alla sottoscrizione. Per altre informazioni, vedere [Panoramica del portale di Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) e [Gestire le risorse di Azure mediante il portale](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Risorse

Le risorse di Azure sono servizi singoli di calcolo, rete, dati o hosting di app distribuiti in una sottoscrizione di Azure. Tra le risorse più usate, le macchine virtuali, gli account di archiviazione e i database SQL. I servizi di Azure sono spesso costituiti da più risorse di Azure correlate. Una macchina virtuale di Azure, ad esempio, può includere una VM, un account di archiviazione, una scheda di rete e un indirizzo IP pubblico. Queste risorse possono essere create, gestite ed eliminate singolarmente o in gruppo. Le risorse di Azure sono illustrate in dettaglio più avanti in questa guida.

### <a name="resource-groups"></a>Gruppi di risorse

Un gruppo di risorse di Azure è un contenitore con risorse correlate per una soluzione di Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. I gruppi di risorse di Azure sono illustrati in dettaglio più avanti in questa guida.

### <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Un modello di Azure Resource Manager è un file JSON (JavaScript Object Notation) che definisce una o più risorse da distribuire in un gruppo di risorse. Il modello definisce anche le dipendenze tra risorse distribuite. I modelli di Resource Manager sono illustrati in dettaglio più avanti in questa guida.

### <a name="automation"></a>Automazione


Oltre alla creazione, alla gestione e all'eliminazione di risorse tramite il Portale di Azure, è possibile automatizzare queste attività tramite PowerShell o l'interfaccia della riga di comando di Azure.

**Azure PowerShell**

Azure PowerShell è un set di moduli che mette a disposizione alcuni cmdlet per la gestione di Azure. È possibile usare i cmdlet per creare, gestire e rimuovere servizi di Azure. I cmdlet consentono di ottenere distribuzioni coerenti, ripetibili e automatiche. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

**interfaccia della riga di comando di Azure**

L'interfaccia della riga di comando di Azure è uno strumento con cui è possibile creare, gestire e rimuovere risorse di Azure dalla riga di comando. L'interfaccia della riga di comando di Azure è disponibile per Linux, Mac OS X e Windows. Per altre informazioni e dettagli tecnici, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli.md).

Le **API REST** di Azure si basano su un set di API REST che supportano l'interfaccia utente del Portale di Azure. La maggior parte di queste API REST è supportata anche per consentire il provisioning e la gestione a livello di codice delle risorse e delle app di Azure da qualsiasi dispositivo abilitato per Internet. Per altre informazioni, vedere [Azure REST SDK Reference](https://docs.microsoft.com/rest/api/index) (Informazioni di riferimento per Azure REST SDK).


## <a name="azure-subscriptions"></a>Sottoscrizioni Azure


Una sottoscrizione è un raggruppamento logico di servizi di Azure collegato a un account di Azure. Un singolo account di Azure può contenere più sottoscrizioni. La fatturazione per i servizi di Azure si basa sulla sottoscrizione. Le sottoscrizioni di Azure hanno un amministratore account, che ha il controllo completo sulla sottoscrizione, e un amministratore del servizio, che ha il controllo su tutti i servizi nella sottoscrizione. Oltre agli amministratori, è possibile concedere a singoli account il controllo dettagliato delle risorse di Azure tramite Controllo degli accessi in base al ruolo.

### <a name="select-and-enable-an-azure-subscription"></a>Selezionare e abilitare una sottoscrizione di Azure

Per poter usare i servizi di Azure è necessaria una sottoscrizione. Sono disponibili diversi tipi di sottoscrizione.

**Account gratuito**: il collegamento per la registrazione di un account gratuito è nel [sito Web Azure](https://azure.microsoft.com/). Questo account offre un credito di 30 giorni per provare una qualsiasi combinazione di risorse in Azure. Se si supera il credito disponibile, l'account viene sospeso. Alla fine del periodo di prova, i servizi vengono ritirati e non funzionano più. È possibile eseguire l'aggiornamento a una sottoscrizione con pagamento in base al consumo.

**Sottoscrizioni MSDN**: con una sottoscrizione MSDN si ottiene un credito Azure di un certo importo ogni mese. Ad esempio, con una sottoscrizione Microsoft Visual Studio Enterprise 2015 con MSDN si ottiene un credito Azure di \$ 150 al mese.

Se si supera il credito disponibile, il servizio viene disabilitato fino all'inizio del mese successivo. È possibile disattivare il limite di spesa e aggiungere una carta di credito da usare per i costi aggiuntivi. Per alcuni di questi costi vengono applicati sconti agli account MSDN. Ad esempio, per le macchine virtuali che eseguono Windows Server si paga il prezzo della sottoscrizione per Linux e non sono previsti costi aggiuntivi per i server Microsoft, come Microsoft SQL Server. Per questo motivo gli account MSDN sono ideali per gli scenari di sviluppo e test.

**Account BizSpark**: il programma Microsoft BizSpark offre numerosi vantaggi per le startup, ad esempio la possibilità di accedere a tutto il software Microsoft per gli ambienti di sviluppo e test per un numero di account MSDN fino a cinque. Per ognuno di questi cinque account MSDN si ottiene un credito Azure di $ 150 e una riduzione della tariffa per diversi servizi di Azure, ad esempio le macchine virtuali.

**Pagamento in base al consumo**: con questa sottoscrizione si paga in base all'uso, aggiungendo una carta di credito o di debito all'account. Le organizzazioni possono anche richiedere la fattura.

**Contratti Enterprise**: con un contratto Enterprise ci si impegna a usufruire di un certo numero di servizi di Azure nel corso di un anno e si paga la tariffa corrispondente in anticipo. L'impegno preso verrà utilizzato nel corso dell'anno. Se si supera l'importo concordato, è possibile pagare l'eccedenza in ritardo. A seconda dell'importo dell'impegno, si ottiene un certo sconto sui servizi di Azure.



### <a name="grant-administrative-access-to-an-azure-subscription"></a>Concedere l'accesso amministrativo a una sottoscrizione di Azure


Sono disponibili più ruoli di amministratore account, che possono essere modificati in qualsiasi momento. Due ruoli chiave sono:

-   **Amministratore del servizio**: questo ruolo è autorizzato a gestire i servizi di Azure. Per impostazione predefinita, a questo ruolo è consentito l'accesso allo stesso account dell'amministratore account.

-   **Coamministratore**: questo ruolo ha le stesse autorizzazioni di accesso dell'amministratore del servizio. Questo ruolo, tuttavia, non può modificare l'associazione di una sottoscrizione alle directory di Azure.

Per altre informazioni, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](../../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Visualizzare le informazioni di fatturazione nel Portale di Azure


Un aspetto importante dell'uso di Azure è la possibilità di visualizzare le informazioni di fatturazione. Il Portale di Azure fornisce una visualizzazione dettagliata delle informazioni di fatturazione di Azure.

Per altre informazioni, vedere [Come scaricare la fattura e i dati di utilizzo giornalieri di Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Ottenere le informazioni di fatturazione dalle API di fatturazione


Oltre a visualizzare la fatturazione nel portale, è possibile accedere alle informazioni di fatturazione tramite uno script o un programma che usi le API REST di fatturazione di Azure:

-   È possibile usare l'API di uso di Azure per recuperare i dati di utilizzo. È anche possibile ottimizzare le informazioni di fatturazione sull'utilizzo assegnando tag a risorse di Azure correlate. Ad esempio, si può contrassegnare ogni risorsa di un gruppo di risorse con il nome di un reparto o di un progetto e quindi registrare i costi specifici per quel tag.

-   L'API tariffario pubblicitario di Azure può essere usata per visualizzare l'elenco di tutte le risorse disponibili, insieme ai metadati e alle informazioni sui prezzi di ognuna di tali risorse.

Per altre informazioni, vedere [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prevedere i costi con il calcolatore dei prezzi

I prezzi dei servizi di Azure sono diversi tra loro. Molti servizi di Azure sono disponibili nei livelli Basic, Standard e Premium. In genere, per ogni livello sono disponibili diversi livelli di prezzo e di prestazioni. Per creare stime dei prezzi, è possibile usare il [calcolatore prezzi online](http://azure.microsoft.com/pricing/calculator). Il calcolatore offre la flessibilità necessaria per eseguire la stima del costo di un'unica risorsa o di un gruppo di risorse.

### <a name="set-up-billing-alerts"></a>Impostare avvisi di fatturazione per le sottoscrizioni Microsoft Azure

Dopo aver distribuito l'applicazione o una soluzione in Azure, è possibile creare avvisi per l'invio di messaggi di posta elettronica all'approssimarsi dei limiti di spesa definiti per ogni avviso. Per altre informazioni, vedere [Configurare avvisi di fatturazione per le sottoscrizioni di Microsoft Azure](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Gestione risorse di Azure

Azure Resource Manager è un meccanismo di distribuzione, gestione e organizzazione delle risorse di Azure. Con Resource Manager è possibile riunire molte risorse singole in un unico gruppo di risorse.

Resource Manager include anche funzionalità di distribuzione che consentono la personalizzazione della distribuzione e della configurazione delle risorse correlate. Con Resource Manager, ad esempio, è possibile distribuire come unità singola un'applicazione costituita da più macchine virtuali, un sistema di bilanciamento del carico e un database SQL. Per sviluppare distribuzioni di questo tipo si usa un modello di Resource Manager.

Gestione risorse offre numerosi vantaggi:

-   È possibile distribuire, gestire e monitorare tutte le risorse per la soluzione come un gruppo, anziché gestire singolarmente tali risorse.

-   È possibile distribuire più volte la soluzione nel corso del ciclo di vita dello sviluppo, con la certezza che le risorse vengono distribuite in uno stato coerente.

-   È possibile gestire l'infrastruttura con modelli dichiarativi, piuttosto che con script.

-   È possibile definire le dipendenze tra risorse e pertanto esse vengono distribuite nell'ordine corretto.

-   È possibile applicare il controllo di accesso a tutti i servizi nel gruppo di risorse, perché il controllo di accesso basato sui ruoli è integrato in modo nativo nella piattaforma di gestione.

-   È anche possibile applicare tag alle risorse per organizzare in modo logico tutte le risorse nella sottoscrizione.

-   Visualizzando i costi di un gruppo di risorse che condividono lo stesso tag si possono ottenere informazioni dettagliate sulla fatturazione per l'organizzazione.

### <a name="tips-for-creating-resource-groups"></a>Suggerimenti per la creazione di un gruppo di risorse


Al momento di prendere decisioni relative ai gruppi di risorse, tenere presenti questi suggerimenti:

-   Tutte le risorse nello stesso gruppo di risorse devono avere lo stesso ciclo di vita.

-   È possibile assegnare una risorsa a un solo gruppo alla volta.

-   È possibile aggiungere o rimuovere una risorsa da un gruppo di risorse in qualsiasi momento. Ogni risorsa deve appartenere a un gruppo di risorse. Se si rimuove una risorsa da un gruppo, quindi, è necessario aggiungerla a un altro.

-   È possibile spostare la maggior parte dei tipi di risorsa in un altro gruppo di risorse in qualsiasi momento.

-   Le risorse in un gruppo di risorse possono trovarsi in aree diverse.

-   È possibile usare un gruppo di risorse per controllare l'accesso alle risorse in esso contenute.

### <a name="building-resource-manager-templates"></a>Creazione di modelli di Resource Manager

I modelli di Resource Manager definiscono in modo dichiarativo le risorse e le configurazioni di risorse che verranno distribuite in un unico gruppo di risorse. È possibile usare modelli di Resource Manager per orchestrare distribuzioni complesse senza la necessità di dedicarsi eccessivamente alla stesura di script o di eseguire una configurazione manuale. Dopo aver sviluppato un modello, è possibile distribuirlo più volte, ogni volta con un esito identico.

Un modello di Resource Manager è costituito da quattro sezioni:

-   **Parametri**: input nella distribuzione. I valori dei parametri possono essere specificati da un utente o un da processo automatizzato. Un parametro di esempio può essere costituito dal nome utente amministratore e dalla password per una macchina virtuale di Windows. Se specificati, i valori dei parametri vengono usati per tutta la distribuzione.

-   **Variabili**: contengono valori usati nel corso dell'intera distribuzione. A differenza dei parametri, i valori delle variabili non vengono specificati in fase di distribuzione, ma sono hardcoded codice o generati in modo dinamico.

-   **Risorse**: questa sezione del modello definisce le risorse da distribuire, ad esempio macchine virtuali, account di archiviazione e reti virtuali.

-   **Output**: al termine di una distribuzione, Resource Manager può restituire dati, ad esempio stringhe di connessione generate dinamicamente.

Per l'automazione della distribuzione sono disponibili i meccanismi seguenti:

-   **Funzioni**: nei modelli di Resource Manager è possibile usare diverse funzioni. Queste prevedono l'esecuzione di operazioni, ad esempio la conversione di una stringa in lettere minuscole, la distribuzione di più istanze di una risorsa definita e la restituzione dinamica del gruppo di risorse di destinazione. Le funzioni di Resource Manager facilitano la creazione di distribuzioni dinamiche.

-   **Dipendenze delle risorse**: quando si distribuiscono più risorse, alcune saranno dipendenti da altre. Per semplificare la distribuzione, è possibile usare una dichiarazione di dipendenza, in modo che le risorse dipendenti vengono distribuite prima delle altre.

-   **Collegamento di modelli**: dall'interno di un modello di Resource Manager è possibile collegarsi a un altro modello. Ciò consente la scomposizione della distribuzione in un set di modelli di destinazione con scopi specifici.

È possibile creare modelli di Resource Manager in qualsiasi editor di testo. Tuttavia, Azure SDK per Visual Studio include strumenti che facilitano questa operazione. Con Visual Studio è possibile aggiungere risorse al modello tramite una procedura guidata e quindi eseguire la distribuzione e il debug del modello direttamente da Visual Studio. Per altre informazioni, vedere [Creazione di modelli di Azure Resource Manager](../../resource-group-authoring-templates.md).

Infine, dal Portale di Azure è possibile convertire gruppi di risorse esistenti in un modello riutilizzabile. Ciò può essere utile se si vuole creare un modello distribuibile di un gruppo di risorse esistente o si vuole solo esaminare il codice JSON sottostante. Per esportare un gruppo di risorse, selezionare il pulsante **Script di automazione** dalle impostazioni del gruppo di risorse.

## <a name="security-of-azure-resources-rbac"></a>Sicurezza delle risorse di Azure (Controllo degli accessi in base al ruolo)

È possibile concedere agli account utente l'accesso operativo per un ambito specifico: sottoscrizione, gruppo di risorse o risorsa singola. Ciò significa che è possibile distribuire un set di risorse in un gruppo di risorse, ad esempio una macchina virtuale e tutte le risorse correlate, e concedere autorizzazioni a un utente o a un gruppo specifico. Questo approccio consente di limitare l'accesso alle risorse appartenenti al gruppo di risorse di destinazione. È anche possibile concedere l'accesso a un'unica risorsa, ad esempio a una macchina virtuale o a una rete virtuale.

Per concedere l'accesso, si assegna un ruolo all'utente o al gruppo di utenti. Sono disponibili numerosi ruoli predefiniti. È anche possibile definire ruoli personalizzati.

Ecco alcuni esempi di ruoli incorporati in Azure:

-   **Proprietario**: un utente con questo ruolo può gestire qualsiasi aspetto, incluso l'accesso.

-   **Lettore**: un utente con questo ruolo può accedere in lettura alle risorse di qualsiasi tipo, ad eccezione dei segreti, ma non può apportare modifiche.

-   **Collaboratore Macchina virtuale**: un utente con questo ruolo può gestire le macchine virtuali ma non la rete virtuale alla quale sono connesse o l'account di archiviazione in cui risiede il file del disco rigido virtuale.

-   **Collaboratore Database SQL**: un utente con questo ruolo può gestire database SQL, ma non i criteri correlati alla sicurezza.

-   **Gestore Sicurezza SQL**: un utente con questo ruolo può gestire i criteri correlati alla sicurezza di SQL Server e dei database SQL.

-   **Collaboratore Account di archiviazione**: un utente con questo ruolo può gestire gli account di archiviazione, ma non l'accesso a questi.

Per altre informazioni, vedere [Usare l'assegnazione dei ruoli per gestire l'accesso alle risorse della sottoscrizione di Azure](../../active-directory/role-based-access-control-configure.md).

## <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

Macchine virtuali di Azure è uno dei servizi IaaS più importanti di Azure. Macchine virtuali di Azure supporta la distribuzione di macchine virtuali Windows o Linux in un centro dati di Microsoft Azure. Con Macchine virtuali di Azure l'utente ha il controllo completo della configurazione delle VM ed è responsabile dell'installazione, della configurazione e della manutenzione di tutto il software.

Quando si distribuisce una VM di Azure, è possibile selezionare un'immagine da Azure Marketplace oppure fornire una propria immagine generalizzata. Questa immagine viene usata per applicare il sistema operativo e la configurazione iniziale. Durante la distribuzione, Resource Manager gestisce alcune impostazioni di configurazione, ad esempio l'assegnazione del nome computer, le credenziali amministrative e la configurazione di rete. È possibile usare le estensioni macchina virtuale di Azure per automatizzare ancora di più le operazioni di configurazione, ad esempio l'installazione di software, la configurazione dell'antivirus e il monitoraggio delle soluzioni.

È possibile creare macchine virtuali con un'ampia scelta di dimensioni. Le dimensioni della macchina virtuale determinano l'allocazione delle risorse, ad esempio la capacità di elaborazione e di archiviazione e la memoria. In alcuni casi, funzionalità specifiche quali le schede di interfaccia di rete abilitate per RDMA e i dischi SSD sono disponibili solo con determinate dimensioni di VM. Per l'elenco completo delle funzionalità e delle dimensioni delle VM, vedere "Dimensioni per le macchine virtuali in Azure" per [Windows](../../virtual-machines/windows/sizes.md) e [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Casi d'uso

Poiché le macchine virtuali di Azure consentono il controllo completo della configurazione, sono ideali per un'ampia gamma di carichi di lavoro server non adatti a un modello PaaS. È ora possibile eseguire all'interno della piattaforma Microsoft Azure carichi di lavoro server, ad esempio i carichi di lavoro di server di database (SQL Server, Oracle o MongoDB), di Windows Server Active Directory, di Microsoft SharePoint, oltre a molti altri. Se necessario, è possibile spostare carichi di lavoro di questo tipo da un centro dati locale a una o più aree di Azure senza dover eseguire una riconfigurazione impegnativa.

### <a name="deployment-of-virtual-machines"></a>Distribuzione di macchine virtuali

È possibile distribuire macchine virtuali di Azure attraverso il Portale di Azure o tramite automazione con il modulo Azure PowerShell o l'interfaccia della riga di comando multipiattaforma.

**Portale**

Per la distribuzione di una macchina virtuale tramite il Portale di Azure servono solo una sottoscrizione di Azure attiva e l'accesso a un Web browser. È possibile selezionare molte immagini di sistemi operativi diversi con configurazioni diverse. Tutti i requisiti di archiviazione e di rete vengono configurati durante la distribuzione. Per altre informazioni, vedere "Creare una macchina virtuale nel Portale di Azure" per [Windows](../../virtual-machines/windows/quick-create-portal.md) e [Linux](../../virtual-machines/linux/quick-create-portal.md).

Oltre alle macchine virtuali, dal Portale di Azure è possibile distribuire modelli di Azure Resource Manager. Questa operazione consente di distribuire e configurare tutte le risorse definite nel modello. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).


**PowerShell**

La distribuzione di macchine virtuali di Azure tramite PowerShell consente di automatizzare completamente la distribuzione di tutte le risorse correlate, incluse le risorse di archiviazione e di rete. Per altre informazioni, vedere [Creare una macchina virtuale Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Oltre a distribuire singole risorse di calcolo di Azure, è possibile usare il modulo Azure PowerShell per distribuire un modello di Azure Resource Manager. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interfaccia della riga di comando (CLI)**

Come il modulo PowerShell, l'interfaccia della riga di comando di Azure consente l'automazione della distribuzione e può essere usata all'interno di sistemi Windows, OS X e Linux. Quando si usa il comando **vm quick-create** dell'interfaccia della riga di comando di Azure, vengono distribuite, oltre alla macchina virtuale stessa, tutte le risorse correlate, incluse le risorse di archiviazione e di rete. Per altre informazioni, vedere [Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../../virtual-machines/linux/quick-create-cli.md).

Analogamente, è possibile usare l'interfaccia della riga di comando di Azure per distribuire un modello di Resource Manager. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Accesso e sicurezza delle macchine virtuali

L'accesso a una macchina virtuale da Internet richiede la configurazione dell'interfaccia di rete associata, o del bilanciamento del carico, se applicabile, con un indirizzo IP pubblico. L'indirizzo IP pubblico include un nome DNS che si risolve nella macchina virtuale o nel bilanciamento del carico. Per altre informazioni, vedere [Indirizzi IP in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

La gestione dell'accesso alla macchina virtuale tramite l'indirizzo IP pubblico viene eseguita tramite una risorsa gruppo di sicurezza di rete (NSG, Network Security Group). Un gruppo NSG funge da firewall, consentendo o impedendo il traffico attraverso l'interfaccia di rete o la subnet in un set di porte definite. Ad esempio, per creare una sessione di Desktop remoto con una VM di Azure, è necessario configurare il gruppo NSG in modo che consenta il traffico in ingresso attraverso la porta 3389. Per altre informazioni, vedere [Aprire le porte in una VM in Azure con il Portale di Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Infine, come nella gestione di qualsiasi computer, è necessario garantire la sicurezza delle macchine virtuali di Azure a livello di sistema operativo usando credenziali di sicurezza e firewall software.

## <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure è un servizio gestito da Microsoft che offre risorse di archiviazione ridondanti, scalabili e durevoli. È possibile aggiungere un account di archiviazione di Azure come risorsa a qualsiasi gruppo di risorse tramite qualsiasi metodo di distribuzione di risorse. Azure include quattro tipi di archiviazione: BLOB, file, tabelle e code. Quando si distribuisce un account di archiviazione, sono disponibili due tipi di account, uno per utilizzo generico e l'altro per l'archiviazione BLOB. Un account di archiviazione per utilizzo generico consente di accedere a tutti i quattro tipi di archiviazione. Un account di archiviazione BLOB è simile a un account per utilizzo generico, ma contiene BLOB specializzati che prevedono i livelli di accesso frequente e sporadico. Per altre informazioni sull'archiviazione BLOB, vedere [Archiviazione BLOB di Azure](../../storage/blobs/storage-blob-storage-tiers.md).

Gli account di archiviazione di Azure possono essere configurati con diversi livelli di ridondanza:

-   L'**archiviazione con ridondanza locale** garantisce la disponibilità elevata considerando riuscita un'operazione di scrittura solo se vengono eseguite in modo sincrono tre copie di tutti i dati. Queste copie vengono archiviate in un'unica struttura in un'unica area. Le repliche risiedono in domini di errore e domini di aggiornamento distinti. Ciò significa che i dati sono disponibili anche se un nodo di archiviazione contenente i dati si guasta o viene disconnesso per un aggiornamento.

-   L'**archiviazione con ridondanza geografica** crea tre copie sincrone dei dati nell'area primaria per la disponibilità elevata e quindi esegue tre repliche asincrone in un'area abbinata da usare per un eventuale ripristino di emergenza.

-   L'**archiviazione con ridondanza geografica e accesso in lettura** corrisponde all'archiviazione con ridondanza geografica, con in più la possibilità di leggere i dati nell'area secondaria. Questa possibilità rende questa opzione ideale per il ripristino di emergenza parziale. Nel caso di un problema con l'area primaria, è possibile modificare l'applicazione in modo che possa accedere in sola lettura all'area abbinata.

### <a name="use-cases"></a>Casi d'uso 

Per ogni tipo di archiviazione il caso d'uso è diverso.

**Archiviazione BLOB** 

Il termine *BLOB* è l'acronimo di *Binary Large OBject* (oggetto binario di grandi dimensioni). I BLOB sono file non strutturati, come quelli che gli utenti salvano nei loro computer. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti. Archiviazione Blob di Azure contiene anche dischi dati delle macchine virtuali di Azure.

Archiviazione di Azure supporta tre tipi di BLOB:

-   **BLOB in blocchi**, usati per contenere file normali con dimensioni fino a 195 GB (4 MB × 50.000 blocchi). Il caso d'uso principale dei BLOB in blocchi è l'archiviazione di file che vengono letti dall'inizio alla fine, ad esempio i file multimediali o di immagine per i siti Web. Si chiamano BLOB in blocchi perché i file più grandi di 64 MB devono essere caricati come blocchi di piccole dimensioni. Questi blocchi vengono quindi consolidati (ne viene eseguito il commit) nell'oggetto BLOB finale.

-   **BLOB di pagine**, usati per contenere file ad accesso casuale con dimensioni fino a 1 TB. I BLOB di pagine vengono usati principalmente per l'archiviazione di backup dei dischi rigidi virtuali che fungono da dischi durevoli di Macchine virtuali di Azure, il servizio di calcolo IaaS di Azure. Si chiamano BLOB di pagine perché consentono l'accesso in lettura/scrittura casuale a pagine da 512 byte.

-   **BLOB di accodamento**, costituiti da blocchi come i BLOB in blocchi, ma ottimizzati per le operazioni di accodamento. Questi vengono spesso usati per la registrazione di informazioni da una o più origini nello stesso BLOB. È ad esempio possibile scrivere nello stesso BLOB di accodamento tutte le informazioni di registrazione traccia per un'applicazione in esecuzione in più VM. Le dimensioni di un BLOB di accodamento singolo possono arrivare fino a un massimo di 195 GB.

Per altre informazioni, vedere [Introduzione all'archiviazione BLOB di Azure con .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) .

**Archiviazione file**

L'archiviazione file di Azure offre condivisioni file nel cloud tramite il protocollo SMB (Server Message Block) standard. Il servizio supporta sia SMB 2.1 che SMB 3.0. Con l'archiviazione file di Azure è possibile eseguire la migrazione ad Azure di applicazioni basate su condivisioni file in modo rapido e senza costose riscritture. Le applicazioni in esecuzione all'interno di macchine virtuali di Azure, di servizi cloud o di client locali possono montare una condivisione file nel cloud, nello stesso modo in cui un'applicazione desktop monta una tipica condivisione SMB. Non ci sono limiti per i componenti delle applicazioni che possono montare e accedere contemporaneamente alla condivisione di archiviazione file.

Poiché una condivisione di Archiviazione file è una condivisione file SMB standard, le applicazioni in esecuzione in Azure possono accedere ai dati nella condivisione tramite le API di I/O del file system. Gli sviluppatori possono quindi usare il codice esistente e le competenze acquisite per eseguire la migrazione delle applicazioni esistenti. I professionisti IT possono usare i cmdlet di PowerShell per creare, montare e gestire condivisioni di Archiviazione file nell'ambito delle attività di amministrazione delle applicazioni di Azure.

Per altre informazioni, vedere [Introduzione ad Archiviazione file di Azure in Windows](../../storage/files/storage-how-to-use-files-windows.md) o [Come usare l'archiviazione file di Azure con Linux](../../storage/files/storage-how-to-use-files-linux.md).

**Archiviazione tabelle**

Il servizio di archiviazione tabelle di Azure archivia dati NoSQL non strutturati nel cloud. L'archiviazione tabelle è un archivio di chiavi/attributi con una struttura senza schema. Poiché l'archiviazione tabelle è senza schema, è facile adattare i dati con il variare delle esigenze dell'applicazione. L'accesso ai dati è rapido e conveniente per tutti i tipi di applicazione. L'archiviazione tabelle presenta in genere costi decisamente più bassi rispetto alle soluzioni SQL tradizionali per volumi simili di dati.

È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi e altri tipi di metadati richiesti dal servizio. In una tabella è possibile archiviare qualsiasi numero di entità. Un account di archiviazione può contenere un numero qualsiasi di tabelle, fino al raggiungimento del limite di capacità dell'account stesso.

Per altre informazioni, vedere [Introduzione all'archiviazione tabelle di Azure](../../cosmos-db/table-storage-how-to-use-dotnet.md).

**Archiviazione code**

L'archivio code di Azure fornisce la messaggistica cloud tra i componenti dell'applicazione. Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, perché sia possibile ridimensionarli in modo indipendente. L'archivio code fornisce la messaggistica asincrona per la comunicazione tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, in un desktop, in un server locale o in un dispositivo mobile. Supporta inoltre la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

Per altre informazioni, vedere [Introduzione all'archiviazione code di Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Distribuzione di un account di archiviazione

Per la distribuzione di un account di archiviazione sono disponibili diverse opzioni.

**Portale**

Per la distribuzione di un account di archiviazione tramite il Portale di Azure servono solo una sottoscrizione di Azure attiva e l'accesso a un Web browser. È possibile distribuire un nuovo account di archiviazione in un gruppo di risorse nuovo o esistente. Dopo aver creato l'account di archiviazione, è possibile creare un contenitore BLOB o una condivisione file tramite il portale. È possibile creare entità di archiviazione di tabelle e code a livello di codice. Per altre informazioni, vedere [Creare un account di archiviazione](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

Oltre agli account di archiviazione, dal Portale di Azure è possibile distribuire modelli di Azure Resource Manager. Questa operazione consente di distribuire e configurare tutte le risorse definite nel modello, compresi eventuali account di archiviazione. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

La distribuzione di un account di archiviazione di Azure tramite PowerShell consente di automatizzare completamente la distribuzione dell'account stesso. Per altre informazioni, vedere [Uso di Azure PowerShell con Archiviazione di Azure](../../storage/common/storage-powershell-guide-full.md).

Oltre a distribuire singole risorse di Azure, è possibile usare il modulo Azure PowerShell per distribuire un modello di Azure Resource Manager. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interfaccia della riga di comando (CLI)**

Come il modulo PowerShell, l'interfaccia della riga di comando di Azure consente l'automazione della distribuzione e può essere usata all'interno di sistemi Windows, OS X e Linux. È possibile usare il comando **storage account create** dell'interfaccia della riga di comando di Azure per creare un account di archiviazione. Per altre informazioni, vedere [Uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure](../../storage/common/storage-azure-cli.md).

Analogamente, è possibile usare l'interfaccia della riga di comando di Azure per distribuire un modello di Resource Manager. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Accesso e sicurezza per Archiviazione di Azure

È possibile accedere ad Archiviazione di Azure in modi diversi, ad esempio tramite il Portale di Azure, durante la creazione e il funzionamento di VM e dalle librerie client di archiviazione. 

**Dischi delle macchine virtuali**

Quando si distribuisce una macchina virtuale, è necessario creare anche un account di archiviazione per il disco del sistema operativo della macchina virtuale e per eventuali dischi dati aggiuntivi. È possibile selezionare un account di archiviazione esistente o crearne uno nuovo. Poiché la dimensione massima di un BLOB è di 1.024 GB, un disco di macchina virtuale singolo può avere una dimensione massima di 1.023 GB. Per configurare un disco dati di maggiori dimensioni, è possibile esporre più dischi dati alla macchina virtuale e riunirli in un pool sotto forma di disco logico singolo. Per altre informazioni, vedere gli articoli relativi alla gestione dei dischi di Azure per [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) e [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

**Strumenti di archiviazione**

È possibile accedere agli account di archiviazione di Azure tramite molti strumenti diversi di esplorazione dell'archiviazione, ad esempio Visual Studio Cloud Explorer. Questi strumenti consentono di esplorare gli account e i dati di archiviazione. Per altre informazioni e per un elenco di strumenti di esplorazione dell'archiviazione disponibili, vedere [Strumento client di Archiviazione di Azure](../../storage/common/storage-explorers.md).

**API di archiviazione**

Le risorse di archiviazione sono accessibili da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS. In Archiviazione di Azure sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano l'uso di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'invio di operazioni in batch, la gestione delle eccezioni e la ripetizione automatica dei tentativi. Per altre informazioni, vedere [Azure Storage service REST API reference](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) (Informazioni di riferimento per l'API REST del servizio Archiviazione di Azure).

**Chiavi di accesso alle risorse di archiviazione**

Per ogni account di archiviazione esistono due chiavi di autenticazione: una chiave primaria e una chiave secondaria. Entrambe possono essere usate per operazioni di accesso alla risorsa di archiviazione. Queste chiavi di archiviazione proteggono l'account di archiviazione e sono obbligatorie per accedere ai dati a livello di codice. Esistono due chiavi che consentono il rollover occasionale delle chiavi per migliorare la sicurezza. È fondamentale mantenere protette le chiavi, perché il loro possesso, insieme al nome dell'account, consente l'accesso illimitato a tutti i dati nell'account di archiviazione.

**Firme di accesso condiviso**

Se si deve concedere agli utenti un accesso controllato alle risorse di archiviazione, è possibile creare una firma di accesso condiviso. Una firma di accesso condiviso è un token che può essere accodato a un URL per consentire l'accesso delegato a una risorsa di archiviazione. Chi possiede il token può accedere alla risorsa a cui questo fa riferimento con le autorizzazioni specificate all'interno di esso per il periodo di validità del token stesso. Per altre informazioni, vedere [Uso delle firme di accesso condiviso](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Rete virtuale di Azure


Le reti virtuali sono necessarie per supportare le comunicazioni tra macchine virtuali. È possibile definire subnet, un indirizzo IP personalizzato, impostazioni DNS, filtri di sicurezza e bilanciamento del carico. Usando un gateway VPN o un circuito ExpressRoute, è possibile connettere le reti virtuali di Azure alle reti locali.

### <a name="use-cases"></a>Casi d'uso

Per la rete di Azure esistono diversi casi d'uso.

**Reti virtuali solo cloud**

Per impostazione predefinita, una rete virtuale di Azure è accessibile solo per le risorse archiviate in Azure. Le risorse connesse alla stessa rete virtuale possono comunicare tra loro. È possibile associare le interfacce di rete delle macchine virtuali e i sistemi di bilanciamento del carico a un indirizzo IP pubblico per rendere accessibili le macchine virtuali da Internet. Per proteggere l'accesso alle risorse esposte pubblicamente è possibile usare un gruppo di sicurezza di rete.

**Reti virtuali cross-premise**

È possibile connettere una rete locale a una rete virtuale di Azure tramite ExpressRoute o una connessione VPN da sito a sito. In questa configurazione, la rete virtuale di Azure è essenzialmente un'estensione della rete locale basata su cloud.

Poiché la rete virtuale di Azure è connessa alla rete locale, le reti virtuali cross-premise devono usare una parte univoca dello spazio indirizzi usato dall'organizzazione. Nello stesso modo in cui sedi aziendali diverse vengono assegnate a una subnet IP specifica, con l'estensione della rete Azure diventa un'altra sede.

###<a name="deploying-a-virtual-network"></a>Distribuzione di una rete virtuale

Per la distribuzione di una rete virtuale sono disponibili diverse opzioni.

**Portale**

Per la distribuzione di una rete virtuale di Azure tramite il Portale di Azure servono solo una sottoscrizione di Azure attiva e l'accesso a un Web browser. È possibile distribuire una nuova rete virtuale in un gruppo di risorse nuovo o esistente. Quando si crea una nuova macchina virtuale dal portale, è possibile selezionare una rete virtuale esistente o crearne una nuova. Per altre informazioni, vedere [Creare una rete virtuale tramite il portale di Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

Oltre alle reti virtuali di Azure, dal Portale di Azure è possibile distribuire modelli di Azure Resource Manager. Questa operazione consente di distribuire e configurare tutte le risorse definite nel modello, comprese eventuali risorse di rete virtuale. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

La distribuzione di una rete virtuale di Azure tramite PowerShell consente di automatizzare completamente la distribuzione dell'account stesso. Per altre informazioni, vedere [Creare una rete virtuale usando PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md).

Oltre a distribuire singole risorse di Azure, è possibile usare il modulo Azure PowerShell per distribuire un modello di Azure Resource Manager. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Interfaccia della riga di comando (CLI)**

Come il modulo PowerShell, l'interfaccia della riga di comando di Azure consente l'automazione della distribuzione e può essere usata all'interno di sistemi Windows, OS X e Linux. Per creare una rete virtuale, è possibile usare il comando **network vnet create** dell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Creare una rete virtuale usando l'interfaccia della riga di comando di Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md) .

Analogamente, è possibile usare l'interfaccia della riga di comando di Azure per distribuire un modello di Resource Manager. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-networks"></a>Accesso e sicurezza delle reti virtuali

È possibile contribuire alla sicurezza delle reti virtuali di Azure usando un gruppo di sicurezza di rete. I gruppi di sicurezza di rete (NSG) contengono un elenco di regole dell'elenco di controllo di accesso (ACL, Access Control List) che consentono o impediscono il traffico di rete alle istanze delle VM in una rete virtuale. È possibile associare un gruppo NSG a una subnet o a singole istanze di VM all'interno di tale subnet. Quando si associa un gruppo NSG a una subnet, le regole ACL si applicano a tutte le istanze di VM in tale subnet. È anche possibile limitare ulteriormente il traffico verso una singola VM associando un gruppo NSG direttamente a tale VM. Per altre informazioni, vedere [Filtrare il traffico di rete con gruppi di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md).

## <a name="next-steps"></a>Passaggi successivi

- [Create a Windows VM](/virtual-machines/windows/quick-create-portal.md) (Creare una VM Windows)
- [Creare una macchina virtuale Linux](../../virtual-machines/linux/quick-create-portal.md)
