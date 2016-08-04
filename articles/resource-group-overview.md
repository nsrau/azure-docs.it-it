<properties
   pageTitle="Panoramica di Gestione risorse di Azure | Microsoft Azure"
   description="Viene descritto come utilizzare Gestione risorse di Azure per la distribuzione, la gestione e il controllo dell’accesso delle risorse in Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Panoramica di Gestione risorse di Microsoft Azure

L'infrastruttura per l'applicazione è in genere costituita da vari componenti, ad esempio una macchina virtuale, un account di archiviazione e una rete virtuale oppure un'app Web, un database, un server di database e servizi di terze parti. Questi componenti non appaiono come entità separate, ma come parti correlate e interdipendenti di una singola entità e devono essere distribuite, gestite e monitorate come gruppo. Gestione risorse di Azure consente di usare le risorse incluse nella soluzione come un gruppo. È quindi possibile distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, gestione temporanea e produzione. Gestione risorse offre funzionalità di sicurezza, controllo e categorizzazione che semplificano la gestione delle risorse dopo la distribuzione.

## Terminologia

Se non si ha esperienza con Azure Resource Manager, è possibile che non si conoscano alcuni termini.

- **risorsa**: elemento incluso nella soluzione Azure. Alcune risorse comuni sono le macchine virtuali, gli account di archiviazione, le app Web, i database e le reti virtuali, ma ne esistono molte altre.
- **gruppo di risorse**: contenitore con risorse correlate per un'applicazione. Il gruppo di risorse può includere tutte le risorse di un'applicazione o solo le risorse raggruppate. È possibile decidere come si desidera allocare le risorse a gruppi di risorse nel modo appropriato per l'organizzazione. Vedere [Gruppi di risorse](#resource-groups).
- **provider di risorse**: servizio che mette a disposizione le risorse che è possibile distribuire e gestire con Resource Manager. Ogni provider di risorse offre operazioni per l'uso delle risorse distribuite. Alcuni provider di risorse comuni sono Microsoft.Compute che mette a disposizione la risorsa delle macchine virtuali, Microsoft.Storage che offre la risorsa di account di archiviazione e Microsoft.Web che mette a disposizione risorse correlate alle app Web. Vedere [Provider di risorse](#resource-providers).
- **modello di Resource Manager**: file JavaScript Object Notation (JSON) che definisce una o più risorse da distribuire in un gruppo di risorse. Definisce anche le dipendenze tra le risorse distribuite. Il modello può essere usato per distribuire le risorse in modo coerente e ripetuto. Vedere [Distribuzione del modello](#template-deployment).
- **sintassi dichiarativa**: sintassi che consente di indicare l'oggetto da creare senza dover scrivere la sequenza di comandi di programmazione per crearlo. Il modello di Resource Manager è un esempio di sintassi dichiarativa. Nel file vengono definite le proprietà per l'infrastruttura da distribuire in Azure.

## Vantaggi dell'utilizzo di Gestione risorse

Gestione risorse offre numerosi vantaggi:

- È possibile distribuire, gestire e monitorare tutte le risorse per la soluzione come un gruppo, anziché gestire singolarmente tali risorse.
- È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita dello sviluppo garantendo al contempo che le risorse vengano distribuite in uno stato coerente.
- È possibile gestire l'infrastruttura con modelli dichiarativi, piuttosto che con script.
- È possibile definire le dipendenze tra risorse e pertanto esse vengono distribuite nell'ordine corretto.
- è possibile applicare il controllo di accesso a tutti i servizi nel gruppo di risorse perché il controllo di accesso basato sui ruoli (RBAC) è integrato in modo nativo nella piattaforma di gestione.
- È possibile applicare i tag alle risorse per organizzare logicamente tutte le risorse nella sottoscrizione.
- È possibile ottenere informazioni di fatturazione dettagliate visualizzando i costi aggregati per l'intero gruppo o per un gruppo di risorse che condividono la stessa categoria.

Gestione risorse offre un nuovo modo per distribuire e gestire le soluzioni. Per informazioni sulle modifiche introdotte rispetto al modello di distribuzione precedente, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](resource-manager-deployment-model.md).

## Indicazioni

I suggerimenti seguenti consentono di sfruttare al meglio Gestione risorse per le proprie soluzioni.

1. Definire e distribuire l'infrastruttura tramite la sintassi dichiarativa nei modelli di Gestione risorse, anziché tramite comandi imperativi.
2. Definire tutti i passaggi di distribuzione e configurazione nel modello. Per la configurazione della soluzione, è consigliabile evitare procedure manuali.
3. Eseguire i comandi imperativi per gestire le risorse, ad esempio per avviare o arrestare un'app o un computer.
4. Includere le risorse con lo stesso ciclo di vita in un gruppo di risorse. Usare le categorie per tutte le altre attività di organizzazione delle risorse.

