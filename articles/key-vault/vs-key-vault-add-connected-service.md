---
title: Aggiungere il supporto di Key Vault al progetto ASP.NET usando Visual Studio | Microsoft Docs
description: Usare questa esercitazione per apprendere come aggiungere il supporto di Key Vault a un'applicazione Web ASP.NET o ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: c90ef26c0170db67b1d422701b6969ca3f9c9e38
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958517"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Aggiungere Key Vault all'applicazione Web usando Servizi connessi di Visual Studio

In questa esercitazione si apprenderà come aggiungere facilmente tutto ciò che occorre per iniziare a usare Azure Key Vault per gestire i segreti per i progetti Web in Visual Studio, sia che si usi ASP.NET Core o qualsiasi tipo di progetto ASP.NET. Con la funzione Servizi connessi di Visual Studio 2017 è possibile fare in modo che vengano aggiunti automaticamente tutti i pacchetti NuGet e le impostazioni di configurazione necessarie per connettersi a Key Vault in Azure. 

Per i dettagli sulle modifiche apportate da Servizi connessi al progetto per abilitare Key Vault, vedere [Key Vault Connected Service - What happened to my ASP.NET 4.7.1 project](vs-key-vault-aspnet-what-happened.md) (Modifiche apportate al progetto ASP.NET - Servizio connesso Key Vault con Visual Studio) o [Key Vault Connected Service - What happened to my ASP.NET Core project](vs-key-vault-aspnet-core-what-happened.md) (Modifiche apportate al progetto ASP.NET Core - Servizio connesso Key Vault con Visual Studio).

## <a name="prerequisites"></a>Prerequisiti

