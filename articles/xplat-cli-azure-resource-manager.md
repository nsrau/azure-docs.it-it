<properties linkid="script-xplat-intro" urlDisplayName="Microsoft Azure Cross-Platform Command-Line Interface" pageTitle="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" title="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaKeywords="windows azure cross-platform command-line interface Resource Manager, windows azure command-line resource manager, azure command-line resource manager, azure cli resource manager" description="Use the Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaCanonical="http://www.windowsazure.com/it-it/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="larryfr" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/it-it/documentation/articles/xplat-cli-azure-resource-manager.md" title="Cross-Platform CLI" class="current">CLI multipiattaforma</a></div>

È stata recentemente presentata l'anteprima di Gestione risorse, un nuovo modo di gestire Microsoft Azure. In questo articolo verrà illustrato come usare l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) in modo che funzioni con Gestione risorse.

> [WACOM.NOTE] Gestione risorse è attualmente in versione di anteprima e non fornisce lo stesso livello delle funzionalità di gestione disponibili con Gestione servizi di Azure.

> [WACOM.NOTE] Se non si è ancora installato e configurato xplat-cli, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure][Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure] per altre informazioni sull'installazione, la configurazione e l'uso di xplat-cli.

## Gestione risorse

Gestione risorse consente di gestire un gruppo di *risorse* (entità gestite dall'utente quali un server di database, un database o un sito Web) come una singola unità logica o *gruppo di risorse*. Un gruppo di risorse potrebbe ad esempio contenere risorse corrispondenti a un sito Web e a un database SQL.

Per supportare una modalità più dichiarativa per descrivere le modifiche alle risorse all'interno di un gruppo di risorse, Gestione risorse utilizza dei *modelli*, che corrispondono a documenti JSON. Il linguaggio del modello consente inoltre di descrivere parametri che possono essere compilati inline quando si esegue un comando oppure archiviati in un file JSON a parte. Ciò consente di creare facilmente nuove risorse usando lo stesso modello e semplicemente fornendo parametri diversi. Un modello che crea un sito Web disporrà ad esempio di parametri per il nome del sito, per l'area in cui verrà collocato e per altri parametri comuni.

> [WACOM.NOTE] Le specifiche del linguaggio dei modelli non sono al momento documentate. Quando la documentazione verrà resa disponibile, questo argomento verrà aggiornato con l'aggiunta di un collegamento a tale documentazione di riferimento.
>
> È possibile, tuttavia, usare il comando `azure group template download` per scaricare dalla raccolta modelli i modelli forniti da Microsoft e dai partner e quindi modificarli.

Quando un modello viene usato per modificare o creare un gruppo, viene creata una *distribuzione*, che viene quindi applicata al gruppo.

## Autenticazione

Attualmente, l'uso di Gestione risorse tramite xplat-cli richiede l'autenticazione a Microsoft Azure con un account aziendale. L'autenticazione tramite un account Microsoft o un certificato installato mediante un file .publishsettings non è supportata.

Per altre informazioni sull'autenticazione tramite un account aziendale, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure][Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure].

## Utilizzo di gruppi e modelli

1.  Gestione risorse è attualmente offerto in anteprima e i comandi xplat-cli per il suo funzionamento sono disabilitati per impostazione predefinita. Per abilitare tali comandi, usare il comando seguente.

        azure config mode arm

    > [WACOM.NOTE] La modalità Gestione risorse e la modalità Gestione servizi di Azure si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.

2.  Quando si lavora con i modelli, è possibile crearne uno personalizzato oppure utilizzarne uno dalla raccolta modelli. Per ottenere un elenco dei modelli disponibili nella raccolta, usare il comando seguente.

        azure group template list

    Nella risposta verranno elencati l'autore e il nome del modello, in una visualizzazione analoga alla seguente.

        data:    Publisher               Name
        data:    ----------------------------------------------------------------------------
        data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
        data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
        data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
        data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
        data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3.  Per visualizzare i dettagli di un modello che creerà un sito Web di Azure, usare il comando seguente.

        azure group template show Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    Verranno restituite informazioni descrittive sul modello.

4.  Dopo avere selezionato un modello, è possibile scaricarlo usando il comando seguente.

        azure group template download Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    Dopo avere scaricato un modello, è possibile personalizzarlo per meglio adattarlo alle proprie esigenze, ad esempio aggiungendovi una nuova risorsa.

    > [WACOM.NOTE] Se si modifica il modello, usare il comando `azure group template validate` per convalidarlo prima di usarlo per creare o modificare un gruppo di risorse esistente.

5.  Aprire il file di modello in un editor di testo. Notare la raccolta **parameters** nella parte iniziale. Questa contiene un elenco dei parametri previsti da questo modello per poter creare le risorse descritte dal modello stesso. Alcuni parametri, come **sku**, presentano valori predefiniti, mentre altri specificano semplicemente il tipo di valore, ad esempio **siteName**. Quando si utilizza un modello, è possibile specificare parametri insieme ai parametri della riga di comando oppure specificando un file contenente i valori desiderati. In ogni caso, i parametri devono essere in formato JSON.

    Per creare un file che contenga i parametri per il modello Microsoft.WebSiteSQLDatabase.0.1.0-preview1, usare i dati seguenti e creare un file denominato **params.json**. Sostituire i valori che iniziano per **My**, ad esempio **MyWebSite**, con i valori desiderati. **siteLocation** deve specificare un'area di Azure, ad esempio **North Europe** o **South Central US**.

        {
          "siteName": {
            "value": "MyWebSite"
          },
          "hostingPlanName": {
            "value": "MyHostingPlan"
          },
          "siteLocation": {
            "value": "North Europe"
          },
          "serverName": {
            "value": "MySQLServer"
          },
          "serverLocation": {
            "value": "North Europe"
          },
          "administratorLogin": {
            "value": "MySQLAdmin"
          },
          "administratorLoginPassword": {
            "value": "MySQLAdminPassword"
          },
          "databaseName": {
            "value": "MySQLDB"
          }
        }

6.  Dopo avere salvato il file **params.json**, usare il comando seguente per creare un nuovo gruppo di risorse in base al modello. Il parametro `-e` specifica il file **params.json** creato nel passaggio precedente.

        azure group create MyGroupName "MyDataCenter" -y Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -d MyDeployment -e params.json

    Sostituire **MyGroupName** con il nome del gruppo che si desidera usare e **MyDataCenter** con il valore **siteLocation** specificato nel modello.

    > [WACOM.NOTE] Questo comando restituirà OK dopo il caricamento della distribuzione, ma prima che quest'ultima sia stata applicata alle risorse nel gruppo. Per verificare lo stato della distribuzione, usare il comando seguente.
    >
    > `azure group deployment show MyGroupName MyDeployment`
    >
    > La voce **ProvisioningState** mostra lo stato della distribuzione.
    >
    > Se ci si accorge che la configurazione non è corretta ed è necessario arrestare una distribuzione a lunga esecuzione, usare il comando seguente.
    >
    > `azure group deployment stop MyGroupName MyDeployment`
    >
    > Se non si fornisce un nome di distribuzione, ne verrà creato uno automaticamente in base al nome del file di modello. Questo verrà restituito come parte dell'output del comando `azure group create`.

7.  Per visualizzare il gruppo, usare il comando seguente:

        azure group show MyGroupName

    Questo comando restituisce informazioni relative alle risorse nel gruppo. Se sono presenti più gruppi, è possibile usare il comando `azure group list` per recuperare un elenco di nomi di gruppo, quindi usare il comando `azure group show` per visualizzare i dettagli di un gruppo specifico.

## Utilizzo delle risorse

Mentre i modelli consentono di dichiarare modifiche a livello di interi gruppi in ambito di configurazione, è talvolta necessario lavorare su una risorsa specifica. A tale scopo, è possibile usare i comandi `azure resource`.

> [WACOM.NOTE] Quando si usano i comandi `azure resource`, ad eccezione di `list`, è necessario specificare la versione dell'API della risorsa su cui si sta lavorando mediante il parametro `-o`. Se non si è certi della versione dell'API da usare, consultare il file di modello e trovare il campo **apiVersion** relativo alla risorsa.

1.  Per elencare tutte le risorse in un gruppo, usare il comando seguente.

        azure resource list MyGroupName

2.  Per visualizzare risorse individuali, ad esempio il sito Web, all'interno del gruppo, usare il comando seguente.

        azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

    Notare il parametro **Microsoft.Web/sites**. Questo indica il tipo di risorsa per il quale si stanno richiedendo informazioni. Se si scarica il file di modello scaricato in precedenza, si noterà che lo stesso valore viene usato per definire il tipo di risorsa di sito Web descritta nel modello.

    Questo comando restituisce informazioni relative al sito Web. Il campo **hostNames**, ad esempio, dovrebbe includere l'URL per il sito Web. Usarlo nel browser per verificare che il sito Web sia in esecuzione.

3.  Quando si visualizzano i dettagli relativi a una risorsa, è spesso utile usare il parametro `--json` in quanto quest'ultimo rende l'output più leggibile in presenza di valori quali strutture nidificate o raccolte. Nell'esempio seguente viene illustrata la restituzione dei risultati del comando show sotto forma di documento JSON.

        azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

    > [WACOM.NOTE] È possibile salvare i dati JSON in un file usando il carattere \> per ridirigere l'output a un file. Ad esempio:
    >
    > `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

4.  Per eliminare una risorsa esistente, usare il comando seguente.

        azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

## Registrazione

Per visualizzare le informazioni registrate sulle operazioni eseguite su un gruppo, usare il comando `azure group log show`. Per impostazione predefinita, questo comando restituisce l'ultima operazione eseguita sul gruppo. Per visualizzare tutte le operazioni, usare il parametro opzionale `--all`. Per l'ultima distribuzione, usare `--last-deployment`. Per una distribuzione specifica, usare `--deployment` e specificare il nome della distribuzione. Nell'esempio seguente viene restituito un log di tutte le operazioni eseguite sul gruppo 'MyGroup'.

    azure group log show mygroup --all

## Passaggi successivi

-   Per altre informazioni sull'utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure][Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure].
-   Per informazioni sull'utilizzo di Gestione risorse usando Azure PowerShell, vedere [Introduzione all'utilizzo di PowerShell con Gestione risorse][Introduzione all'utilizzo di PowerShell con Gestione risorse]

  [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Microsoft Azure]: /it-it/documentation/articles/xplat-cli/
  [Introduzione all'utilizzo di PowerShell con Gestione risorse]: http://go.microsoft.com/fwlink/?LinkId=394760