Per ulteriori suggerimenti, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](resource-manager-template-best-practices.md).

## Gruppi di risorse

Esistono alcuni fattori importanti da considerare quando si definisce il gruppo di risorse:

1. Tutte le risorse del gruppo devono condividere lo stesso ciclo di vita. Le risorse verranno distribuite, aggiornate ed eliminate insieme. Se una risorsa, ad esempio un server di database, deve esistere in un ciclo di distribuzione diverso deve essere inclusa in un altro gruppo di risorse.
2. Ogni risorsa può appartenere a un solo gruppo di risorse.
3. È possibile aggiungere o rimuovere una risorsa in un gruppo di risorse in qualsiasi momento.
4. È possibile spostare una risorsa da un gruppo di risorse a un altro. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
4. Un gruppo di risorse può contenere le risorse che risiedono in aree diverse.
5. Un gruppo di risorse consente di definire l'ambito di controllo di accesso per operazioni amministrative.
6. Una risorsa può interagire con una risorsa in un altro gruppo di risorse quando le due risorse sono correlate, ma non condividono lo stesso ciclo di vita, ad esempio app Web che si connettono a un database.

## Provider di risorse

Ogni provider di risorse offre una serie di risorse e operazioni per l'uso dell'area tecnica. Per archiviare chiavi e segreti sarà ad esempio necessario usare il provider di risorse **Microsoft.KeyVault**. Questo provider di risorse offre un tipo di risorsa denominato **vaults** per creare l'insieme di credenziali delle chiavi e un tipo di risorsa denominato **vaults/secrets** per creare un segreto nell'insieme di credenziali delle chiavi. Mette anche a disposizione operazioni tramite l'[API REST dell'insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/dn903609.aspx). È possibile chiamare direttamente l'API REST oppure usare i [cmdlet PowerShell dell'insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/dn868052.aspx) e l'[interfaccia della riga di comando di Azure dell'insieme di credenziali delle chiavi](./key-vault/key-vault-manage-with-cli.md) per gestire l'insieme di credenziali delle chiavi. È anche possibile usare una serie di linguaggi di programmazione per la maggior parte delle risorse. Per altre informazioni, vedere [SDK ed esempi](#sdks-and-samples).

Per distribuire e gestire l'infrastruttura, è necessario conoscere i dettagli relativi al provider di risorse, come ad esempio i tipi di risorse offerti, i numeri di versione delle operazioni API REST, le operazioni supportate e lo schema da usare per impostare i valori del tipo di risorsa da creare. Per altre informazioni sui provider di risorse supportati, vedere [Provider, aree, versioni API e schemi di Gestione risorse](resource-manager-supported-services.md).

## Distribuzione del modello

Gestione risorse consente di creare un modello semplice (in formato JSON) che definisce la distribuzione e la configurazione dell'applicazione. Utilizzando un modello, è possibile distribuire l'applicazione in tutto il ciclo di vita dell'app ripetutamente e avere la certezza che le risorse vengano distribuite in uno stato coerente. Azure Resource Manager analizza le dipendenze per far sì che le risorse vengano create nell'ordine corretto. Per altre informazioni, vedere [Definizione delle dipendenze nei modelli di Gestione risorse di Azure](resource-group-define-dependencies.md).

Quando si crea una soluzione dal portale, la soluzione include automaticamente un modello di distribuzione. Non è necessario creare un modello da zero perché è possibile iniziare con il modello per la soluzione e personalizzarlo per soddisfare esigenze specifiche. È possibile recuperare un modello per un gruppo di risorse esistente esportando lo stato corrente del gruppo di risorse in un modello oppure visualizzando il modello usato per una distribuzione specifica. Per conoscere la sintassi del modello è molto utile visualizzare il modello esportato. Per altre informazioni sull'uso di modelli esportati, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).

Non è necessario definire l'intera infrastruttura in un singolo modello. Spesso, è consigliabile dividere i requisiti di distribuzione in un set di modelli specifici mirati, in base allo scopo. È anche possibile riutilizzare i modelli per altre soluzioni. Per distribuire una soluzione specifica, è necessario creare un modello master che collega tutti i modelli necessari. Per altre informazioni, vedere [Uso di modelli collegati con Gestione risorse di Azure](resource-group-linked-templates.md).

