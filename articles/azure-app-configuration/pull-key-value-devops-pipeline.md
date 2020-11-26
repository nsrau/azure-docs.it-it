---
title: Effettuare il pull della configurazione dell'app settingsfromo con Azure Pipelines
description: Informazioni su come usare Azure Pipelines per eseguire il pull dei valori di chiave in un archivio di configurazione dell'app
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 15810e65873c685565ccaad6c2dcdc1707713f2c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182649"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Impostazioni pull per la configurazione dell'app con Azure Pipelines

L'attività di [configurazione app Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) estrae i valori chiave dall'archivio di configurazione dell'app e li imposta come variabili della pipeline di Azure, che possono essere usati dalle attività successive. Questa attività è complementare all'attività [push di configurazione app Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) che inserisce i valori chiave da un file di configurazione nell'archivio di configurazione dell'app. Per altre informazioni, vedere [Impostazioni Push per la configurazione delle app con Azure Pipelines](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- Archivio di configurazione dell'app: crearne uno gratuitamente nel [portale di Azure](https://portal.azure.com).
- Progetto DevOps di Azure- [crearne uno](https://go.microsoft.com/fwlink/?LinkId=2014881) gratuitamente
- App Azure attività di configurazione: scaricare gratuitamente dal [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.).  

## <a name="create-a-service-connection"></a>Creare una connessione al servizio

Una connessione al servizio consente di accedere alle risorse nella sottoscrizione di Azure dal progetto DevOps di Azure.

1. In Azure DevOps passare al progetto contenente la pipeline di destinazione e aprire le **impostazioni del progetto** in basso a sinistra.
1. In **pipeline** selezionare **connessioni al servizio**.
1. Se non sono presenti connessioni al servizio, fare clic sul pulsante **Crea connessione al servizio** al centro della schermata. In caso contrario, fare clic su **nuova connessione al servizio** in alto a destra nella pagina.
1. Selezionare **Azure Resource Manager**.
1. Selezionare **entità servizio (automatica)**.
1. Compilare la sottoscrizione e la risorsa. Assegnare un nome alla connessione al servizio.

Ora che è stata creata la connessione al servizio, trovare il nome dell'entità servizio assegnata. Nel passaggio successivo verrà aggiunta una nuova assegnazione di ruolo a questa entità servizio.

1. Passare a **Impostazioni progetto**  >  **connessioni al servizio**.
1. Selezionare la connessione al servizio creata nella sezione precedente.
1. Selezionare **Gestisci entità servizio**.
1. Prendere nota del **nome visualizzato** elencato.

## <a name="add-role-assignment"></a>Aggiungi un'assegnazione di ruolo

Assegnare il ruolo di configurazione dell'app appropriato alla connessione del servizio usata all'interno dell'attività in modo che l'attività possa accedere all'archivio di configurazione dell'app.

1. Passare all'archivio di configurazione dell'app di destinazione. Per una procedura dettagliata sull'impostazione di un archivio di configurazione dell'app, vedere [creare un archivio di configurazione delle app](/azure/azure-app-configuration/quickstart-dotnet-core-app#create-an-app-configuration-store) in una delle guide introduttive alla configurazione app Azure.
1. A sinistra selezionare controllo di **accesso (IAM)**.
1. Nella parte superiore selezionare **+ Aggiungi** e scegliere **Aggiungi assegnazione ruolo**.
1. In **ruolo** selezionare **lettore dati di configurazione dell'app**. Questo ruolo consente all'attività di leggere dall'archivio di configurazione dell'app. 
1. Selezionare l'entità servizio associata alla connessione al servizio creata nella sezione precedente.

> [!NOTE]
> Per risolvere Azure Key Vault riferimenti nella configurazione dell'app, è necessario concedere anche alla connessione al servizio l'autorizzazione per leggere i segreti negli insiemi di credenziali delle chiavi di Azure a cui si fa riferimento.
  
## <a name="use-in-builds"></a>Uso nelle compilazioni

In questa sezione viene illustrato come usare l'attività di configurazione app Azure in una pipeline di compilazione di Azure DevOps.

1. Passare alla pagina della pipeline di compilazione facendo **clic su pipeline pipeline**  >  **Pipelines**. Per la documentazione della pipeline di compilazione, vedere  [creare la prima pipeline](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Se si sta creando una nuova pipeline di compilazione, fare clic su **nuova pipeline** e selezionare il repository per la pipeline. Selezionare **Mostra Assistente** sul lato destro della pipeline e cercare l'attività di **configurazione app Azure** .
      - Se si usa una pipeline di compilazione esistente, selezionare **modifica** per modificare la pipeline. Nella scheda **attività** cercare l'attività di **configurazione app Azure** .
1. Configurare i parametri necessari per l'attività per eseguire il pull dei valori di chiave dall'archivio di configurazione dell'app. Le descrizioni dei parametri sono disponibili nella sezione **Parameters** riportata di seguito e nelle descrizioni comandi accanto a ogni parametro.
      - Impostare il parametro **sottoscrizione di Azure** sul nome della connessione del servizio creata in un passaggio precedente.
      - Impostare il **nome della configurazione dell'app** sul nome della risorsa dell'archivio di configurazione dell'app.
      - Lasciare i valori predefiniti per i parametri rimanenti.
1. Salvare e accodare una compilazione. Nel log di compilazione verranno visualizzati tutti gli errori che si sono verificati durante l'esecuzione dell'attività.

## <a name="use-in-releases"></a>Uso nelle versioni

In questa sezione viene illustrato come usare l'attività di configurazione app Azure in una pipeline di rilascio di Azure DevOps.

1. Passare alla pagina della pipeline di rilascio selezionando **pipeline**  >  **versioni**. Per la documentazione sulla pipeline di rilascio, vedere [pipeline di rilascio](/azure/devops/pipelines/release?view=azure-devops).
1. Scegliere una pipeline di rilascio esistente. Se non è già stato fatto, fare clic su **nuova pipeline** per crearne uno nuovo.
1. Selezionare il pulsante **modifica** nell'angolo superiore destro per modificare la pipeline di rilascio.
1. Scegliere la **fase** per aggiungere l'attività. Per altre informazioni sulle fasi, vedere [aggiungere fasi, dipendenze, & condizioni](/azure/devops/pipelines/release/environments?view=azure-devops).
1. Fare clic su **+** per "Esegui su agente", quindi aggiungere l'attività di **configurazione app Azure** nella scheda **Aggiungi attività** .
1. Configurare i parametri necessari all'interno dell'attività per eseguire il pull dei valori di chiave dall'archivio di configurazione dell'app. Le descrizioni dei parametri sono disponibili nella sezione **Parameters** riportata di seguito e nelle descrizioni comandi accanto a ogni parametro.
      - Impostare il parametro **sottoscrizione di Azure** sul nome della connessione del servizio creata in un passaggio precedente.
      - Impostare il **nome della configurazione dell'app** sul nome della risorsa dell'archivio di configurazione dell'app.
      - Lasciare i valori predefiniti per i parametri rimanenti.
1. Salvare e accodare una versione. Nel log versione verranno visualizzati tutti gli errori rilevati durante l'esecuzione dell'attività.

## <a name="parameters"></a>Parametri

L'attività di configurazione app Azure utilizza i parametri seguenti:

- **Sottoscrizione di Azure**: un elenco a discesa contenente le connessioni al servizio di Azure disponibili. Per aggiornare e aggiornare l'elenco delle connessioni dei servizi di Azure disponibili, fare clic sul pulsante **Aggiorna sottoscrizione di Azure** a destra della casella di testo.
- **Nome della configurazione dell'app**: un elenco a discesa che carica gli archivi di configurazione disponibili nella sottoscrizione selezionata. Per aggiornare e aggiornare l'elenco degli archivi di configurazione disponibili, fare clic sul pulsante **Aggiorna nome della configurazione dell'app** a destra della casella di testo.
- **Filtro della chiave**: è possibile usare il filtro per selezionare i valori di chiave richiesti dalla configurazione app Azure. Il valore * consente di selezionare tutti i valori chiave. Per altre informazioni su, vedere [valori della chiave di query](concept-key-value.md#query-key-values).
- **Label**: specifica l'etichetta da usare per la selezione dei valori chiave dall'archivio di configurazione dell'app. Se non viene specificata alcuna etichetta, verranno recuperati i valori di chiave con l'etichetta No. I caratteri seguenti non sono consentiti:, *.
- **Trim Key prefix**: specifica uno o più prefissi che devono essere rimossi dalle chiavi di configurazione dell'app prima di impostarli come variabili. Più prefissi possono essere separati da un carattere di nuova riga.

## <a name="use-key-values-in-subsequent-tasks"></a>Usare valori di chiave nelle attività successive

I valori di chiave recuperati dalla configurazione dell'app vengono impostati come variabili di pipeline, accessibili come variabili di ambiente. La chiave della variabile di ambiente è la chiave del valore chiave recuperato dalla configurazione dell'app dopo aver tagliato il prefisso, se specificato.

Se, ad esempio, un'attività successiva esegue uno script di PowerShell, potrebbe usare un valore chiave con la chiave "myBuildSetting", come indicato di seguito:
```powershell
echo "$env:myBuildSetting"
```
Il valore verrà quindi stampato sulla console.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore imprevisto, è possibile abilitare i log di debug impostando la variabile della pipeline `system.debug` su `true` .

## <a name="faq"></a>Domande frequenti

**Ricerca per categorie comporre la configurazione da più chiavi ed etichette?**

In alcuni casi potrebbe essere necessario comporre la configurazione da più etichette, ad esempio, default e dev. Per implementare questo scenario, è possibile usare più attività di configurazione dell'app in una pipeline. I valori chiave recuperati da un'attività in un passaggio successivo sostituiranno tutti i valori dei passaggi precedenti. Nell'esempio precedente, un'attività può essere usata per selezionare i valori chiave con l'etichetta predefinita, mentre una seconda attività può selezionare i valori chiave con l'etichetta dev. Le chiavi con l'etichetta dev sostituiranno le stesse chiavi con l'etichetta predefinita.
