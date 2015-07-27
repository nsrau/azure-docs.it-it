<properties
   pageTitle="Panoramica di Gestione risorse di Microsoft Azure"
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
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Panoramica di Gestione risorse di Microsoft Azure

Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Questi componenti non appaiono come entità separate, ma come parti correlate e interdipendenti di una singola entità e devono essere distribuite, gestite e monitorate come gruppo. Gestione risorse di Azure consente di lavorare con le risorse dell'applicazione come gruppo. È quindi possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione mediante un'unica operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, gestione temporanea e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo.

Gestione risorse di Azure integra in modo nativo il controllo degli accessi nella piattaforma di gestione per cui è possibile specificare le azioni che un utente nell'organizzazione può eseguire per un gruppo di risorse.

> [AZURE.NOTE]In questo argomento vengono descritte le risorse, i gruppi e i modelli utilizzando il portale di anteprima per illustrare i concetti. Tuttavia, è anche possibile creare, gestire ed eliminare le risorse di Azure tramite l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows](virtual-machines/xplat-cli-azure-resource-manager.md) e [PowerShell](powershell-azure-resource-manager.md).

## Gruppi di risorse

Un gruppo di risorse è un contenitore che contiene risorse correlate per un'applicazione. Il gruppo di risorse potrebbe includere tutte le risorse per un'applicazione o solo le risorse raggruppate logicamente. È possibile decidere come si desidera allocare le risorse a gruppi di risorse nel modo appropriato per l'organizzazione.

Esistono alcuni fattori importanti da considerare quando si definisce il gruppo di risorse:

1. Tutte le risorse del gruppo devono condividere lo stesso ciclo di vita. Le risorse verranno distribuite, aggiornate ed eliminate insieme. Se una risorsa, ad esempio un server di database, deve esistere in un ciclo di distribuzione diverso deve essere inclusa in un altro gruppo di risorse.
2. Ogni risorsa può appartenere a un solo gruppo di risorse.
3. È possibile aggiungere o rimuovere una risorsa in un gruppo di risorse in qualsiasi momento.
4. Un gruppo di risorse può contenere le risorse che risiedono in aree diverse.
5. Un gruppo di risorse consente di definire l'ambito di controllo di accesso per operazioni amministrative.

Nel portale di anteprima di Azure, tutte le nuove risorse vengono create in un gruppo di risorse. Anche se si crea un'unica risorsa, ad esempio un sito Web, è necessario decidere se aggiungere tale risorsa a un gruppo esistente o creare un nuovo gruppo per tale risorsa.

Nell'immagine seguente viene illustrato un gruppo di risorse con un sito Web, un database e Application Insights.

![riepilogo del gruppo di risorse](./media/resource-group-overview/resourcegroupsummary.png)

Un gruppo di risorse può inoltre essere collegato a una risorsa in un altro gruppo di risorse. Una risorsa viene considerata collegata quando esiste una dipendenza di distribuzione tra le risorse in diversi gruppi di risorse. Ad esempio, se un'app Web in un gruppo di risorse si connette al database in un altro gruppo di risorse, tali risorse sono collegate.

![risorsa collegata](./media/resource-group-overview/linkedresource.png)

Dal portale di anteprima è facile visualizzare i costi, monitorare gli eventi e gestire gli avvisi. Nell'immagine seguente viene illustrata la fatturazione consolidata per un gruppo.

![fatturazione](./media/resource-group-overview/billing.png)

## Distribuzione del modello

Con Gestione risorse di Azure, è possibile creare un modello semplice (in formato JSON) che definisce la distribuzione e la configurazione dell'applicazione. Questo modello è noto come modello di Azure e consente di definire la distribuzione in modo dichiarativo. Utilizzando un modello, è possibile distribuire l'applicazione in tutto il ciclo di vita dell'app ripetutamente e avere la certezza che le risorse vengano distribuite in uno stato coerente.

