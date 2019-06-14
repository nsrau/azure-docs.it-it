---
title: Configurare le app nel portale - servizio App di Azure
description: Informazioni su come configurare le impostazioni comuni per un'App del servizio app nel portale di Azure.
keywords: servizio app di Azure, app web, le impostazioni dell'app, le variabili di ambiente
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957911"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configurare un'app di servizio App nel portale di Azure

Questo argomento illustra come configurare le impostazioni comuni per le app web, back-end per dispositivi mobili o app API usando il [portale di Azure].

## <a name="configure-app-settings"></a>Configurare le impostazioni applicazione

Nel servizio App, si usano le impostazioni dell'app, ad esempio le variabili di ambiente. Nel [portale di Azure], passare alla pagina di gestione dell'app. Nel menu a sinistra dell'app, fare clic su **Configuration** > **le impostazioni dell'applicazione**.

![Impostazioni dell'applicazione](./media/configure-common/open-ui.png)

Per gli sviluppatori ASP.NET e ASP.NET Core, le impostazioni dell'impostazione app nel servizio App sono come impostarli `<appSettings>` nelle *Web. config*, ma i valori nel servizio App di eseguire l'override di quelli *Web. config*. È possibile mantenere le impostazioni di sviluppo (ad esempio password MySQL locale) nella *Web. config*, ma i segreti di produzione (ad esempio, la password del database MySQL di Azure)-safe nel servizio App. Lo stesso codice Usa le impostazioni di sviluppo quando si esegue il debug in locale e Usa i segreti di produzione quando distribuito in Azure.

Altri stack di linguaggio, ottenere in modo analogo, le impostazioni dell'app come variabili di ambiente in fase di esecuzione. Per lo stack di linguaggio specifico, vedere:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contenitori personalizzati](containers/configure-custom-container.md#configure-environment-variables)

Le impostazioni dell'app vengono sempre crittografate quando sono archiviate (crittografia dei dati inattivi).

> [!NOTE]
> Le impostazioni dell'App possono inoltre essere risolte dal [Key Vault](/azure/key-vault/) utilizzando [fa riferimento a Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostra i valori nascosti

Per impostazione predefinita, i valori per le impostazioni dell'app sono nascosti nel portale per la sicurezza. Per visualizzare un valore nascosto di un'impostazione dell'app, scegliere il **valore** campo di tale impostazione. Per visualizzare i valori di tutte le impostazioni dell'app, scegliere il **mostrano valore** pulsante.

### <a name="add-or-edit"></a>Aggiungere o modificare

Per aggiungere una nuova impostazione di app, fare clic su **nuova impostazione dell'applicazione**. Nella finestra di dialogo, è possibile [mantenere l'impostazione slot corrente](deploy-staging-slots.md#which-settings-are-swapped).

Per modificare un'impostazione, scegliere il **modifica** pulsante sul lato destro.

Al termine, fare clic su **Update**. Non dimenticare di fare clic su **salvare** nuovamente il **configurazione** pagina.

> [!NOTE]
> In un contenitore Linux predefinito o un contenitore Linux personalizzato, qualsiasi struttura di chiave JSON annidata nel nome dell'impostazione app, ad esempio `ApplicationInsights:InstrumentationKey` deve essere configurato nel servizio App come `ApplicationInsights__InstrumentationKey` per il nome della chiave. In altri termini, qualsiasi `:` devono essere sostituiti da `__` (doppio carattere di sottolineatura).
>

### <a name="edit-in-bulk"></a>Modifica in blocco

Per aggiungere o modificare le impostazioni dell'app in blocco, fare clic sui **Advanced modifica** pulsante. Al termine, fare clic su **Update**. Non dimenticare di fare clic su **salvare** nuovamente il **configurazione** pagina.

Le impostazioni dell'App sono la formattazione JSON seguente:

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

Nel [portale di Azure], passare alla pagina di gestione dell'app. Nel menu a sinistra dell'app, fare clic su **Configuration** > **le impostazioni dell'applicazione**.

![Impostazioni dell'applicazione](./media/configure-common/open-ui.png)

Per gli sviluppatori ASP.NET e ASP.NET Core, le stringhe di connessione impostazione nel servizio App sono come impostarli `<connectionStrings>` nelle *Web. config*, ma i valori impostati nel servizio App di eseguire l'override di quelli *Web. config*. È possibile mantenere le impostazioni di sviluppo (ad esempio, un file di database) in *Web. config* e i segreti di produzione (ad esempio, le credenziali del Database SQL)-safe nel servizio App. Lo stesso codice Usa le impostazioni di sviluppo quando si esegue il debug in locale e Usa i segreti di produzione quando distribuito in Azure.

Per altri stack di linguaggio, è preferibile usare [le impostazioni dell'app](#configure-app-settings) invece, poiché le stringhe di connessione richiedono una formattazione speciale nelle chiavi variabile ordinare per accedere ai valori. Ecco un'unica eccezione, tuttavia: determinati tipi di database di Azure vengono eseguito il backup insieme all'app se si configura le relative stringhe di connessione nell'app. Per altre informazioni, vedere [quali backup](manage-backup.md#what-gets-backed-up). Se non è necessario questo backup automatizzato, quindi usare le impostazioni dell'app.

In fase di esecuzione, le stringhe di connessione sono disponibili come variabili di ambiente, con precedute con i tipi di connessione seguenti:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Database SQL: `SQLAZURECONNSTR_`
* Personalizzato: `CUSTOMCONNSTR_`

Ad esempio, una stringa di connessione MySql denominato *connectionstring1* sono accessibili come variabile di ambiente `MYSQLCONNSTR_connectionString1`. Per lo stack di linguaggio specifico, vedere:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Contenitori personalizzati](containers/configure-custom-container.md#configure-environment-variables)

Le stringhe di connessione vengono sempre crittografate quando sono archiviate (crittografia dei dati inattivi).

> [!NOTE]
> Le stringhe di connessione possono inoltre essere risolte dal [Key Vault](/azure/key-vault/) utilizzando [fa riferimento a Key Vault](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostra i valori nascosti

Per impostazione predefinita, i valori per le stringhe di connessione sono nascosti nel portale per la sicurezza. Per visualizzare un valore nascosto della stringa di connessione, scegliere semplicemente il **valore** campo di tale stringa. Per visualizzare i valori di tutte le stringhe di connessione, scegliere il **mostrano valore** pulsante.

### <a name="add-or-edit"></a>Aggiungere o modificare

Per aggiungere una nuova stringa di connessione, fare clic su **nuova stringa di connessione**. Nella finestra di dialogo, è possibile [incollare la stringa di connessione allo slot corrente](deploy-staging-slots.md#which-settings-are-swapped).

Per modificare un'impostazione, scegliere il **modifica** pulsante sul lato destro.

Al termine, fare clic su **Update**. Non dimenticare di fare clic su **salvare** nuovamente il **configurazione** pagina.

### <a name="edit-in-bulk"></a>Modifica in blocco

Per aggiungere o modificare le stringhe di connessione in blocco, fare clic sui **Advanced modifica** pulsante. Al termine, fare clic su **Update**. Non dimenticare di fare clic su **salvare** nuovamente il **configurazione** pagina.

Stringhe di connessione sono la formattazione JSON seguente:

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

Nel [portale di Azure], passare alla pagina di gestione dell'app. Nel menu a sinistra dell'app, fare clic su **Configuration** > **le impostazioni dell'applicazione**.

![Impostazioni generali](./media/configure-common/open-general.png)

In questo caso, è possibile configurare alcune impostazioni comuni per l'app. Alcune impostazioni occorre [scalabilità fino a piani tariffari superiori](web-sites-scale.md).

- **Stack impostazioni**: Lo stack di software per eseguire l'app, tra cui la lingua e versioni precedenti del SDK. Per le app Linux e App contenitore personalizzato, è possibile impostare anche un file o un comando di avvio facoltativo.
- **Impostazioni piattaforma**: Consente di configurare le impostazioni per la piattaforma di hosting, tra cui:
    - **Numero di bit**: 32 bit o 64 bit.
    - **Protocollo WebSocket**: Per la [ASP.NET SignalR] oppure [socket.io](https://socket.io/), ad esempio.
    - **Always On**: Mantenere l'app caricata anche quando non viene rilevato traffico. È necessario abilitarlo per i processi Web continui o processi Web che vengono attivati mediante un'espressione CRON.
    - **Versione pipeline gestita**: IIS [modalità pipeline]. Impostarla su **classico** se si dispone di un'app legacy che richiede una versione precedente di IIS.
    - **Versione HTTP**: Impostata su **2.0** per abilitare il supporto per il protocollo [HTTPS/2](https://wikipedia.org/wiki/HTTP/2).
    > [!NOTE]
    > I browser più recenti supportano il protocollo HTTP/2 solo su TLS, mentre il traffico non crittografato continua a usare il protocollo HTTP/1.1. Per verificare che tale client browser connettersi all'App con HTTP/2, ovvero [acquistare un certificato del servizio App](web-sites-purchase-ssl-web-site.md) dominio personalizzato dell'app oppure [associare un certificato di terze parti](app-service-web-tutorial-custom-ssl.md).
    - **Affinità ARR**: In una distribuzione a istanze multiple, assicurarsi che il client venga indirizzato alla stessa istanza per la durata della sessione. È possibile impostare questa opzione su **disattivata** per le applicazioni senza state.
- **Debug**: Abilitare il debug remoto per [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), o [Node. js](containers/configure-language-nodejs.md#debug-remotely) app. Questa opzione si disattiva automaticamente dopo 48 ore.
- **Certificati client in infresso**: richiedere certificati client in [l'autenticazione reciproca](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurare i documenti predefiniti

Questa impostazione è solo per le app di Windows.

Nel [portale di Azure], passare alla pagina di gestione dell'app. Nel menu a sinistra dell'app, fare clic su **Configuration** > **documenti predefiniti**.

![Impostazioni generali](./media/configure-common/open-documents.png)

Il documento predefinito è la pagina web che viene visualizzato nell'URL radice per un sito Web. Viene utilizzato il primo file corrispondente dell'elenco. Per aggiungere un nuovo documento predefinito, fare clic su **nuovo documento**. Non dimenticare di fare clic su **salvare**.

Se l'app Usa i moduli che instradano basati sull'URL invece di fornire contenuti statici, non è necessario per i documenti predefiniti.

## <a name="configure-path-mappings"></a>Configurare i mapping di percorso

Nel [portale di Azure], passare alla pagina di gestione dell'app. Nel menu a sinistra dell'app, fare clic su **Configuration** > **mapping tra i percorsi**.

![Impostazioni generali](./media/configure-common/open-path.png)

Il **mapping di percorso** pagina vengono visualizzati diversi elementi in base al tipo del sistema operativo.

### <a name="windows-apps-uncontainerized"></a>App di Windows (uncontainerized)

Per le app di Windows, è possibile personalizzare i mapping dei gestori IIS e le applicazioni virtuali e le directory.

Mapping gestori consentono di aggiungere processori script personalizzati per gestire le richieste per estensioni di file specifiche. Per aggiungere un gestore personalizzato, fare clic su **new_handler**. Configurare il gestore come segue:

- **Estensione**. L'estensione di file che si desidera gestire, ad esempio  *\*PHP* oppure *fcgi*.
- **Processore script**. Il percorso assoluto del processore script all'utente. Le richieste di file che corrispondono all'estensione di file vengono elaborate dal processore script. Utilizzare il percorso `D:\home\site\wwwroot` per fare riferimento alla directory radice della propria app.
- **Gli argomenti**. Argomenti facoltativi della riga di comando per il processore script.

Ogni app ha il percorso radice predefinito (`/`) mappato a `D:\home\site\wwwroot`, in cui il codice viene distribuito per impostazione predefinita. Se la directory radice dell'app si trova in una cartella diversa o se il repository contiene più di un'applicazione, è possibile modificare o aggiungere le applicazioni virtuali e le directory qui. Fare clic su **nuova applicazione virtuale o una directory**.

Per configurare applicazioni e directory virtuali, specificare ogni directory virtuale e il corrispondente percorso fisico relativo alla radice del sito Web (`D:\home`). È facoltativamente possibile selezionare la casella di controllo **Applicazione** in modo da contrassegnare una directory virtuale come applicazione.

### <a name="containerized-apps"></a>App in contenitori

È possibile [aggiungere spazio di archiviazione personalizzato per l'app in contenitori](containers/how-to-serve-content-from-azure-storage.md). Le App in contenitori includono tutte le app di Linux e anche i contenitori personalizzati in Windows e Linux in esecuzione nel servizio App. Fare clic su **archiviazione di Azure nuova montare** e configurare l'archiviazione personalizzata come indicato di seguito:

- **Nome**: Nome visualizzato.
- **Opzioni di configurazione**: **Base** oppure **Advanced**.
- **Account di archiviazione**: L'account di archiviazione con il contenitore.
- **Tipo di archiviazione**: **I BLOB di Azure** oppure **file di Azure**.
  > [!NOTE]
  > App contenitore di Windows supportano solo file di Azure.
- **Contenitore di archiviazione**: Per la configurazione di base, il contenitore desiderato.
- **Nome della condivisione**: Per la configurazione avanzata, nome di condivisione file.
- **Tasto di scelta rapida**: Per la configurazione avanzata, la chiave di accesso.
- **Percorso di montaggio**: Il percorso assoluto nel contenitore per montare l'archiviazione personalizzata.

Per altre informazioni, vedere [rendere disponibile il contenuto verso l'archiviazione di Azure in servizio App in Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Configurare le impostazioni dello stack di linguaggio

Per le app di Linux, vedere:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurare i contenitori personalizzati

Vedere [configurare un contenitore Linux personalizzato per il servizio App di Azure](containers/configure-custom-container.md)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare un nome di dominio personalizzato nel servizio app di Azure]
- [Configurare ambienti di servizio App di Azure di staging]
- [Abilitare HTTPS per un'app in Azure App Service]
- [Abilitare i log di diagnostica](troubleshoot-diagnostic-logs.md)
- [Aumentare le prestazioni di un'app nel Servizio app di Azure]
- [Informazioni di base sul monitoraggio nel Servizio app di Azure]
- [Modificare le impostazioni di applicationHost. config con xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Portale di Azure]: https://portal.azure.com/
[Configurare un nome di dominio personalizzato nel servizio app di Azure]: ./app-service-web-tutorial-custom-domain.md
[Configurare ambienti di servizio App di Azure di staging]: ./deploy-staging-slots.md
[Abilitare HTTPS per un'app in Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Informazioni di base sul monitoraggio nel Servizio app di Azure]: ./web-sites-monitor.md
[modalità pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Aumentare le prestazioni di un'app nel Servizio app di Azure]: ./web-sites-scale.md
