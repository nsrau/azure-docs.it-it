---
title: Configurare le app nel portale
description: Informazioni su come configurare le impostazioni comuni per un'app del servizio app nel portale di Azure.Learn to configure common settings for an App Service app in the Azure portal. Impostazioni dell'app, stringhe di connessione, piattaforma, stack di lingue, contenitore e così via.
keywords: Servizio app azure, app Web, impostazioni dell'app, variabili di ambiente
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: 18469c94b66acab27b58243e8d15eb924843319b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811112"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configurare un'app del servizio app nel portale di AzureConfigure an App Service app in the Azure portal

Questo argomento illustra come configurare le impostazioni comuni per le app Web, il back-end per dispositivi mobili o l'app API usando il portale di [Azure.]

## <a name="configure-app-settings"></a>Configurare le impostazioni applicazione

Nel servizio app, le impostazioni dell'app sono variabili passate come variabili di ambiente al codice dell'applicazione. Per le app Linux e i contenitori personalizzati, `--env` il servizio app passa le impostazioni dell'app al contenitore usando il flag per impostare la variabile di ambiente nel contenitore.

Nel [portale]di Azure cercare e selezionare **App Services**e quindi selezionare l'app. 

![Cercare i servizi dell'appSearch for App Services](./media/configure-common/search-for-app-services.png)

Nel menu a sinistra dell'app, seleziona**Impostazioni applicazione**di **configurazione.** > 

![Impostazioni dell'applicazione](./media/configure-common/open-ui.png)

Per gli sviluppatori ASP.NET e ASP.NET Core, `<appSettings>` l'impostazione delle impostazioni dell'app nel servizio app è analoga all'impostazione in *Web.config* o *appsettings.json*, ma i valori in App Service eseguono l'override di quelli in *Web.config* o *appsettings.json*. È possibile mantenere al sicuro le impostazioni di sviluppo, ad esempio la password MySQL locale, in *Web.config* o *appsettings.json*, ma i segreti di produzione (ad esempio, la password del database MySQL di Azure) sono sicuri nel servizio app. Lo stesso codice usa le impostazioni di sviluppo quando si esegue il debug in locale e usa i segreti di produzione quando viene distribuito in Azure.The same code uses your development settings when you debug locally, and it uses your production secrets when deployed to Azure.

Anche altri stack di linguaggio ottengono le impostazioni dell'app come variabili di ambiente in fase di esecuzione. Per i passaggi specifici dello stack del linguaggio, vedere:For language-stack steps, see:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contenitori personalizzati](containers/configure-custom-container.md#configure-environment-variables)

Le impostazioni dell'app vengono sempre crittografate quando sono archiviate (crittografia dei dati inattivi).

> [!NOTE]
> Le impostazioni dell'app possono anche essere risolte [dall'insieme di credenziali delle](/azure/key-vault/) chiavi usando [i riferimenti dell'insieme di credenziali delle chiavi](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostra valori nascosti

Per impostazione predefinita, i valori per le impostazioni dell'app sono nascosti nel portale per la sicurezza. Per visualizzare un valore nascosto di un'impostazione dell'app, fai clic sul campo **Valore** di tale impostazione. Per visualizzare i valori di tutte le impostazioni dell'app, fai clic sul pulsante **Mostra valore.**

### <a name="add-or-edit"></a>Aggiungere o modificare

Per aggiungere una nuova impostazione dell'app, fare clic su **Nuova impostazione applicazione**. Nella finestra di dialogo, è possibile [attaccare l'impostazione allo slot corrente.](deploy-staging-slots.md#which-settings-are-swapped)

Per modificare un'impostazione, fare clic sul pulsante **Modifica** sul lato destro.

Al termine, fare clic su **Aggiorna**. Non dimenticare di fare clic su **Salva** di nuovo nella pagina **Configurazione.**

> [!NOTE]
> In un contenitore Linux predefinito o in un contenitore Linux `ApplicationInsights:InstrumentationKey` personalizzato, qualsiasi struttura di `ApplicationInsights__InstrumentationKey` chiave JSON annidata nel nome dell'impostazione dell'app, come deve essere configurata nel servizio app come per il nome della chiave. In altre parole, qualsiasi `:` `__` deve essere sostituito da (doppio sottolineatura).
>

### <a name="edit-in-bulk"></a>Modifica in blocco

Per aggiungere o modificare le impostazioni dell'app in blocco, fai clic sul pulsante **Modifica avanzata.** Al termine, fare clic su **Aggiorna**. Non dimenticare di fare clic su **Salva** di nuovo nella pagina **Configurazione.**

Le impostazioni dell'app hanno la formattazione JSON seguente:App settings have the following JSON formatting:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Configurare le stringhe di connessione

Nel [portale]di Azure cercare e selezionare **App Services**e quindi selezionare l'app. Nel menu a sinistra dell'app, seleziona**Impostazioni applicazione**di **configurazione.** > 

![Impostazioni dell'applicazione](./media/configure-common/open-ui.png)

Per gli sviluppatori ASP.NET e ASP.NET Core, l'impostazione delle stringhe di connessione nel servizio app è analoga all'impostazione in `<connectionStrings>` *Web.config*, ma i valori impostati in Servizio app eseguono l'override di quelli in *Web.config.* È possibile mantenere le impostazioni di sviluppo (ad esempio, un file di database) in *Web.config* e segreti di produzione (ad esempio, le credenziali del database SQL) in modo sicuro nel servizio app. Lo stesso codice usa le impostazioni di sviluppo quando si esegue il debug in locale e usa i segreti di produzione quando viene distribuito in Azure.The same code uses your development settings when you debug locally, and it uses your production secrets when deployed to Azure.

Per altri stack di lingue, è preferibile usare [le impostazioni dell'app,](#configure-app-settings) perché le stringhe di connessione richiedono una formattazione speciale nelle chiavi delle variabili per accedere ai valori. Ecco un'eccezione, tuttavia: viene eseguito il backup di alcuni tipi di database di Azure insieme all'app se si configurano le stringhe di connessione nell'app. Per ulteriori informazioni, vedere [Elementi di cui viene eseguito il backup.](manage-backup.md#what-gets-backed-up) Se non hai bisogno di questo backup automatico, usa le impostazioni dell'app.

In fase di esecuzione, le stringhe di connessione sono disponibili come variabili di ambiente, precedute dai tipi di connessione seguenti:At runtime, connection strings are available as environment variables, prefixed with the following connection types:

* Sqlserver:`SQLCONNSTR_`  
* MySQL: `MYSQLCONNSTR_` 
* SQLAzure:`SQLAZURECONNSTR_` 
* Personalizzato: `CUSTOMCONNSTR_`
* Postgresql:`POSTGRESQLCONNSTR_`  

Ad esempio, è possibile accedere a una stringa di `MYSQLCONNSTR_connectionString1`connessione MySql denominata *connectionstring1* come variabile di ambiente . Per i passaggi specifici dello stack del linguaggio, vedere:For language-stack steps, see:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contenitori personalizzati](containers/configure-custom-container.md#configure-environment-variables)

Le stringhe di connessione vengono sempre crittografate quando sono archiviate (crittografia dei dati inattivi).

> [!NOTE]
> Le stringhe di connessione possono anche essere risolte [dall'insieme di credenziali](/azure/key-vault/) delle chiavi utilizzando [i riferimenti dell'insieme](app-service-key-vault-references.md)di credenziali delle chiavi .

### <a name="show-hidden-values"></a>Mostra valori nascosti

Per impostazione predefinita, i valori per le stringhe di connessione sono nascosti nel portale per la sicurezza. Per visualizzare un valore nascosto di una stringa di connessione, è sufficiente fare clic sul campo **Valore** di tale stringa. Per visualizzare i valori di tutte le stringhe di connessione, fare clic sul pulsante **Mostra valore.**

### <a name="add-or-edit"></a>Aggiungere o modificare

Per aggiungere una nuova stringa di connessione, fare clic su **Nuova stringa**di connessione . Nella finestra di dialogo è possibile incollare la stringa di [connessione allo slot corrente.](deploy-staging-slots.md#which-settings-are-swapped)

Per modificare un'impostazione, fare clic sul pulsante **Modifica** sul lato destro.

Al termine, fare clic su **Aggiorna**. Non dimenticare di fare clic su **Salva** di nuovo nella pagina **Configurazione.**

### <a name="edit-in-bulk"></a>Modifica in blocco

Per aggiungere o modificare le stringhe di connessione in blocco, fare clic sul pulsante **Modifica avanzata.** Al termine, fare clic su **Aggiorna**. Non dimenticare di fare clic su **Salva** di nuovo nella pagina **Configurazione.**

Le stringhe di connessione hanno la formattazione JSON seguente:Connection strings have the following JSON formatting:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Configurare le impostazioni generali

Nel [portale]di Azure cercare e selezionare **App Services**e quindi selezionare l'app. Nel menu a sinistra dell'app, seleziona**Impostazioni generali** **configurazione** > .

![Impostazioni generali](./media/configure-common/open-general.png)

Qui puoi configurare alcune impostazioni comuni per l'app. Per alcune impostazioni è necessario [passare a livelli tariffari più elevati.](manage-scale-up.md)

- **Impostazioni dello stack**: Lo stack software per eseguire l'app, incluse le versioni della lingua e dell'SDK. Per le app Linux e le app contenitore personalizzate, è anche possibile impostare un file o un comando di avvio facoltativo.
- **Impostazioni piattaforma**: Consente di configurare le impostazioni per la piattaforma di hosting, tra cui:
    - **Bitness**: 32 bit o 64 bit.
    - **Protocollo WebSocket**: ad esempio [per ASP.NET SignalR] o [socket.io](https://socket.io/).
    - **Sempre acceso**: Mantenere l'applicazione caricata anche quando non c'è traffico. È necessario per WebJobs continui o per WebJobs che vengono attivati utilizzando un'espressione CRON.
      > [!NOTE]
      > Con la funzionalità Always On, non è possibile controllare l'endpoint. Invia sempre una richiesta alla radice dell'applicazione.
    - **Versione pipeline gestita**: [modalità pipeline]IIS . Impostarlo su **Classico** se si dispone di un'app legacy che richiede una versione precedente di IIS.
    - **Versione HTTP**: impostare su **2.0** per abilitare il supporto per il protocollo [HTTPS/2.](https://wikipedia.org/wiki/HTTP/2)
    > [!NOTE]
    > I browser più recenti supportano il protocollo HTTP/2 solo su TLS, mentre il traffico non crittografato continua a usare il protocollo HTTP/1.1. Per garantire che i browser client si connettano all'app con HTTP/2, proteggi il nome DNS personalizzato. Per altre informazioni, vedere [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel servizio app](configure-ssl-bindings.md)di Azure.For more information, see Secure a custom DNS name with a TLS/SSL binding in Azure App Service .
    - **Affinità ARR**: in una distribuzione a più istanze, assicurarsi che il client venga instradato alla stessa istanza per tutta la durata della sessione. È possibile impostare questa opzione su **Disattivato** per le applicazioni senza stato.
- **Debug**: abilita il debug remoto per [ASP.NET,](troubleshoot-dotnet-visual-studio.md#remotedebug) [ASP.NET core](/visualstudio/debugger/remote-debugging-azure)o [Node.js.](containers/configure-language-nodejs.md#debug-remotely) Questa opzione si spegne automaticamente dopo 48 ore.
- **Certificati client in ingresso**: richiedono certificati client [nell'autenticazione reciproca](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurare i documenti predefiniti

Questa impostazione è solo per le app di Windows.

Nel [portale]di Azure cercare e selezionare **App Services**e quindi selezionare l'app. Nel menu a sinistra dell'app, seleziona Documenti**predefiniti**di **configurazione.** > 

![Documenti predefiniti](./media/configure-common/open-documents.png)

Il documento predefinito è la pagina Web visualizzata all'URL radice di un sito Web. Viene utilizzato il primo file corrispondente dell'elenco. Per aggiungere un nuovo documento predefinito, fare clic su **Nuovo documento**. Non dimenticare di fare clic su **Salva**.

Se l'app usa moduli che si instradano in base all'URL invece di pubblicare contenuto statico, non è necessario che i documenti predefiniti siano necessari.

## <a name="configure-path-mappings"></a>Configurare i mapping dei percorsi

Nel [portale]di Azure cercare e selezionare **App Services**e quindi selezionare l'app. Nel menu a sinistra dell'app, seleziona**Mapping percorso di** **configurazione** > .

![Mapping di tracciati](./media/configure-common/open-path.png)

La pagina **Mapping percorso** mostra cose diverse in base al tipo di sistema operativo.

### <a name="windows-apps-uncontainerized"></a>App di Windows (non containerizzate)

Per le app di Windows, è possibile personalizzare i mapping dei gestori IIS e le applicazioni e le directory virtuali.

I mapping dei gestori consentono di aggiungere processori di script personalizzati per gestire le richieste di estensioni di file specifiche. Per aggiungere un gestore personalizzato, fare clic su **Nuovo gestore**. Configurare il gestore come segue:

- **Estensione**. Estensione di file che si * \** desidera gestire, ad esempio .php o *handler.fcgi*.
- **Processore di script**. Percorso assoluto dell'elaboratore di script. Le richieste ai file che corrispondono all'estensione del file vengono elaborate dall'elaboratore di script. Utilizzare il percorso `D:\home\site\wwwroot` per fare riferimento alla directory radice della propria app.
- **Argomenti**. Argomenti facoltativi della riga di comando per l'elaboratore di script.

A ogni app è`/`mappato il `D:\home\site\wwwroot`percorso radice predefinito ( ) a , in cui il codice viene distribuito per impostazione predefinita. Se la radice dell'app si trova in una cartella diversa o se il repository contiene più di un'applicazione, è possibile modificare o aggiungere applicazioni virtuali e directory qui. Fare clic su **Nuova applicazione virtuale o directory**.

Per configurare applicazioni e directory virtuali, specificare ogni directory virtuale`D:\home`e il percorso fisico corrispondente relativo alla radice del sito Web ( ). È facoltativamente possibile selezionare la casella di controllo **Applicazione** in modo da contrassegnare una directory virtuale come applicazione.

### <a name="containerized-apps"></a>App containerizzate

È possibile [aggiungere spazio di archiviazione personalizzato per l'app in contenitori.](containers/how-to-serve-content-from-azure-storage.md) Le app containerizzate includono tutte le app Linux e anche i contenitori personalizzati di Windows e Linux in esecuzione nel servizio app. Fare clic su **Nuovo montaggio di Archiviazione di Azure** e configurare l'archiviazione personalizzata come segue:Click New Azure Storage Mount and configure your custom storage as follows:

- **Nome**: Il nome visualizzato.
- **Opzioni di configurazione**: **Di base** o **Avanzate**.
- **Account di archiviazione:** l'account di archiviazione con il contenitore desiderato.
- **Tipo di archiviazione:** BLOB di **Azure** o file di **Azure**.
  > [!NOTE]
  > Windows container apps only support Azure Files.
- **Contenitore di archiviazione**: Per la configurazione di base, il contenitore desiderato.
- **Nome condivisione**: Per la configurazione avanzata, il nome della condivisione file.
- **Tasto di scelta**: Per la configurazione avanzata, la chiave di accesso.
- **Percorso di montaggio**: il percorso assoluto nel contenitore per montare l'archiviazione personalizzata.

Per altre informazioni, vedere [Rendere disponibile contenuto di Archiviazione di Azure nel servizio app in Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Configurare le impostazioni dello stack di lingua

Per le app Linux, vedere:For Linux apps, see:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurare contenitori personalizzati

Vedere [Configurare un contenitore Linux personalizzato per](containers/configure-custom-container.md) il servizio app di AzureSee Configure a custom Linux container for Azure App Service

## <a name="next-steps"></a>Passaggi successivi

- [Configurare un nome di dominio personalizzato nel servizio app di Azure]
- [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure]
- [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel servizio app di AzureSecure a custom DNS name with a TLS/SSL binding in Azure App Service](configure-ssl-bindings.md)
- [Abilitare i log di diagnostica](troubleshoot-diagnostic-logs.md)
- [Aumentare le prestazioni di un'app nel Servizio app di Azure]
- [Informazioni di base sul monitoraggio nel Servizio app di Azure]
- [Modificare le impostazioni di applicationHost.config con applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Portale di AzureAzure Portal]: https://portal.azure.com/
[Configurare un nome di dominio personalizzato nel servizio app di Azure]: ./app-service-web-tutorial-custom-domain.md
[Configurare gli ambienti di gestione temporanea nel Servizio app di Azure]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Informazioni di base sul monitoraggio nel Servizio app di Azure]: ./web-sites-monitor.md
[modalità pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Aumentare le prestazioni di un'app nel Servizio app di Azure]: ./manage-scale-up.md
