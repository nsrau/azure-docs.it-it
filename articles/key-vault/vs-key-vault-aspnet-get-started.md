---
title: Introduzione al Servizio connesso Key Vault in Visual Studio (progetti ASP.NET) | Microsoft Docs
description: Usare questa esercitazione per apprendere come aggiungere il supporto di Key Vault a un'applicazione Web ASP.NET o ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: cd305801f10c899682aa6d751e48f30b6e8303fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781620"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Introduzione al Servizio connesso Key Vault in Visual Studio (progetti ASP.NET)

> [!div class="op_single_selector"]
> - [Per iniziare](vs-key-vault-aspnet-get-started.md)
> - [Risultati](vs-key-vault-aspnet-what-happened.md)

Questo articolo fornisce istruzioni aggiuntive da seguire dopo l'aggiunta di Key Vault a un progetto ASP.NET MVC tramite il comando **Aggiungi Servizi connessi** in Visual Studio. Se il servizio non è già stato aggiunto al progetto, è possibile eseguire questa operazione in qualsiasi momento seguendo le istruzioni in [Aggiungere Key Vault all'applicazione Web usando Servizi connessi di Visual Studio](vs-key-vault-add-connected-service.md).

Vedere [What happened to my ASP.NET project?](vs-key-vault-aspnet-core-what-happened.md) (Modifiche apportate al progetto ASP.NET) per informazioni sulle modifiche apportate al progetto quando si aggiunge il servizio connesso.

## <a name="after-you-connect"></a>Dopo la connessione

1. Aggiungere un segreto nel Key Vault in Azure. Per accedere alla posizione corretta nel portale, fare clic sul collegamento **Gestisci i segreti archiviati in questo Key Vault**. Se è stato chiuso il progetto o la pagina, è possibile accedere a questa posizione nel [portale di Azure](https://portal.azure.com) scegliendo **Tutti i servizi** sotto **Sicurezza**, scegliere **Key Vault**, quindi selezionare il Key Vault appena creato.

   ![Accesso al portale](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Nella sezione Insieme di credenziali delle chiavi per l'insieme di credenziali delle chiavi appena creato scegliere **Segreti**, quindi **Genera/Importa**.

   ![Generare/Importare un segreto](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Immettere un segreto, ad esempio **MySecret**, e assegnargli un valore stringa come test, quindi scegliere il pulsante **Crea**.

   ![Creare un segreto](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (facoltativo) Immettere un altro segreto, ma questa volta inserirlo in una categoria assegnando il nome **Secrets--MySecret**. Questa sintassi specifica una categoria **Secrets** che contiene un segreto **MySecret**.

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

A questo punto l'app Web è stata abilitata all'uso di Key Vault per accedere ai segreti archiviati in modo sicuro.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse. In questo modo vengono eliminati l'insieme di credenziali delle chiavi e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questa guida introduttiva, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.

# <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo con Key Vault leggere [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](key-vault-developers-guide.md)