All'interno del modello, è possibile definire l'infrastruttura per l'applicazione, come configurare tale infrastruttura e come pubblicare il codice dell'app in tale infrastruttura. Non è necessario preoccuparsi dell'ordine per la distribuzione perché Gestione risorse di Azure analizza le dipendenze per assicurarsi che le risorse vengano create nell'ordine corretto.

Inoltre, è possibile utilizzare il modello per gli aggiornamenti all'infrastruttura. Ad esempio, è possibile aggiungere una nuova risorsa per l'applicazione e aggiungere le regole di configurazione per le risorse già distribuite. Se il modello specifica la creazione di una nuova risorsa, ma tale risorsa esiste già, Gestione risorse di Azure esegue un aggiornamento anziché creare un nuovo asset. Gestione risorse di Azure aggiorna l'asset esistente allo stesso stato di quelli nuovi.

È possibile specificare parametri nel modello per consentire la personalizzazione e la flessibilità nella distribuzione. Ad esempio, è possibile passare i valori dei parametri che consentono di personalizzare la distribuzione nell'ambiente di prova. Specificando i parametri, è possibile utilizzare lo stesso modello per la distribuzione in tutti gli ambienti dell'app.

È possibile eseguire tutti i passaggi necessari per la distribuzione e la configurazione tramite il modello e non è necessario eseguire altre operazioni manualmente. Gestione risorse di Azure fornisce estensioni per gli scenari quando sono necessarie operazioni aggiuntive, ad esempio l'installazione di software specifico non incluso nel programma di installazione. Se si utilizza già un servizio di gestione configurazione, ad esempio DSC, Chef o Puppet, è possibile continuare a usare il servizio utilizzando le estensioni.

Quando si crea una soluzione da Marketplace, la soluzione include automaticamente un modello di distribuzione. Non è necessario creare un modello da zero perché è possibile iniziare con il modello per la soluzione e personalizzarlo per soddisfare esigenze specifiche.

Infine, il modello diventa parte del codice sorgente per l'applicazione. È possibile archiviarlo nel repository del codice sorgente e aggiornarlo con l'evoluzione dell'applicazione. È possibile modificare il modello tramite Visual Studio.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md).

Per gli schemi del modello, vedere [Schemi di Gestione risorse di Azure](https://github.com/Azure/azure-resource-manager-schemas).

Per informazioni sull'utilizzo di un modello per la distribuzione, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](azure-portal/resource-group-template-deploy.md) e [Distribuire un'applicazione complessa in modo prevedibile in Azure](app-service-web/app-service-deploy-complex-application-predictably.md).

## Tag

Gestione risorse di Azure offre una funzionalità di assegnazione di tag che consente di classificare le risorse in base ai requisiti di gestione o fatturazione. È possibile utilizzare i tag quando si dispone di un insieme complesso di gruppi di risorse e risorse ed è necessario visualizzare tali risorse in modo più significativo per l'utente. Ad esempio, è possibile contrassegnare le risorse che svolgono un ruolo simile nell'organizzazione o che appartengono allo stesso reparto.

Nel portale di anteprima, è possibile iniziare a utilizzare i tag facendo clic sull'icona del tag.

![tags](./media/resource-group-overview/tags.png)

Non è necessario che le risorse si trovino nello stesso gruppo di risorse per condividere un tag. È possibile creare una propria tassonomia di tag per assicurarsi che tutti gli utenti dell'organizzazione utilizzino tag comuni anziché applichino inavvertitamente tag leggermente diversi (ad esempio "dept" anziché "department").

Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](./resource-group-using-tags.md).

## Controllo dell’accesso

