<properties
   pageTitle="Cause comuni del riciclo dei ruoli del servizio cloud | Microsoft Azure"
   description="Un ruolo del servizio cloud che viene riciclato improvvisamente può causare tempi di inattività significativi. Ecco alcuni problemi comuni che causano il riciclo dei ruoli, che possono essere utili per ridurre i tempi di inattività."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# Problemi comuni che comportano il riciclo dei ruoli

Questo articolo illustra alcune delle cause comuni dei problemi di distribuzione e fornisce suggerimenti per consentirne la risoluzione. La presenza di un problema con un'applicazione viene evidenziata se l'istanza del ruolo non si avvia o se lo stato passa ciclicamente da inizializzazione a occupato e arresto.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Dipendenze di runtime mancanti

Se un ruolo nell'applicazione si basa su un'assembly che non fa parte di .NET Framework o della libreria gestita di Azure, è necessario includere tale assembly in modo esplicito nel pacchetto dell'applicazione. Tenere presente che in Azure non sono disponibili per impostazione predefinita altri framework di Microsoft. Se il ruolo si basa su un framework di questo tipo, è necessario aggiungere tali assembly al pacchetto dell'applicazione.

Prima di compilare e includere in un pacchetto l'applicazione, verificare quanto segue:

- Se si usa Visual Studio, assicurarsi che la proprietà **Copy Local** sia impostata su **True** per ogni assembly di riferimento nel progetto che non fa parte di Azure SDK o .NET Framework.

- Verificare che il file web.config non faccia riferimento ad alcun assembly inutilizzato nell'elemento di compilazione.

- La proprietà **Build Action** di ciascun file con estensione cshtml è impostata su **Content**. Ciò garantisce che i file vengano visualizzati correttamente nel pacchetto e consente la visualizzazione anche di altri file di riferimento.

## Riferimento dell'assembly alla piattaforma errata

Azure è un ambiente a 64 bit, pertanto gli assembly .NET compilati per una destinazione a 32 bit non funzioneranno in Azure.

## Generazione di eccezioni non gestite da parte del ruolo durante l'inizializzazione o l'arresto

Eventuali eccezioni generate dai metodi della classe [RoleEntryPoint], che include i metodi [OnStart], [OnStop] e [Run], sono eccezioni non gestite. Se si verifica un'eccezione non gestita in uno di questi metodi, il ruolo verrà riciclato. Se il ruolo viene riciclato più volte, è possibile che venga generata un'eccezione non gestita a ogni tentativo di avvio.

## Restituzioni del ruolo dal metodo Run

È previsto che il metodo [Run], venga eseguito in modo illimitato. Se tramite il codice viene eseguito l'override del metodo [Run], il codice deve essere sospeso per un periodo illimitato. Se tramite il metodo [Run] viene restituito un valore, il ruolo viene riciclato.

## Impostazione di DiagnosticsConnectionString errata

Se l'applicazione usa Diagnostica di Azure, il file cscfg deve specificare l'impostazione di configurazione `DiagnosticsConnectionString`. Questa impostazione deve specificare una connessione HTTPS all'account di archiviazione in Azure.

Per assicurarsi che l'impostazione `DiagnosticsConnectionString` sia corretta, prima di distribuire il pacchetto dell'applicazione in Azure, verificare quanto segue:

- L'impostazione `DiagnosticsConnectionString` punti a un account di archiviazione valido in Azure. Per impostazione predefinita, questa impostazione punta all'account di archiviazione emulato, pertanto è necessario modificarla in modo esplicito prima di distribuire il pacchetto dell'applicazione. Se non si modifica questa impostazione, viene generata un'eccezione quando l'istanza del ruolo tenta di avviare il monitoraggio di diagnostica. Ciò potrebbe comportare il riciclo illimitato dell'istanza del ruolo.

- La stringa di connessione viene specificata nel [formato](../storage/storage-configure-connection-string.md) seguente. Il protocollo deve essere specificato come HTTPS. Sostituire *MyAccountName* con il nome dell'account di archiviazione e *MyAccountKey* con la chiave di accesso dell'account:

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se l'applicazione viene sviluppata usando Strumenti di Azure per Microsoft Visual Studio, è possibile usare le [pagine delle proprietà](https://msdn.microsoft.com/library/ee405486) per impostare questo valore.

## Chiave privata non inclusa nel certificato esportato

Per eseguire un ruolo Web in SSL, è necessario assicurarsi che nel certificato di gestione esportato sia inclusa la chiave privata. Se si utilizza *Gestione certificati di Windows* per esportare il certificato, assicurarsi di selezionare **Sì** per l'opzione **Esporta la chiave privata**. Il certificato deve essere esportato nel formato con estensione PFX, l'unico attualmente supportato.

## Passaggi successivi

Altri [articoli sulla risoluzione dei problemi](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) per i servizi cloud.

Per altri scenari di riciclo dei ruoli, vedere la [serie di blog di Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx

<!---HONumber=AcomDC_0907_2016-->