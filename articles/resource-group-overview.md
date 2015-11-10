<properties
   pageTitle="Panoramica di Gestione risorse di Azure | Microsoft Azure"
   description="Viene descritto come utilizzare Gestione risorse di Azure per la distribuzione, la gestione e il controllo dell’accesso delle risorse in Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="tomfitz"/>

# Panoramica di Gestione risorse di Microsoft Azure

L'infrastruttura per l'applicazione è in genere costituita da vari componenti, ad esempio una macchina virtuale, un account di archiviazione e una rete virtuale oppure un'app Web, un database, un server di database e servizi di terze parti. Questi componenti non appaiono come entità separate, ma come parti correlate e interdipendenti di una singola entità e devono essere distribuite, gestite e monitorate come gruppo. Gestione risorse di Azure consente di usare le risorse incluse nella soluzione come un gruppo. È quindi possibile distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, gestione temporanea e produzione. Gestione risorse offre funzionalità di sicurezza, controllo e categorizzazione che semplificano la gestione delle risorse dopo la distribuzione.

## Vantaggi dell'utilizzo di Gestione risorse

Gestione risorse offre numerosi vantaggi:

- È possibile distribuire, gestire e monitorare tutte le risorse per la soluzione come un gruppo, anziché gestire singolarmente tali risorse.
- È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita dello sviluppo garantendo al contempo che le risorse vengano distribuite in uno stato coerente.
- È possibile utilizzare modelli dichiarativi per definire la distribuzione.
- È possibile definire le dipendenze tra risorse e pertanto esse vengono distribuite nell'ordine corretto.
- è possibile applicare il controllo di accesso a tutti i servizi nel gruppo di risorse perché il controllo di accesso basato sui ruoli (RBAC) è integrato in modo nativo nella piattaforma di gestione.
- È possibile applicare i tag alle risorse per organizzare logicamente tutte le risorse nella sottoscrizione.
- È ottenere informazioni di fatturazione dettagliate visualizzando i costi aggregati per l'intero gruppo o per un gruppo di risorse che condividono la stessa categoria.  

Gestione risorse offre un nuovo modo per distribuire e gestire le soluzioni. Per informazioni sulle modifiche introdotte rispetto al modello di distribuzione precedente, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](resource-manager-deployment-model.md).

## Indicazioni

I suggerimenti seguenti consentono di sfruttare al meglio Gestione risorse per le proprie soluzioni.

1. Definire e distribuire l'infrastruttura tramite la sintassi dichiarativa nei modelli di Gestione risorse, anziché tramite comandi imperativi.
2. Definire tutti i passaggi di distribuzione e configurazione nel modello. Per la configurazione della soluzione, è consigliabile evitare procedure manuali.
3. Eseguire i comandi imperativi per gestire le risorse, ad esempio per avviare o arrestare un'app o un computer.
4. Includere le risorse con lo stesso ciclo di vita in un gruppo di risorse. Usare le categorie per tutte le altre attività di organizzazione delle risorse.

## Gruppi di risorse

Un gruppo di risorse è un contenitore che contiene risorse correlate per un'applicazione. Il gruppo di risorse potrebbe includere tutte le risorse per un'applicazione o solo le risorse raggruppate logicamente. È possibile decidere come si desidera allocare le risorse a gruppi di risorse nel modo appropriato per l'organizzazione.

Esistono alcuni fattori importanti da considerare quando si definisce il gruppo di risorse:

1. Tutte le risorse del gruppo devono condividere lo stesso ciclo di vita. Le risorse verranno distribuite, aggiornate ed eliminate insieme. Se una risorsa, ad esempio un server di database, deve esistere in un ciclo di distribuzione diverso deve essere inclusa in un altro gruppo di risorse.
2. Ogni risorsa può appartenere a un solo gruppo di risorse.
3. È possibile aggiungere o rimuovere una risorsa in un gruppo di risorse in qualsiasi momento.
4. È possibile spostare una risorsa da un gruppo di risorse a un altro. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).
4. Un gruppo di risorse può contenere le risorse che risiedono in aree diverse.
5. Un gruppo di risorse consente di definire l'ambito di controllo di accesso per operazioni amministrative.
6. Una risorsa può essere collegata a una risorsa in un altro gruppo di risorse quando le due risorse devono interagire l'una con l'altra ma non condividono lo stesso ciclo di vita, ad esempio più app che si connettono a un database. Per altre informazioni, vedere [Collegamento di risorse in Gestione risorse di Azure](resource-group-link-resources.md).