Gestione risorse di Azure consente di controllare chi ha accesso alle azioni specifiche per l'organizzazione. In modo nativo, integra la piattaforma di gestione OAuth e controllo di accesso basato sui ruoli (RBAC) e applica tale controllo di accesso a tutti i servizi nel gruppo di risorse. È possibile aggiungere utenti ai ruoli predefiniti specifici di piattaforma e risorsa e applicare tali ruoli a una sottoscrizione, a un gruppo di risorse o a una risorsa per limitare l'accesso. Ad esempio, è possibile sfruttare il ruolo predefinito denominato Collaboratore di DB SQL che consente agli utenti di gestire i database, ma non i server di database o i criteri di sicurezza. È possibile aggiungere utenti dell'organizzazione che richiedono questo tipo di accesso al ruolo Collaboratore DB SQL e assegnare il ruolo alla sottoscrizione, al gruppo di risorse o alla risorsa.

Nel portale di anteprima, è possibile definire il controllo di accesso facendo clic sul pulsante di accesso.

![controllo di accesso dell’utente](./media/resource-group-overview/access.png)

Gestione risorse di Azure registra automaticamente le azioni dell'utente per il controllo.

È anche possibile bloccare in modo esplicito le risorse critiche per impedire agli utenti di eliminarle o modificarle.

Per ulteriori informazioni sul controllo di accesso basato sui ruoli, vedere [Controllo dell'accesso basato sui ruoli nel portale di anteprima di Microsoft Azure](./role-based-access-control-configure.md).

Per esempi di impostazione dei criteri di accesso, vedere [Gestione e controllo di accesso alle risorse](azure-portal/resource-group-rbac.md).

## Livello di gestione coerente

Gestione risorse di Azure fornisce operazioni completamente compatibili tramite Azure PowerShell, l’interfaccia della riga di comando di Azure per Mac, Linux e Windows, il portale di Azure o l'API REST. È possibile utilizzare l'interfaccia più adatta alle proprie esigenze e spostarsi rapidamente tra le interfacce senza confusione. Nel portale vengono anche visualizzate notifiche per le azioni eseguite all'esterno del portale.

Per informazioni su PowerShell, vedere [Utilizzo di Azure PowerShell con Gestione risorse di Azure](./powershell-azure-resource-manager.md) e [Cmdlet di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Per informazioni sull'interfaccia della riga di comando di Azure, vedere [Utilizzo dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](./virtual-machines/xplat-cli-azure-resource-manager.md).

Per informazioni sull'API REST, vedere [Informazioni si riferimento sull'API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Passaggi successivi
Introduzione

- [Utilizzo di Azure PowerShell con Gestione risorse](./powershell-azure-resource-manager.md)
- [Utilizzo dell'interfaccia della riga di comando di Azure con Gestione risorse](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Utilizzo del portale di Azure per gestire le risorse](azure-portal/resource-group-portal.md)

Creazione e distribuzione delle applicazioni

- [Creazione di modelli](./resource-group-authoring-templates.md)
- [Distribuzione di modelli](azure-portal/resource-group-template-deploy.md)
- [Risoluzione dei problemi delle distribuzioni](virtual-machines/resource-group-deploy-debug.md)
- [Distribuire un'applicazione complessa in modo prevedibile in Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Distribuire le risorse usando le librerie .NET e un modello](virtual-machines/arm-template-deployment.md)
- [Funzioni di modello](./resource-group-template-functions.md)
- [Operazioni avanzate con i modelli](./resource-group-advanced-template.md)
- [Schemi del modello](https://github.com/Azure/azure-resource-manager-schemas)

Organizzazione delle risorse

- [Utilizzo dei tag per organizzare le risorse](./resource-group-using-tags.md)

Gestione e controllo dell'accesso

- [Gestione e controllo dell'accesso alle risorse](azure-portal/resource-group-rbac.md)
- [Controllo degli accessi in base al ruolo nel portale di anteprima](./role-based-access-control-configure.md)
- [Autenticazione di un'entità servizio](./resource-group-authenticate-service-principal.md)
- [Creare una nuova entità servizio utilizzando il portale di Azure](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO3-->