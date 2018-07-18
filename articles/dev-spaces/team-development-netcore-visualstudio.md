---
title: Team di sviluppo per Azure Dev Spaces usando .NET Core e Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: 93c9f42df1136f242a5a04dc3056e0b1595cfb02
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37933443"
---
# <a name="team-development-with-azure-dev-spaces"></a>Team di sviluppo con Azure Dev Spaces

In questa esercitazione si apprenderà come usare più spazi di sviluppo che vengono eseguiti contemporaneamente in diversi ambienti di sviluppo, tenendo separato il lavoro in spazi di sviluppo separati nello stesso cluster.

## <a name="call-another-container"></a>Chiamare un altro contenitore
In questa sezione si creerà un secondo servizio `mywebapi` al quale `webfrontend` assegnerà un nome. Ogni servizio viene eseguito in contenitori separati. Verrà quindi eseguito il debug in entrambi i contenitori.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Scaricare il codice di esempio per *mywebapi*
Per motivi di tempo, scarichiamo il codice di esempio da un repository GitHub. Passare a https://github.com/Azure/dev-spaces e selezionare **Clone or Download** (Clona o scarica) per scaricare il repository GitHub. Il codice per questa sezione è disponibile in `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Eseguire *mywebapi*
1. Aprire il progetto `mywebapi` in una *finestra di Visual Studio separata*.
1. Selezionare **Azure Dev Spaces** nell'elenco a discesa delle impostazioni di avvio, come in precedenza per il progetto `webfrontend`. Anziché creare un nuovo cluster AKS ora, selezionare lo stesso già creato. Come in precedenza, lasciare lo spazio impostato sul valore predefinito `default` e fare clic su **OK**. Nella finestra Output si nota che Visual Studio inizia a riscaldare il nuovo servizio nello spazio di sviluppo per velocizzare le operazioni quando si avvia il debug.
1. Premere F5 e attendere la compilazione e la distribuzione del servizio. Il servizio è pronto quando la barra di stato di Visual Studio diventa arancione
1. Prendere nota dell'URL dell'endpoint visualizzato nel riquadro **Azure Dev Spaces per AKS** nella finestra **Output**. Sarà simile a http://localhost:\<portnumber\>. Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nello spazio di sviluppo in Azure.
2. Quando `mywebapi` è pronto, aprire il browser all'indirizzo localhost e aggiungere `/api/values` all'URL per richiamare l'API GET predefinita per `ValuesController`. 
3. Se tutte le operazioni hanno avuto esito positivo, dovrebbe venire visualizzata una risposta da parte del servizio `mywebapi` simile a quanto segue.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Creare una richiesta da *webfrontend* a *mywebapi*
Ora scriviamo il codice in `webfrontend` che crea una richiesta a `mywebapi`. Passare alla finestra di Visual Studio con il progetto `webfrontend`. Nel file `HomeController.cs` *sostituire* il codice per il metodo About con il codice seguente:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

L'esempio di codice precedente inoltra l'intestazione `azds-route-as` dalla richiesta in ingresso alla richiesta in uscita. Più avanti si vedrà come ciò agevoli un'esperienza di sviluppo più produttiva negli scenari di team.

### <a name="debug-across-multiple-services"></a>Eseguire il debug in più servizi
1. A questo punto `mywebapi` dovrebbe ancora essere in esecuzione con il debugger collegato. In caso contrario, premere F5 nel progetto `mywebapi`.
1. Impostare un punto di interruzione nel metodo `Get(int id)` nel file `Controllers/ValuesController.cs` che gestisce richieste GET `api/values/{id}`.
1. Nel punto del progetto `webfrontend` in cui è stato incollato il codice precedente, impostare un punto di interruzione prima che una richiesta GET venga inviata a `mywebapi/api/values`.
1. Premere F5 nel progetto `webfrontend`. Visual Studio apre di nuovo un browser sulla porta localhost appropriata e viene visualizzata l'app Web.
1. Fare clic sul collegamento **About** nella parte superiore della pagina per attivare il punto di interruzione nel progetto `webfrontend`. 
1. Premere F10 per continuare. Il punto di interruzione nel progetto `mywebapi` viene attivato.
1. Premere F5 per continuare e tornare al codice nel progetto `webfrontend`.
1. Se si preme F5 ancora una volta la richiesta viene completata e si torna a una pagina nel browser. Nell'app Web, la pagina About contiene un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi".