Inoltre, è possibile utilizzare il modello per gli aggiornamenti all'infrastruttura. Ad esempio, è possibile aggiungere una nuova risorsa per l'applicazione e aggiungere le regole di configurazione per le risorse già distribuite. Se il modello specifica la creazione di una nuova risorsa, ma tale risorsa esiste già, Gestione risorse di Azure esegue un aggiornamento anziché creare un nuovo asset. Gestione risorse di Azure aggiorna l'asset esistente allo stesso stato di quelli nuovi. In alternativa, è possibile specificare che Gestione risorse deve eliminare tutte le risorse non specificate nel modello. Per comprendere le differenze tra le opzioni durante la distribuzione, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).

È possibile specificare parametri nel modello per consentire la personalizzazione e la flessibilità nella distribuzione. Ad esempio, è possibile passare i valori dei parametri che consentono di personalizzare la distribuzione nell'ambiente di prova. Specificando i parametri, è possibile utilizzare lo stesso modello per la distribuzione in tutti gli ambienti dell'app.

In Gestione risorse sono disponibili estensioni utili negli scenari che richiedono operazioni aggiuntive, ad esempio l'installazione di software specifico non incluso nella configurazione. Se si utilizza già un servizio di gestione configurazione, ad esempio DSC, Chef o Puppet, è possibile continuare a usare il servizio utilizzando le estensioni.

Infine, il modello diventa parte del codice sorgente per l'applicazione. È possibile archiviarlo nel repository del codice sorgente e aggiornarlo con l'evoluzione dell'applicazione. È possibile modificare il modello tramite Visual Studio.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

Per istruzioni dettagliate sulla creazione di un modello, vedere [Procedura dettagliata per un modello di Resource Manager](resource-manager-template-walkthrough.md).

Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).

## Tag

Gestione risorse di Azure offre una funzionalità di categorizzazione che consente di suddividere le risorse in categorie in base ai requisiti di gestione o fatturazione. È possibile utilizzare i tag quando si dispone di un insieme complesso di gruppi di risorse e risorse ed è necessario visualizzare tali risorse in modo più significativo per l'utente. Ad esempio, è possibile contrassegnare le risorse che svolgono un ruolo simile nell'organizzazione o che appartengono allo stesso reparto. Senza tag è possibile che gli utenti dell'organizzazione creino più risorse, che possono risultare molto difficili da identificare e gestire in un secondo momento. Ad esempio, è possibile eliminare tutte le risorse per un particolare progetto, ma se tali risorse non sono state contrassegnate per il progetto, sarà necessario trovarle manualmente. L'assegnazione di tag può essere un modo importante per ridurre i costi non necessari nella sottoscrizione.

Non è necessario che le risorse si trovino nello stesso gruppo di risorse per condividere un tag. È possibile creare una propria tassonomia di tag per assicurarsi che tutti gli utenti dell'organizzazione utilizzino tag comuni anziché applichino inavvertitamente tag leggermente diversi (ad esempio "dept" anziché "department").

Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md). È possibile creare [criteri personalizzati](#manage-resources-with-customized-policies) che richiedono l'aggiunta di tag alle risorse durante la distribuzione.

## Controllo di accesso

Gestione risorse di Azure consente di controllare gli utenti autorizzati ad accedere ad azioni specifiche per l'organizzazione. In modo nativo, integra la piattaforma di gestione OAuth e controllo di accesso basato sui ruoli (RBAC) e applica tale controllo di accesso a tutti i servizi nel gruppo di risorse. È possibile aggiungere utenti ai ruoli predefiniti specifici di piattaforma e risorsa e applicare tali ruoli a una sottoscrizione, a un gruppo di risorse o a una risorsa per limitare l'accesso. Ad esempio, è possibile sfruttare il ruolo predefinito denominato Collaboratore di DB SQL che consente agli utenti di gestire i database, ma non i server di database o i criteri di sicurezza. È possibile aggiungere utenti dell'organizzazione che richiedono questo tipo di accesso al ruolo Collaboratore DB SQL e assegnare il ruolo alla sottoscrizione, al gruppo di risorse o alla risorsa.

Gestione risorse di Azure registra automaticamente le azioni dell'utente ai fini del controllo. Per informazioni sull'uso dei log di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md). L'argomento [RBAC: Ruoli predefiniti](./active-directory/role-based-access-built-in-roles.md) contiene un elenco dei ruoli predefiniti e delle azioni consentite. I ruoli predefiniti includono i ruoli generali, ad esempio Proprietario, Lettore e Collaboratore, oltre ai ruoli specifici del servizio come ad esempio Collaboratore Macchina virtuale, Collaboratore Rete virtuale, Gestore Sicurezza SQL, solo per citarne alcuni.

È anche possibile bloccare in modo esplicito le risorse critiche per impedire agli utenti di eliminarle o modificarle. Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

