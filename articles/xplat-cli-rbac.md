<properties pageTitle="Managing Role-Based Access Control with Azure Cross-Platform Command-Line Interface" metaKeywords="ResourceManager, Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli, RBAC" description="Managing role-based access control with cross-platform command-line interface" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="mohisri" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Gestione del controllo degli accessi in base al ruolo con l'interfaccia della riga di comando multipiattaforma

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/it-it/documentation/articles/xplat-cli-rbac.md" title="Interfaccia della riga di comando multipiattaforma">Interfaccia della riga di comando multipiattaforma</a></div>

Nel portale di anteprima Azure e nell'API di Gestione risorse di Azure, il controllo degli accessi in base al ruolo (RBAC) consente di gestire con estrema precisione l'accesso alla propria sottoscrizione. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

In questa esercitazione verrà illustrato come utilizzare l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) per la gestione di RBAC. Verrà inoltre descritto il processo di creazione e controllo delle assegnazioni dei ruoli.

**Tempo previsto per il completamento:** 15 minuti

## Prerequisiti

Per utilizzare xplat-cli per la gestione del controllo degli accessi in base al ruolo, è necessario disporre dei programmi seguenti:

-   Azure Cross-Platform Command-Line Interface versione 0.8.8 o successiva. Per installare l'ultima versione e associarla alla sottoscrizione Azure, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure][].
-   Si consiglia di leggere anche le seguenti esercitazioni per acquisire familiarità con la configurazione e l'utilizzo di Gestione risorse di Azure nell'interfaccia della riga di comando multipiattaforma di Azure: [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse][]

## Contenuto dell'esercitazione:

-   [Connessione alle sottoscrizioni][]
-   [Controllo delle assegnazioni di ruoli esistenti][]
-   [Creazione di un'assegnazione di ruolo][]
-   [Verifica delle autorizzazioni][]
-   [Passaggi successivi][]

## <span id="connect"></span></a>Connessione alle sottoscrizioni

Poiché RBAC funziona solo con Gestione risorse di Azure, occorre innanzitutto passare alla modalità Gestione risorse di Azure digitando:

    azure config mode arm

Per ulteriori informazioni, vedere [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse][].

Per connettersi alle proprie sottoscrizioni di Azure, digitare:

    azure login -u <username>

Nel prompt della riga di comando, immettere la password dell'account Azure (utilizzare solo un ID aziendale di supporto). Xplat-cli recupererà tutte le sottoscrizioni di cui si dispone con questo account e utilizzerà la prima come predefinita. Si noti che con RBAC sarà possibile ottenere le sottoscrizioni solo se si dispone di alcune autorizzazioni, sia come coamministratori che come aventi assegnazioni di ruolo.

Se si dispone di più sottoscrizioni e si desidera passare a un'altra, digitare:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Per ulteriori informazioni, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure][].

## <span id="check"></span></a>Controllo delle assegnazioni di ruoli esistenti

Controlliamo ora quali assegnazioni di ruoli esistono già nella sottoscrizione. Digitare:

    azure role assignment list

Verranno restituite tutte le assegnazioni di ruoli nella sottoscrizione. Si notino due cose:

1.  È necessario disporre dell'accesso in lettura a livello di sottoscrizione.
2.  Se la sottoscrizione conta numerose assegnazioni di ruoli, recuperarle tutte potrebbe richiedere qualche tempo.

È possibile inoltre controllare le assegnazioni di ruoli esistenti per una determinata definizione di ruolo, in un dato ambito e per un utente specifico. Digitare:

    azure role assignment list -g group1 --mail <user's email> -o Owner

Verranno restituite tutte le assegnazioni di ruolo per un determinato utente nel tenant di AD con assegnazione del ruolo "Owner" per il gruppo di risorse "group1". L'assegnazione del ruolo può avere due origini:

1.  Un'assegnazione del ruolo di "Owner" all'utente per il gruppo di risorse.
2.  Un'assegnazione del ruolo "Owner" all'utente per l'entità principale del gruppo di risorse (in questo caso, la sottoscrizione), poiché se si dispone di un'autorizzazione per un determinato livello si avranno le stesse autorizzazioni anche per tutti i relativi elementi secondari.

Tutti i parametri di questo cmdlet sono facoltativi. È possibile combinarli per controllare le assegnazioni dei ruoli con filtri diversi.

## <span id="create"></span></a>Creazione di un'assegnazione di ruolo

Per creare un'assegnazione di ruolo è necessario prendere in considerazione quanto segue

-   A chi assegnare il ruolo: è possibile utilizzare i seguenti cmdlet di Azure Active Directory per vedere quali utenti, gruppi ed entità di servizio sono presenti nel tenant di AD.

    `azure ad user list azure ad user show azure ad group list azure ad group show azure ad group member list azure sp list azure sp show`

-   Quale ruolo assegnare: è possibile utilizzare il seguente cmdlet per vedere le definizioni di ruolo supportate.

    `azure role list`

-   Quale ambito assegnare: gli ambiti sono riconducibili a tre livelli

    -   La sottoscrizione corrente
    -   Un gruppo di risorse: per ottenere un elenco di gruppi di risorse, digitare `azure group list`
    -   Una risorsa: per ottenere un elenco di risorse, digitare `azure resource list`

Utilizzare quindi `azure role assignment create` per creare un'assegnazione di ruolo. Ad esempio:

-   In questo modo verrà creata per un utente un'assegnazione di ruolo al livello della sottoscrizione attuale come lettore.

    `azure role assignment create --mail <user's email> -o Reader`

-   In questo modo verrà creata un'assegnazione di ruolo a livello di gruppo di risorse

    `PS C:PS C:\> azure role assignment create --mail <user's email> -o Contributor -g group1`gt; azure role assignment create --mail \<user's email\> -o Contributor -g group1</code>

-   In questo modo verrà creata un'assegnazione di ruolo a livello di risorsa

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <span id="verify"></span></a>Verifica delle autorizzazioni

Dopo aver controllato che l'account disponga di alcune assegnazioni di ruoli, è possibile visualizzare le autorizzazioni accordate da tali assegnazioni di ruoli eseguendo

    PS C:\> azure group list
    PS C:\> azure resource list

Questi due cmdlet restituiranno solo i gruppi di risorse o le risorse in cui si dispone dell'autorizzazione di lettura. Inoltre, verranno visualizzate anche le autorizzazioni di cui si dispone.

Quando si tenterà di eseguire un altro cmdlet, ad esempio `azure group create`, verrà restituito un errore di accesso negato se non si dispone dell'autorizzazione necessaria.

## <span id="next"></span></a>Passaggi successivi

Per ulteriori informazioni sulla gestione del controllo degli accessi in base al ruolo con xplat-cli e per gli argomenti correlati:

-   [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure][]
-   [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse][]
-   [Utilizzo dei gruppi di risorse per la gestione delle risorse di Azure][]: per informazioni su come creare e gestire i gruppi di risorse nel portale di gestione di Azure.
-   [Blog di Azure][]: informazioni sulle nuove funzionalità di Azure.

  [Windows PowerShell]: /it-it/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [Interfaccia della riga di comando multipiattaforma]: /it-it/documentation/articles/xplat-cli-rbac.md "Interfaccia della riga di comando multipiattaforma"
  [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure]: http://azure.microsoft.com/it-it/documentation/articles/xplat-cli/
  [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse]: http://azure.microsoft.com/it-it/documentation/articles/xplat-cli-azure-resource-manager/
  [Connessione alle sottoscrizioni]: #connect
  [Controllo delle assegnazioni di ruoli esistenti]: #check
  [Creazione di un'assegnazione di ruolo]: #create
  [Verifica delle autorizzazioni]: #verify
  [Passaggi successivi]: #next
  [Utilizzo dei gruppi di risorse per la gestione delle risorse di Azure]: http://azure.microsoft.com/it-it/documentation/articles/azure-preview-portal-using-resource-groups
  [Blog di Azure]: http://blogs.msdn.com/windowsazure
