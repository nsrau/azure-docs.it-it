---
title: Creare modelli di distribuzione per le app per la logica di Azure | Microsoft Docs
description: Creare modelli di Azure Resource Manager per la distribuzione delle app per la logica e la gestione dei rilasci
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: e736bf626cd379e1bffe2dbf023e86addb09539f
ms.lasthandoff: 04/21/2017


---
# <a name="create-templates-for-logic-apps-deployment-and-release-management"></a>Creare modelli per la distribuzione delle app per la logica e la gestione dei rilasci

Una volta creata un'app per la logica, è possibile crearla come un modello di Azure Resource Manager.
Così facendo, l'app per la logica potrà essere facilmente distribuita in qualsiasi ambiente o gruppo di risorse in cui potrebbe essere necessaria.
Per altre informazioni sui modelli di Resource Manager, vedere gli articoli su [creazione di modelli di Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [distribuzione delle risorse con i modelli di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modello di distribuzione di app per la logica

Un'app per la logica dispone di tre componenti di base:

* **Risorsa di app per la logica**: contiene informazioni su elementi quali piano tariffario, posizione e definizione del flusso di lavoro.
* **Definizione del flusso di lavoro**: descrive i passaggi del flusso di lavoro dell'app per la logica e come il motore dell'app per la logica deve eseguire il flusso di lavoro.
È possibile visualizzare questa definizione nella finestra **Visualizzazione Codice** dell'app per la logica.
Nella risorsa di app per la logica è possibile trovare questa definizione nella proprietà `definition`.
* **Connessioni**: si riferisce a risorse separate per archiviare in modo sicuro i metadati su qualsiasi connessione del connettore, ad esempio una stringa di connessione e un token di accesso.
Nella risorsa di app per la logica, l'app per la logica si riferisce a queste risorse nella sezione `parameters`.

È possibile visualizzare tutti questi elementi delle app per la logica esistenti usando uno strumento come [Esplora inventario risorse di Azure](http://resources.azure.com).

Per creare un modello per un'app per la logica da usare con distribuzioni di gruppi di risorse, è necessario definire le risorse e creare i parametri necessari.
Ad esempio, se si esegue la distribuzione in un ambiente di sviluppo, un ambiente di test e un ambiente di produzione, è probabile che in ogni si vogliano usare stringhe di connessione a un database SQL diverse.
In alternativa, è possibile che si voglia eseguire la distribuzione in sottoscrizioni o gruppi di risorse diversi.  

## <a name="create-a-logic-app-deployment-template"></a>Creare un modello di distribuzione di app per la logica

Il modo più semplice per ottenere un modello di distribuzione di app per la logica consiste nell'usare [Visual Studio Tools per app per la logica](logic-apps-deploy-from-vs.md).
Gli strumenti di Visual Studio generano un modello di distribuzione valido, che può essere usato in qualsiasi sottoscrizione o località.

Esistono altri strumenti utili per la creazione di un modello di distribuzione di app per la logica.
È possibile eseguire la creazione manualmente utilizzando le risorse appena descritte per creare i parametri in base alle esigenze.
In alternativa è possibile utilizzare un modulo [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) di PowerShell. Questo modulo open source valuta l'app per la logica ed eventuali connessioni che utilizza, quindi genera risorse del modello con i parametri necessari per la distribuzione.
Se, ad esempio, si dispone di un'app per la logica che ha ricevuto un messaggio da una coda del bus del servizio di Azure e ha aggiunto dati a un database SQL di Azure, lo strumento conserva l'intera logica di orchestrazione e crea parametri per le stringhe di connessione di SQL e del bus di servizio, in modo che sia possibile configurarle in fase di distribuzione.

> [!NOTE]
> Le connessioni devono essere incluse nello stesso gruppo di risorse dell'app per la logica.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Installare il modulo PowerShell per il modello di app per la logica
Il modo più semplice per installare il modulo consiste nell'usare la [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) con il comando `Install-Module -Name LogicAppTemplate`.  

È inoltre possibile installare il modulo PowerShell manualmente:

1. Scaricare la versione più recente di [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Estrarre la cartella nella cartella del modulo PowerShell, in genere `%UserProfile%\Documents\WindowsPowerShell\Modules`.

Per consentire il funzionamento del modulo con qualsiasi tenant e qualsiasi token di accesso della sottoscrizione, è consigliabile usare lo strumento della riga di comando [ARMClient](https://github.com/projectkudu/ARMClient).  Questo [post di blog ](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) illustra ARMClient in modo più dettagliato.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generare un modello di app per la logica tramite PowerShell
Dopo aver installato PowerShell, è possibile generare un modello utilizzando il comando seguente:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` è l'ID sottoscrizione di Azure. Questa riga ottiene innanzitutto un token di accesso tramite ARMClient, lo trasmette allo script di PowerShell e quindi crea il modello in un file JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Aggiungere parametri a un modello di app per la logica
Dopo aver creato il modello di app per la logica, è possibile continuare ad aggiungere o modificare i parametri necessari. Se, ad esempio, la definizione include un ID di risorsa in una funzione di Azure o in un flusso di lavoro annidato in cui si prevede eseguire una singola distribuzione, è possibile aggiungere altre risorse al modello e creare parametri per gli ID in base alla necessità. Lo stesso approccio è applicabile a qualsiasi riferimento ad API personalizzate o endpoint Swagger che si prevede di distribuire in ogni gruppo di risorse.

## <a name="deploy-a-logic-app-template"></a>Distribuire un modello di app per la logica

Per distribuire il modello, è possibile usare alcuni strumenti, inclusi PowerShell, API REST, [Visual Studio Team Services Release Management](#team-services) e la distribuzione dei modelli attraverso il Portale di Azure.
Per archiviare i valori per i parametri, è inoltre consigliabile creare un [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Sono disponibili informazioni su come [distribuire risorse con i modelli di Azure Resource Manager e PowerShell](../azure-resource-manager/resource-group-template-deploy.md) o [distribuire risorse con i modelli di Azure Resource Manager e il Portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autorizzare le connessioni OAuth

Una volta distribuite, le app per la logica funzionano end-to-end con parametri validi.
Tuttavia, è necessario continuare ad autorizzare le connessioni OAuth per generare un token di accesso valido.
Per autorizzare le connessioni OAuth, aprire l'app per la logica nella finestra di progettazione delle app per la logica e autorizzare le connessioni. In alternativa, per la distribuzione automatizzata è possibile usare uno script per consentire ogni connessione OAuth.
Nel progetto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) è presente uno script di esempio su GitHub.

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services Release Management

Uno scenario comune per la distribuzione e la gestione di ambienti consiste nell'usare uno strumento come Release Management di Visual Studio Team Services con un modello di distribuzione di app per la logica. Visual Studio Team Services include un'attività [Distribuisci gruppo di risorse di Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) , che può essere aggiunta in una build o in una pipeline di rilascio. Per l'autorizzazione alla distribuzione è necessario avere un'[entità servizio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Sarà quindi possibile generare la definizione della versione.

1. In Release Management selezionare **Vuoto** per creare una definizione vuota.

    ![Creare una definizione vuota][1]

2. Scegliere le risorse necessarie, possibilmente includendo il modello di app per la logica generato manualmente o nell'ambito del processo di compilazione.
3. Aggiungere un'attività **Distribuzione gruppo di risorse di Azure** .
4. Eseguire la configurazione con un' [entità servizio](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), quindi fare riferimento ai file Modello e Parametri modello.
5. Continuare a compilare passaggi nel processo di rilascio per eventuali altri ambienti, test automatizzati o responsabili approvazione necessari.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png