## Distribuzione del modello

Gestione risorse consente di creare un modello semplice (in formato JSON) che definisce la distribuzione e la configurazione dell'applicazione. Questo modello è noto come modello di Gestione risorse e permette di definire la distribuzione in modo dichiarativo. Utilizzando un modello, è possibile distribuire l'applicazione in tutto il ciclo di vita dell'app ripetutamente e avere la certezza che le risorse vengano distribuite in uno stato coerente.

All'interno del modello, è possibile definire l'infrastruttura per l'applicazione, come configurare tale infrastruttura e come pubblicare il codice dell'app in tale infrastruttura. Non è necessario preoccuparsi dell'ordine per la distribuzione perché Gestione risorse di Azure analizza le dipendenze per assicurarsi che le risorse vengano create nell'ordine corretto. Per altre informazioni, vedere [Definizione delle dipendenze nei modelli di Gestione risorse di Azure](resource-group-define-dependencies.md).

Non è necessario definire l'intera infrastruttura in un singolo modello. Spesso, è consigliabile dividere i requisiti di distribuzione in un set di modelli specifici mirati, in base allo scopo. È anche possibile riutilizzare i modelli per altre soluzioni. Per distribuire una soluzione specifica, è necessario creare un modello master che collega tutti i modelli necessari. Per altre informazioni, vedere [Uso di modelli collegati con Gestione risorse di Azure](resource-group-linked-templates.md).

Inoltre, è possibile utilizzare il modello per gli aggiornamenti all'infrastruttura. Ad esempio, è possibile aggiungere una nuova risorsa per l'applicazione e aggiungere le regole di configurazione per le risorse già distribuite. Se il modello specifica la creazione di una nuova risorsa, ma tale risorsa esiste già, Gestione risorse di Azure esegue un aggiornamento anziché creare un nuovo asset. Gestione risorse di Azure aggiorna l'asset esistente allo stesso stato di quelli nuovi.

È possibile specificare parametri nel modello per consentire la personalizzazione e la flessibilità nella distribuzione. Ad esempio, è possibile passare i valori dei parametri che consentono di personalizzare la distribuzione nell'ambiente di prova. Specificando i parametri, è possibile utilizzare lo stesso modello per la distribuzione in tutti gli ambienti dell'app.

In Gestione risorse sono disponibili estensioni utili negli scenari che richiedono operazioni aggiuntive, ad esempio l'installazione di software specifico non incluso nella configurazione. Se si utilizza già un servizio di gestione configurazione, ad esempio DSC, Chef o Puppet, è possibile continuare a usare il servizio utilizzando le estensioni.

Quando si crea una soluzione da Marketplace, la soluzione include automaticamente un modello di distribuzione. Non è necessario creare un modello da zero perché è possibile iniziare con il modello per la soluzione e personalizzarlo per soddisfare esigenze specifiche.

Infine, il modello diventa parte del codice sorgente per l'applicazione. È possibile archiviarlo nel repository del codice sorgente e aggiornarlo con l'evoluzione dell'applicazione. È possibile modificare il modello tramite Visual Studio.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md).

Per gli schemi del modello, vedere [Schemi di Gestione risorse di Azure](https://github.com/Azure/azure-resource-manager-schemas).

Per informazioni sull'uso di un modello per la distribuzione, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).

Per indicazioni su come strutturare i modelli, vedere [Procedure consigliate per la progettazione di modelli di Gestione risorse di Azure](best-practices-resource-manager-design-templates.md).

Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments-preview-portal.md).

## Tag

Gestione risorse di Azure offre una funzionalità categorizzazione che consente suddividere le risorse in categorie in base ai requisiti di gestione o fatturazione. È possibile utilizzare i tag quando si dispone di un insieme complesso di gruppi di risorse e risorse ed è necessario visualizzare tali risorse in modo più significativo per l'utente. Ad esempio, è possibile contrassegnare le risorse che svolgono un ruolo simile nell'organizzazione o che appartengono allo stesso reparto.

Non è necessario che le risorse si trovino nello stesso gruppo di risorse per condividere un tag. È possibile creare una propria tassonomia di tag per assicurarsi che tutti gli utenti dell'organizzazione utilizzino tag comuni anziché applichino inavvertitamente tag leggermente diversi (ad esempio "dept" anziché "department").

Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md).