- **Una sottoscrizione di Azure**. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 versione 15.7** con il carico di lavoro **Sviluppo Web** installato. [Scaricarla qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Per ASP.NET (non Core) è necessario usare Strumenti di sviluppo per .NET Framework 4.7.1 non inclusi nell'installazione per impostazione predefinita. Per eseguire l'installazione, avviare il programma di installazione di Visual Studio, scegliere **Modifica**, quindi **Singoli componenti**, sul lato destro espandere **Sviluppo web e ASP.NET**, quindi scegliere **Strumenti di sviluppo per .NET Framework 4.7.1**.
- Un progetto Web aperto ASP.NET 4.7.1 o ASP.NET Core 2.0

## <a name="add-key-vault-support-to-your-project"></a>Aggiungere il supporto di Key Vault al progetto

1. In **Esplora soluzioni** scegliere **Aggiungi** > **Servizio connesso**.
   Nella pagina Servizio connesso visualizzata sono elencati i servizi che è possibile aggiungere al progetto.
1. Nel menu dei servizi disponibili scegliere **Proteggi i segreti con Azure Key Vault**.

   ![Scegliere "Proteggi i segreti con Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Se è stato effettuato l'accesso a Visual Studio e al proprio account è associata una sottoscrizione di Azure, viene visualizzata una pagina contenente un elenco a discesa con le sottoscrizioni. Assicurarsi di aver eseguito l'accesso a Visual Studio e verificare che l'account di accesso sia lo stesso usato per la sottoscrizione di Azure.

1. Selezionare la sottoscrizione da usare e quindi scegliere un Key Vault nuovo o esistente oppure scegliere il collegamento per la modifica per cambiare il nome generato automaticamente.

   ![Selezionare la propria sottoscrizione](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Digitare il nome da usare per il Key Vault.

   ![Rinominare il Key Vault e scegliere un gruppo di risorse](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Selezionare un gruppo di risorse esistente oppure crearne uno nuovo con un nome univoco generato automaticamente.  Se si vuole creare un nuovo gruppo con un nome diverso, è possibile usare il [portale di Azure](https://portal.azure.com), quindi chiudere la pagina e riavviare per ricaricare l'elenco di gruppi di risorse.
1. Scegliere l'area in cui creare il Key Vault. Se l'applicazione Web è ospitata in Azure, scegliere l'area che ospita l'applicazione Web per ottenere prestazioni ottimali.
1. Scegliere un modello di prezzi. Per informazioni dettagliate, vedere [Prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Scegliere OK per accettare le opzioni di configurazione.
1. Scegliere **Aggiungi** per creare il Key Vault. Se si sceglie un nome già usato, il processo di creazione potrebbe non riuscire.  In tal caso usare il collegamento per la **modifica** per rinominare il Key Vault, quindi riprovare.

   ![Aggiunta del servizio connesso al progetto](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. A questo punto aggiungere un segreto nel Key Vault in Azure. Per accedere alla posizione corretta nel portale, fare clic sul collegamento Gestisci i segreti archiviati in questo Key Vault. Se è stato chiuso il progetto o la pagina, è possibile accedere a questa posizione nel [portale di Azure](https://portal.azure.com) scegliendo **Tutti i servizi** sotto **Sicurezza**, scegliere **Key Vault**, quindi selezionare il Key Vault appena creato.

   ![Accesso al portale](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Nella sezione Insieme di credenziali delle chiavi per l'insieme di credenziali delle chiavi appena creato scegliere **Segreti**, quindi **Genera/Importa**.

   ![Generare/Importare un segreto](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Immettere un segreto, ad esempio "MySecret", e assegnargli un valore stringa come test, quindi scegliere il pulsante **Crea**.

   ![Creare un segreto](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (facoltativo) Immettere un altro segreto, ma questa volta inserirlo in una categoria assegnando il nome "Secrets--MySecret". Questa sintassi specifica una categoria "Secrets" che contiene un segreto "MySecret".
 
A questo punto è possibile accedere ai segreti nel codice. I passaggi successivi variano a seconda che si usi ASP.NET 4.7.1 o ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Accedere ai segreti nel codice (progetti ASP.NET Core)

La connessione a Key Vault è impostata all'avvio da una classe che implementa [Microsoft.AspNetCore.Hosting.IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) usando un metodo di estensione di comportamento di avvio che è descritto in [Enhance an app from an external assembly in ASP.NET Core with IHostingStartup](/aspnet/core/fundamentals/host/platform-specific-configuration) (Migliorare un'app da un assembly esterno in ASP.NET Core con IHostingStartup). La classe di avvio usa due variabili di ambiente che contengono le informazioni di connessione di Key Vault: ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED, impostata su true e ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT, impostata sull'URL di Key Vault. Queste vengono aggiunte al file launchsettings.json quando l'esecuzione avviene tramite il processo **Aggiungi servizio connesso**.

Per accedere ai segreti:

1. Nel progetto ASP.NET Core in Visual Studio è possibile ora fare riferimento a questi segreti usando le espressioni seguenti nel codice:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. In una pagina con estensione cshtml, ad esempio About.cshtml, aggiungere la direttiva @inject nella parte superiore del file per configurare una variabile che è possibile usare per accedere alla configurazione di Key Vault.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Come test è possibile confermare che il valore del segreto è disponibile visualizzandolo in una delle pagine. Usare @config per fare riferimento alla variabile di configurazione.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Compilare ed eseguire l'applicazione Web, passare alla pagina delle informazioni e visualizzare il valore del "segreto".

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Accedere ai segreti nel codice (progetti ASP.NET 4.7.1)

La connessione a Key Vault è impostata dalla classe ConfigurationBuilder mediante le informazioni aggiunte al file web.config. quando l'esecuzione avviene tramite il processo **Aggiungi servizio connesso**.

Per accedere ai segreti:

1. Modificare web.config nel modo seguente. Le chiavi sono segnaposti che verranno sostituiti da AzureKeyVault ConfigurationBuilder con i valori dei segreti nel Key Vault.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. In HomeController, nel metodo del controller About, aggiungere le seguenti righe per recuperare il segreto e archiviarlo in ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. Nella visualizzazione About.cshtml aggiungere quanto segue per visualizzare il valore del segreto (solo come test).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

1. Eseguire l'app in locale per verificare che sia possibile leggere il valore del segreto immesso nel portale di Azure, non il valore fittizio dal file di configurazione.

Successivamente, pubblicare l'app in Azure.

## <a name="publish-to-azure-app-service"></a>Pubblicare in Servizio app di Azure

1. Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Pubblica**. Verrà visualizzata la schermata **Selezionare una destinazione di pubblicazione**. A sinistra scegliere **Servizio app** e quindi **Crea nuovo**.

   ![Eseguire la pubblicazione nel servizio app](media/vs-key-vault-add-connected-service/AppServicePublish1.PNG)

1. Nella schermata **Crea servizio app** verificare che la sottoscrizione e il gruppo di risorse corrispondano a quelli creati in Key Vault e scegliere **Crea**.

   ![Creare un servizio app](media/vs-key-vault-add-connected-service/AppServicePublish2.PNG)

1. Dopo aver creato l'applicazione Web, viene visualizzata la schermata **Pubblica**. Prendere nota dell'URL per l'applicazione Web pubblicata, ospitata in Azure. Se viene visualizzato **Nessuno** accanto a **Insieme di credenziali delle chiavi**, è comunque necessario indicare a Servizio app l'insieme di credenziali delle chiavi con cui si vuole stabilire la connessione. Scegliere il collegamento **Aggiungi Key Vault** e scegliere l'insieme di credenziali delle chiavi creato.

   ![Aggiungere l'insieme di credenziali delle chiavi](media/vs-key-vault-add-connected-service/AppServicePublish3.PNG)

   Se viene visualizzato **Gestisci insieme di credenziali delle chiavi**, è possibile fare clic per visualizzare le impostazioni correnti, modificare le autorizzazioni o apportare modifiche ai segreti nel portale di Azure.

1. A questo punto, scegliere il collegamento dell'URL del sito per visitare l'applicazione Web nel browser. Verificare che venga visualizzato il valore corretto di Key Vault.

A questo punto si è verificato che l'app Web può usare Key Vault per accedere ai segreti archiviati in modo sicuro quando viene eseguita in Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse. In questo modo vengono eliminati l'insieme di credenziali delle chiavi e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questa guida introduttiva, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Key Vault leggere [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](key-vault-developers-guide.md)