Per le procedure consigliate, vedere [Considerazioni sulla sicurezza per Gestione risorse di Azure](best-practices-resource-manager-security.md)

## Gestire risorse con criteri personalizzati

Gestione risorse consente di creare criteri personalizzati per gestire le risorse. I tipi di criteri creati possono includere scenari diversi, ad esempio l'applicazione di una convenzione di denominazione alle risorse, la limitazione dei tipi di istanze delle risorse che è possibile distribuire, la limitazione delle aree che possono ospitare un tipo di risorsa o la richiesta di un valore di tag sulle risorse per organizzare la fatturazione per reparto. Creare criteri per ridurre i costi e mantenere la coerenza nella sottoscrizione. Per altre informazioni, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

## Livello di gestione coerente

Gestione risorse fornisce operazioni completamente compatibili attraverso Azure PowerShell, l'interfaccia della riga di comando di Azure per Mac, Linux e Windows, il portale di Azure o l'API REST. È possibile utilizzare l'interfaccia più adatta alle proprie esigenze e spostarsi rapidamente tra le interfacce senza confusione. Nel portale vengono anche visualizzate notifiche per le azioni eseguite all'esterno del portale.

Per informazioni su PowerShell, vedere [Utilizzo di Azure PowerShell con Gestione risorse di Azure](powershell-azure-resource-manager.md) e [Cmdlet di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Per informazioni sull'interfaccia della riga di comando di Azure, vedere [Utilizzo dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](xplat-cli-azure-resource-manager.md).

Per informazioni sull'API REST, vedere [Informazioni si riferimento sull'API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx). Per visualizzare le operazioni REST per le risorse distribuite, vedere [Usare Esplora risorse di Azure per visualizzare e modificare le risorse](resource-manager-resource-explorer.md).

Per informazioni sull'uso del portale, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](resource-group-template-deploy-portal.md).

Gestione risorse di Azure supporta la condivisione di risorse tra origini (CORS, Cross-Origin Resource Sharing). Con CORS è possibile chiamare l'API REST di Gestione risorse o un'API REST del servizio Azure da un'applicazione Web residente in un dominio diverso. Senza il supporto di CORS il Web browser impedisce a un'app in un dominio di accedere alle risorse in un altro dominio. Gestione risorse abilita CORS per tutte le richieste con credenziali di autenticazione valide.

## SDK ed esempi

Azure SDK sono disponibili per più linguaggi e piattaforme. Ogni implementazione del linguaggio è disponibile tramite Gestione pacchetti del relativo ecosistema e in GitHub.

Il codice in ogni SDK viene generato dalle specifiche dell'API RESTful di Azure. Queste specifiche sono open source e si basano sulla specifica Swagger 2.0. Il codice degli SDK viene generato con un progetto open source denominato AutoRest. AutoRest trasforma le specifiche delle API basate su REST in librerie client in più linguaggi. Se si vogliono migliorare alcuni aspetti del codice generato negli SDK, l'intero set di strumenti per creare gli SDK è aperto, disponibile gratuitamente e basato su un formato di specifica API ampiamente diffuso.

**Esempi**: operatività immediata con il linguaggio scelto.

- [.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet)
- [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java)
- [Node.JS](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs)
- [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python)
- [PHP](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=php) *presto disponibile*
- [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

**Repository di SDK open source**: sono graditi commenti e suggerimenti, problemi e richieste pull.

- [.NET](https://github.com/Azure/azure-sdk-for-net)
- [Java](https://github.com/Azure/azure-sdk-for-java)
- [Node.JS](https://github.com/Azure/azure-sdk-for-node)
- [PHP](https://github.com/Azure/azure-sdk-for-php)
- [Python](https://github.com/Azure/azure-sdk-for-python)
- [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [AZURE.NOTE] Se l'SDK non offre le funzionalità necessarie, è anche possibile chiamare direttamente l'[API REST di Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Passaggi successivi

- Per una semplice introduzione all'uso dei modelli, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).
- Per istruzioni dettagliate sulla creazione di un modello, vedere [Procedura dettagliata per un modello di Resource Manager](resource-manager-template-walkthrough.md).
- Per comprendere le funzioni che è possibile usare in un modello, vedere [Funzioni del modello](resource-group-template-functions.md)
- Per informazioni sull'uso di Visual Studio con Resource Manager, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
- Per informazioni sull'uso di Visual Studio Code con Resource Manager, vedere [Uso dei modelli di Azure Resource Manager in Visual Studio Code](resource-manager-vs-code.md).

Ecco una dimostrazione video di questa panoramica:

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=AcomDC_0727_2016-->