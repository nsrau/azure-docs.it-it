<properties
   pageTitle="Come aggiornare i progetti alla versione attuale degli strumenti di Azure"
   description="Informazioni su come aggiornare un progetto Azure in Visual Studio alla versione attuale degli strumenti di Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/28/2015"
   ms.author="kempb" />

# Come aggiornare i progetti alla versione attuale degli strumenti di Microsoft Azure per Visual Studio

## Panoramica

Dopo avere installato la versione corrente degli strumenti di Azure (o successiva alla versione 1.6), eventuali progetti creati mediante una versione precedente alla 1.6 (novembre 2011) verranno automaticamente aggiornati all'apertura. Se sono stati creati progetti con la versione 1.6 (novembre 2011) di questi strumenti e la versione è ancora installata, è possibile aprire i progetti nella versione precedente e decidere in seguito se aggiornarli.

## Modifiche apportate al progetto durante l'aggiornamento

Se un progetto viene aggiornato automaticamente o si specifica di volerlo aggiornare, verrà modificato in modo da funzionare con le versioni correnti di determinati assembly e verranno anche modificate alcune proprietà, come descritto in questa sezione. Se il progetto richiede altre modifiche per la compatibilità con la versione più recente degli strumenti, è necessario apportarle manualmente.

- Il file web.config per i ruoli Web e il file app.config per i ruoli di lavoro vengono aggiornati in modo da fare riferimento alla versione più recente di Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.

- Gli assembly Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll e Microsoft.WindowsAzure.ServiceRuntime.dll vengono aggiornati alle nuove versioni.

- I profili di pubblicazione archiviati nel file di progetto Azure (con estensione ccproj) vengono spostati in un file separato con estensione azurePubxml nella sottodirectory **Publish**.

- Alcune proprietà del profilo di pubblicazione vengono aggiornate per supportare funzionalità nuove e modificate. **AllowUpgrade** viene sostituita da **DeploymentReplacementMethod** perché è possibile aggiornare un servizio cloud distribuito in modo simultaneo o incrementale.

- La proprietà **UseIISExpressByDefault** viene aggiunta e impostata su false in modo che il server Web usato per il debug non passi automaticamente da Internet Information Services (IIS) a IIS Express. IIS Express è il server Web predefinito dei progetti creati con le versioni più recenti degli strumenti.

- Se Azure Caching è ospitato in uno o più ruoli del progetto, alcune proprietà nella configurazione del servizio (file con estensione cscfg) e nella definizione del servizio (file con estensione csdef) vengono modificate al momento dell'aggiornamento di un progetto. Se il progetto usa il pacchetto NuGet di Azure Caching, verrà aggiornato alla versione più recente del pacchetto. È necessario aprire il file web.config e verificare che la configurazione client sia stata gestita correttamente durante il processo di aggiornamento. Se si aggiungono riferimenti agli assembly del client Azure Caching senza usare il pacchetto NuGet, questi assembly non verranno aggiornati. È necessario aggiornare manualmente i riferimenti alle nuove versioni.

>[AZURE.IMPORTANT]Per i progetti in F#, è necessario aggiornare manualmente i riferimenti agli assembly di Azure in modo che facciano riferimento alle versioni più recenti.

### Come aggiornare un progetto Azure alla versione corrente

1. Installare la versione corrente degli strumenti di Azure nell'installazione di Visual Studio da usare per il progetto aggiornato, quindi aprire il progetto da aggiornare. Se il progetto è stato creato con una versione degli strumenti di Azure precedente alla 1.6 (novembre 2011), verrà automaticamente aggiornato alla versione corrente. Se il progetto è stato creato con la versione di novembre 2011 e tale versione è ancora installata, verrà aperto in quest'ultima.

1. In Esplora soluzioni aprire il menu di scelta rapida per il nodo del progetto, scegliere **Proprietà** e quindi la scheda **Applicazione** della finestra di dialogo visualizzata.

    La scheda **Applicazione** indica la versione degli strumenti associata al progetto. Se viene visualizzata la versione corrente degli strumenti di Azure, il progetto è già stato aggiornato. Se è stata installata una versione più recente degli strumenti rispetto a quella indicata nella scheda, viene visualizzato il pulsante **Aggiorna**.

1. Scegliere il pulsante **Aggiorna** per aggiornare un progetto alla versione corrente degli strumenti.

1. Creare il progetto, quindi trovare eventuali errori risultanti dalle modifiche API. Per informazioni su come modificare il codice per la nuova versione, vedere la documentazione per l'API specifica.

<!---HONumber=Oct15_HO3-->