Ecco fatto! È ora disponibile un'applicazione multicontenitore in cui ogni contenitore può essere sviluppato e distribuito separatamente.

## <a name="learn-about-team-development"></a>Informazioni sul team di sviluppo

Finora il codice dell'applicazione è stato eseguito come se si fosse l'unico sviluppatore a lavorare sull'app. In questa sezione, si apprenderà come Azure Dev Spaces semplifichi lo sviluppo in team:
* Consente a un team di sviluppatori di lavorare nello stesso ambiente, usando uno spazio di sviluppo condiviso o spazi di sviluppo distinti in base alle esigenze.
* Supporta ogni sviluppatore che itera sul proprio codice in isolamento senza il timore di interrompere gli altri.
* Permette di testare il codice end-to-end, prima di eseguirne il commit, senza dover creare simulazioni delle dipendenze.

### <a name="challenges-with-developing-microservices"></a>Problemi con lo sviluppo di microservizi
L'applicazione di esempio non è molto complessa al momento. Nello sviluppo nel mondo reale, tuttavia, le sfide emergono appena si aggiungono più servizi e il team di sviluppo cresce.

Si immagini di lavorare a un servizio che interagisce con dozzine di altri servizi.

- Può diventare irrealistico eseguire tutto in locale per lo sviluppo. Il computer di sviluppo potrebbe non disporre di risorse sufficienti per eseguire l'intera app. In alternativa, è probabile che l'app disponga di endpoint che devono essere raggiungibili pubblicamente (ad esempio l'app risponde a un webhook da un'app SaaS).
- È possibile provare a eseguire solo i servizi da cui si dipende, ma in tal caso sarebbe necessario conoscere la chiusura completa delle dipendenze (ad esempio le dipendenze delle dipendenze). Potrebbe anche non essere semplice capire come compilare ed eseguire le proprie dipendenze perché non si è lavorato su di esse.
- Alcuni sviluppatori ricorrono alla simulazione di molte delle loro dipendenze di servizio. Ciò può essere utile a volte, ma la gestione di quelle simulazioni può pesare presto sul proprio lavoro di sviluppo. Inoltre, in questo modo l'ambiente di sviluppo è visto in modo diverso dalla produzione e potrebbero insorgere bug.
- È quindi difficile eseguire qualsiasi tipo di test end-to-end. I test di integrazione possono avvenire in modo realistico solo dopo il commit, il che significa che i problemi verranno riscontrati più avanti nel ciclo di sviluppo.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Lavorare in uno spazio di sviluppo condiviso
Con Azure Dev Spaces è possibile configurare uno spazio di sviluppo *condiviso* in Azure. Ogni sviluppatore può concentrarsi solo sulla propria parte dell'applicazione e può sviluppare in modo iterativo *codice pre-commit* in uno spazio di sviluppo che contiene già tutti gli altri servizi e le risorse cloud da cui dipendono i propri scenari. Le dipendenze sono sempre aggiornate e gli sviluppatori lavorano in un modo che rispecchia la produzione.

### <a name="work-in-your-own-space"></a>Lavorare nello spazio personale
Quando si sviluppa codice per un servizio, prima che sia pronto per l'archiviazione, il codice spesso non è in buono stato. Sono ancora in corso attività iterative di modellazione, test e sperimentazione con le soluzioni. Azure Dev Spaces fornisce il concetto di uno **spazio**, che consente di lavorare in isolamento senza timore di interrompere i membri del team.

Procedere come segue per assicurarsi che entrambi i servizi `webfrontend` e `mywebapi` siano in esecuzione nello **spazio di sviluppo `default`**.
1. Chiudere qualsiasi sessione di debug/F5 per entrambi i servizi, ma tenere aperti i progetti nelle finestre di Visual Studio.
2. Passare alla finestra di Visual Studio con il progetto `mywebapi` e premere CTRL+F5 per eseguire il servizio senza debugger collegato
3. Passare alla finestra di Visual Studio con il progetto `webfrontend` e premere CTRL+F5 per eseguire anche questo.

