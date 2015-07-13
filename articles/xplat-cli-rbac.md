<properties
	pageTitle="Gestione del controllo di accesso basato sui ruoli con l'interfaccia della riga di comando di Azure (Azure CLI)"
	description="Gestione del controllo di accesso basato sui ruoli con XPLAT CLI"
	services=""
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tomfitz"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2015"
	ms.author="tomfitz"/>

# Gestione del controllo di accesso basato sui ruoli con l’interfaccia della riga di comando di Azure (Azure CLI)#

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/documentation/articles/xplat-cli-rbac.md" title="Interfaccia della riga di comando di Azure">Interfaccia della riga di comando di Azure</a></div>

Nel portale di Azure e nell'API di Gestione risorse di Azure, il controllo di accesso basato sui ruoli (RBAC) consente di gestire con estrema precisione l'accesso alla propria sottoscrizione. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

In questa esercitazione vengono fornite informazioni sull'utilizzo dell’interfaccia della riga di comando di Azure per la gestione di RBAC. Verrà inoltre descritto il processo di creazione e controllo delle assegnazioni dei ruoli.

**Tempo previsto per il completamento:** 15 minuti

## Prerequisiti ##

Per usare l'interfaccia della riga di comando di Azure per gestire il controllo degli accessi in base al ruolo, è necessario disporre dei programmi seguenti:

- Usare la versione 0.8.8 o successiva dell'interfaccia della riga di comando di Azure. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Installare](xplat-cli-install.md).
- Si consiglia di leggere anche le seguenti esercitazioni per acquisire familiarità con la configurazione e l'uso di Gestione risorse di Azure nell'interfaccia della riga di comando di Azure: [Uso dell'interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md)

## Contenuto dell'esercitazione: ##

* [Connettersi alle sottoscrizioni](#connect)
* [Controllare le assegnazioni di ruoli esistenti](#check)
* [Creare un'assegnazione di ruolo](#create)
* [Verificare le autorizzazioni](#verify)
* [Passaggi successivi](#next)

## <a id="connect"></a>Connettersi alle sottoscrizioni ##

Poiché RBAC funziona solo con Gestione risorse di Azure, occorre innanzitutto passare alla modalità Gestione risorse di Azure digitando:

    azure config mode arm

Per maggiori informazioni, vedere [Utilizzo dell'interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md)

Per connettersi alle proprie sottoscrizioni di Azure, digitare:

    azure login -u <username>

Nel prompt della riga di comando, immettere la password dell'account Azure (usare solo un ID di lavoro o scolastico di supporto - detto anche **ID aziendale **). L’interfaccia della riga di comando di Azure recupererà tutte le sottoscrizioni di cui si dispone con questo account e utilizzerà la prima come predefinita. Si noti che con RBAC sarà possibile ottenere le sottoscrizioni solo se si dispone di alcune autorizzazioni, sia come coamministratori che come aventi assegnazioni di ruolo.

Se si dispone di più sottoscrizioni e si desidera passare a un'altra, digitare:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Per maggiori informazioni, consultare [i comandi dell’interfaccia della riga di comando di Azure](azure-cli-arm-commands.md).

## <a id="check"></a>Controllare le assegnazioni di ruoli esistenti ##

Controllare ora quali assegnazioni di ruoli esistono già nella sottoscrizione. Digitare:

    azure role assignment list

Verranno restituite tutte le assegnazioni di ruoli nella sottoscrizione. Si notino due cose:

1. È necessario disporre dell'accesso in lettura a livello di sottoscrizione.
2. Se la sottoscrizione conta numerose assegnazioni di ruoli, recuperarle tutte potrebbe richiedere qualche tempo.

È possibile inoltre controllare le assegnazioni di ruoli esistenti per una determinata definizione di ruolo, in un dato ambito e per un utente specifico. Digitare:

    azure role assignment list -g group1 --mail <user's email> -o Owner

Verranno restituite tutte le assegnazioni di ruolo per un determinato utente nel tenant di AD con assegnazione del ruolo "Owner" per il gruppo di risorse "group1". L'assegnazione del ruolo può avere due origini:

1. Un'assegnazione del ruolo di "Owner" all'utente per il gruppo di risorse.
2. Un'assegnazione del ruolo "Owner" all'utente per l'entità principale del gruppo di risorse (in questo caso, la sottoscrizione), poiché se si dispone di un'autorizzazione per un determinato livello si avranno le stesse autorizzazioni anche per tutti i relativi elementi secondari.

Tutti i parametri di questo cmdlet sono facoltativi. È possibile combinarli per controllare le assegnazioni dei ruoli con filtri diversi.

## <a id="create"></a>Creare un'assegnazione di ruolo ##

Per creare un'assegnazione di ruolo è necessario prendere in considerazione quanto segue

- A chi assegnare il ruolo: è possibile usare i seguenti cmdlet di Azure Active Directory per vedere quali utenti, gruppi ed entità di servizio sono presenti nel tenant di AD.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

- Quale ruolo assegnare: è possibile usare il cmdlet seguente per vedere le definizioni di ruolo supportate.

    `azure role list`

- Quale ambito assegnare: gli ambiti sono riconducibili a tre livelli

    - La sottoscrizione corrente
    - Un gruppo di risorse per ottenere un elenco di gruppi di risorse, digitare `azure group list`
    - Una risorsa per ottenere un elenco di risorse, digitare `azure resource list`

Usare quindi `azure role assignment create` per creare un'assegnazione di ruolo. Ad esempio:

 - Questo comando creerà per un utente un'assegnazione di ruolo come lettore al livello della sottoscrizione corrente.

    `azure role assignment create --mail <user's email> -o Reader`

- In questo modo verrà creata un'assegnazione di ruolo a livello di gruppo di risorse

    `PS C:\> azure role assignment create --mail <user's email> -o Contributor -g group1`

- In questo modo verrà creata un'assegnazione di ruolo a livello di risorsa

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>Verificare le autorizzazioni ##

Dopo aver controllato che l'account disponga di alcune assegnazioni di ruoli, è possibile visualizzare le autorizzazioni accordate da tali assegnazioni di ruoli eseguendo

    PS C:\> azure group list
    PS C:\> azure resource list

Questi due cmdlet restituiranno solo i gruppi di risorse o le risorse in cui si dispone dell'autorizzazione di lettura. Inoltre, verranno visualizzate anche le autorizzazioni di cui si dispone.

Quando si proverà a eseguire un altro cmdlet, ad esempio `azure group create`, verrà restituito un errore di accesso negato se non si dispone dell'autorizzazione necessaria.

## <a id="next"></a>Passaggi successivi ##

Per altre informazioni sulla gestione del controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure e per gli argomenti correlati:

- [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli-install.md)
- [Uso dell'interfaccia della riga di comando di Azure con Gestione risorse](xplat-cli-azure-resource-manager.md)
- [Uso dei gruppi di risorse per la gestione delle risorse di Azure](resource-groups-overview.md): informazioni su come creare e gestire gruppi di risorse nel portale di gestione di Azure.

<!---HONumber=July15_HO1-->