## Controllo di accesso

Gestione risorse di Azure consente di controllare gli utenti autorizzati ad accedere ad azioni specifiche per l'organizzazione. In modo nativo, integra la piattaforma di gestione OAuth e controllo di accesso basato sui ruoli (RBAC) e applica tale controllo di accesso a tutti i servizi nel gruppo di risorse. È possibile aggiungere utenti ai ruoli predefiniti specifici di piattaforma e risorsa e applicare tali ruoli a una sottoscrizione, a un gruppo di risorse o a una risorsa per limitare l'accesso. Ad esempio, è possibile sfruttare il ruolo predefinito denominato Collaboratore di DB SQL che consente agli utenti di gestire i database, ma non i server di database o i criteri di sicurezza. È possibile aggiungere utenti dell'organizzazione che richiedono questo tipo di accesso al ruolo Collaboratore DB SQL e assegnare il ruolo alla sottoscrizione, al gruppo di risorse o alla risorsa.

Gestione risorse di Azure registra automaticamente le azioni dell'utente ai fini del controllo. Per informazioni sull'utilizzo dei log di controllo, vedere [Operazioni di controllo con Gestione risorse](resource-group-audit.md).

Per ulteriori informazioni sul controllo di accesso basato sui ruoli, vedere [Controllo dell'accesso basato sui ruoli nel portale di anteprima di Microsoft Azure](role-based-access-control-configure.md). Questo argomento contiene un elenco dei ruoli predefiniti e delle azioni consentite. I ruoli predefiniti includono i ruoli generali, ad esempio Proprietario, Lettore e Collaboratore, oltre ai ruoli specifici del servizio come ad esempio Collaboratore Macchina virtuale, Collaboratore Rete virtuale, Gestore Sicurezza SQL, solo per citarne alcuni.

Per esempi di assegnazione di ruoli, vedere [Gestione dell'accesso alle risorse](resource-group-rbac.md).

È anche possibile bloccare in modo esplicito le risorse critiche per impedire agli utenti di eliminarle o modificarle. Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

Per le procedure consigliate, vedere [Considerazioni sulla sicurezza per Gestione risorse di Azure](best-practices-resource-manager-security.md)

## Gestire risorse con criteri personalizzati

Gestione risorse consente di creare criteri personalizzati per gestire le risorse. I tipi di criteri creati possono includere scenari, tra cui l'applicazione di una convenzione di denominazione delle risorse, la limitazione sulle aree che possono ospitare un tipo di risorsa o la richiesta di un valore di tag alle risorse per organizzare la fatturazione per reparto. Per altre informazioni, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

## Livello di gestione coerente

Gestione risorse fornisce operazioni completamente compatibili tramite Azure PowerShell, l'interfaccia della riga di comando di Azure per Mac, Linux e Windows, il portale di anteprima di Azure o l'API REST. È possibile utilizzare l'interfaccia più adatta alle proprie esigenze e spostarsi rapidamente tra le interfacce senza confusione. Nel portale vengono anche visualizzate notifiche per le azioni eseguite all'esterno del portale.

Per informazioni su PowerShell, vedere [Utilizzo di Azure PowerShell con Gestione risorse di Azure](./powershell-azure-resource-manager.md) e [Cmdlet di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Per informazioni sull'interfaccia della riga di comando di Azure, vedere [Utilizzo dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](./virtual-machines/xplat-cli-azure-resource-manager.md).

Per informazioni sull'API REST, vedere [Informazioni si riferimento sull'API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

Per altre informazioni sull'uso del portale di anteprima, vedere [Uso del portale di anteprima di Azure per gestire le risorse di Azure](azure-portal/resource-group-portal.md).


## Passaggi successivi

- Per altre informazioni sulla creazione di modelli, vedere [Creazione di modelli](./resource-group-authoring-templates.md).
- Per distribuire il modello creato, vedere [Distribuzione di modelli](resource-group-template-deploy.md)
- Per comprendere le funzioni che è possibile usare in un modello, vedere [Funzioni del modello](./resource-group-template-functions.md)
- Per informazioni aggiuntive sulla progettazione di modelli, vedere [Procedure consigliate per la progettazione di modelli di Gestione risorse di Azure](best-practices-resource-manager-design-templates.md)

Ecco una dimostrazione video di questa panoramica:

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=Nov15_HO2-->