> [!Note]
> In alcuni casi è necessario aggiornare il browser dopo la visualizzazione iniziale della pagina Web dopo CTRL+F5.

Chiunque apra l'URL pubblico e navighi all'app Web richiama il percorso del codice scritto in precedenza che viene eseguito in entrambi i servizi con lo spazio `default` predefinito. Si supponga di voler continuare a sviluppare `mywebapi`. Come è possibile eseguire questa operazione senza interrompere altri sviluppatori che usano lo spazio di sviluppo? A tale scopo, è necessario impostare il proprio spazio.

### <a name="create-a-new-dev-space"></a>Creare un nuovo spazio di sviluppo
Da Visual Studio, è possibile creare spazi aggiuntivi che verranno utilizzati quando si utilizza F5 o CTRL+F5 nel servizio. È possibile assegnare qualsiasi nome allo spazio con la massima flessibilità di significato (ad esempio `sprint4` o `demo`).

Per creare un nuovo spazio, eseguire questa operazione:
1. Passare alla finestra di Visual Studio con il progetto `mywebapi`.
2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Proprietà**.
3. Selezionare la scheda **Debug** a sinistra per visualizzare le impostazioni di Azure Dev Spaces.
4. A questo punto, è possibile modificare o creare il cluster e/o lo spazio che sarà utilizzato quando si usa F5 o CTRL+F5. *Assicurarsi che sia selezionato lo spazio Azure Dev Spaces creato in precedenza*.
5. Nell'elenco a discesa Spazio, selezionare **<Crea nuovo spazio...>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Nella finestra di dialogo **Aggiungi spazio** digitare un nome per lo spazio e fare clic su **OK**. È possibile utilizzare il proprio nome (ad esempio "scott") per il nuovo spazio in modo che ai colleghi sia chiaro quale spazio sia in uso dallo sviluppatore.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Verrà ora visualizzato il cluster AKS e il nuovo spazio selezionato nella pagina delle proprietà del progetto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aggiornare il codice per *mywebapi*

1. Nel progetto `mywebapi` apportare una modifica al codice per il metodo `string Get(int id)` nel file `Controllers/ValuesController.cs` come indicato di seguito:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Impostare un punto di interruzione in questo blocco di codice aggiornato (potrebbe essercene già uno impostato in precedenza).
3. Premere F5 per avviare il servizio `mywebapi`. Il servizio viene avviato nel cluster usando lo spazio selezionato, che in questo caso è `scott`.

Di seguito è riportato un diagramma che aiuta a capire il funzionamento dei diversi spazi. Il percorso viola mostra una richiesta tramite lo spazio `default`, che è il percorso predefinito usato se non viene anteposto un altro spazio all'URL. Il percorso rosa indica una richiesta tramite lo spazio `default/scott`.

![](media/common/Space-Routing.png)

Questa funzionalità integrata di Azure Dev Spaces consente di testare codice end-to-end in un ambiente condiviso senza richiedere a ogni sviluppatore di ricreare lo stack completo dei servizi nel proprio spazio. Questo routing richiede l'inoltro delle intestazioni di propagazione nel codice dell'app, come illustrato nel passaggio precedente di questa Guida.

### <a name="test-code-running-in-the-defaultscott-space"></a>Testare il codice in esecuzione nello spazio `default/scott`
Per testare la nuova versione di `mywebapi` in combinazione con `webfrontend`, aprire il browser all'URL di punto di accesso pubblico per `webfrontend`, ad esempio http://webfrontend.123456abcdef.eastus.aksapp.io), e passare alla pagina delle informazioni. Verrà visualizzato il messaggio originale "Salve da webfrontend e Salve da mywebapi".

A questo punto, aggiungere "scott.s". all'URL in modo che sia simile a http://scott.s.webfrontend.123456abcdef.eastus.aksapp.io e aggiornare il browser. Si dovrebbe raggiungere il punto di interruzione impostato nel progetto `mywebapi`. Premere F5 per continuare e nel browser dovrebbe essere visualizzato il nuovo messaggio "Salve da webfrontend e mywebapi ora dice qualcosa di nuovo". Ciò è dovuto al fatto che il percorso al codice aggiornato in `mywebapi` è in esecuzione nello spazio `default/scott`.

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
