<properties
	pageTitle="Gestione del controllo di accesso basato sui ruoli con l'interfaccia della riga di comando di Azure"
	description="Gestione del controllo di accesso basato sui ruoli con l'interfaccia della riga di comando di Azure"
	services="active-directory"
	documentationCenter="na"
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="inhenk"/>

# Gestione del controllo di accesso basato sui ruoli con l'interfaccia della riga di comando di Azure (Azure CLI) #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](/role-based-access-control-xplat-cli-install.md)

Il controllo di accesso basato sui ruoli nel portale di Azure e nell'API Gestione risorse di Azure consente di gestire l'accesso alle proprie sottoscrizioni e risorse a un livello estremamente dettagliato. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

In questa esercitazione verrà illustrato come usare l'interfaccia della riga di comando di Azure per gestire il controllo di accesso basato sui ruoli. Verrà inoltre descritto il processo di creazione e controllo delle assegnazioni dei ruoli.

**Tempo previsto per il completamento:** 15 minuti

## Prerequisiti ##

Per usare l'interfaccia della riga di comando di Azure per gestire il controllo degli accessi in base al ruolo, è necessario disporre dei programmi seguenti:

- Usare la versione 0.8.8 o successiva dell'interfaccia della riga di comando di Azure. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
- Si consiglia di leggere anche le seguenti esercitazioni per acquisire familiarità con la configurazione e l'uso di Gestione risorse di Azure nell'interfaccia della riga di comando di Azure: [Uso dell'interfaccia della riga di comando di Azure con Gestione risorse](../xplat-cli-azure-resource-manager.md)

## Contenuto dell'esercitazione: ##

* [Connettersi alle sottoscrizioni](#connect)
* [Controllare le assegnazioni di ruoli esistenti](#check)
* [Creare un'assegnazione di ruolo](#create)
* [Verificare le autorizzazioni](#verify)
* [Passaggi successivi](#next)

## <a id="connect"></a>Connettersi alle sottoscrizioni ##

Poiché il controllo di accesso basato sui ruoli funziona solo con Gestione risorse di Azure, la prima cosa da fare è passare alla modalità Gestione risorse di Azure. Digitare:

    azure config mode arm

Per maggiori informazioni, vedere [Utilizzo dell'interfaccia della riga di comando di Azure con Gestione risorse](../xplat-cli-azure-resource-manager.md)

Per connettersi alle sottoscrizioni Azure, digitare:

    azure login -u <username>

Nel prompt della riga di comando immettere la password dell'account Azure (usare solo un account aziendale). L'interfaccia della riga di comando di Azure recupererà tutte le sottoscrizioni di cui si dispone con questo account e userà la prima come predefinita. Si noti che con il controllo di accesso basato sui ruoli, sarà possibile recuperare solo le sottoscrizioni per cui si disponga di alcune autorizzazioni, ad esempio essendo coamministratore o avendo dei ruoli assegnati.

Se si dispone di più sottoscrizioni e si desidera passare a un'altra, digitare:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Per maggiori informazioni, consultare [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

## <a id="check"></a>Controllare le assegnazioni di ruoli esistenti ##

Controllare ora quali assegnazioni di ruoli esistono già nella sottoscrizione. Digitare:

    azure role assignment list

Verranno restituite tutte le assegnazioni di ruoli nella sottoscrizione. Si notino due cose:

1. È necessario disporre dell'accesso in lettura a livello di sottoscrizione.
2. Se la sottoscrizione contiene molte assegnazioni di ruoli, recuperarle tutte potrebbe richiedere alcuni minuti.

È inoltre possibile controllare le assegnazioni di ruoli esistenti per una determinata definizione di ruolo, in un determinato ambito e per un determinato utente. Digitare:

    azure role assignment list -g group1 --upn <user email> -o Owner

Verranno restituite tutte le assegnazioni di ruoli per un determinato utente nella directory di Azure AD, con un'assegnazione del ruolo di proprietario ("Owner") per il gruppo di risorse "group1". L'assegnazione del ruolo può avere due origini:

1. Un'assegnazione del ruolo di "Owner" all'utente per il gruppo di risorse.
2. Un'assegnazione del ruolo di proprietario all'utente per la risorsa padre del gruppo di risorse (in questo caso, la sottoscrizione), poiché se si dispone di qualsiasi autorizzazione in una determinata risorsa padre, si disporrà delle stesse autorizzazioni nelle relative risorse figlio.

Tutti i parametri di questo cmdlet sono facoltativi. È possibile combinarli per controllare le assegnazioni dei ruoli con filtri diversi.

## <a id="create"></a>Creare un'assegnazione di ruolo ##

Per creare un'assegnazione di ruolo, è necessario considerare quanto segue:

- A chi assegnare il ruolo: è possibile usare i seguenti cmdlet di Azure Active Directory per vedere quali utenti, gruppi ed entità di servizio sono presenti nella directory.

    ```
    azure ad user list  
    azure ad user show  
    azure ad group list  
    azure ad group show  
    azure ad group member list  
    azure ad sp list  
    azure ad sp show  
    ```

- Quale ruolo assegnare: è possibile usare il cmdlet seguente per vedere le definizioni di ruolo supportate.

    `azure role list`

- Quale ambito assegnare: gli ambiti sono riconducibili a tre livelli:

    - La sottoscrizione corrente
    - Un gruppo di risorse. Per recuperare un elenco di gruppi di risorse, digitare `azure group list`
    - Una risorsa. Per recuperare un elenco di risorse, digitare `azure resource list`

Usare quindi `azure role assignment create` per creare un'assegnazione di ruolo. Ad esempio:

 - Questo comando creerà un'assegnazione di ruolo al livello della sottoscrizione corrente per un utente come lettore:

    `azure role assignment create --upn <user's email> -o Reader`

- Questo comando creerà un'assegnazione di ruolo a livello di gruppo di risorse:

    `PS C:\> azure role assignment create --upn <user's email> -o Contributor -g group1`

- Questo comando creerà un'assegnazione di ruolo a livello di risorsa:

    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>Verificare le autorizzazioni ##

Dopo aver controllato che il proprio account disponga di alcune assegnazioni di ruoli, è possibile visualizzare le autorizzazioni concesse da tali assegnazioni eseguendo:

    PS C:\> azure group list
    PS C:\> azure resource list

Questi due cmdlet restituiranno solo i gruppi di risorse o le risorse in cui si dispone dell'autorizzazione di lettura. Inoltre, verranno visualizzate anche le autorizzazioni di cui si dispone.

Quando si tenta di eseguire altri cmdlet come `azure group create`, si riceverà un errore di accesso negato se non si dispone dell'autorizzazione adeguata.

## <a id="next"></a>Passaggi successivi ##

Per altre informazioni sulla gestione del controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure e per gli argomenti correlati:

- [Controllo di accesso basato sui ruoli in Microsoft Azure](../role-based-access-control-configure.md)
- [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)
- [Uso dell'interfaccia della riga di comando di Azure con Gestione risorse](../xplat-cli-azure-resource-manager.md)
- [Uso dei gruppi di risorse per la gestione delle risorse di Azure](../azure-preview-portal-using-resource-groups.md): informazioni su come creare e gestire gruppi di risorse nel portale di gestione di Azure.
- [Blog di Azure](http://blogs.msdn.com/windowsazure): informazioni sulle nuove funzionalità di Azure.
- [Configurazione del controllo di accesso basato sui ruoli con Windows PowerShell](role-based-access-control-powershell.md)
- [Risoluzione dei problemi relativi al controllo di accesso basato sui ruoli](role-based-access-control-troubleshooting.md)

<!---HONumber=AcomDC_0107_2016-->