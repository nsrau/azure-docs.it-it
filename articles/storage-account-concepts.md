<properties umbracoNaviHide="0" pageTitle="Concetti sull'account di archiviazione | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Informazioni sui concetti relativi all'account di archiviazione." urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Concetti sull'account di archiviazione" services="storage" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Concetti sull'account di archiviazione

## Opzioni di replica dell'account di archiviazione

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## Endpoint dell'account di archiviazione

Gli *endpoint* di un account di archiviazione rappresentano il livello più elevato dello spazio dei nomi per l'accesso a BLOB, tabelle o code. I formati degli endpoint predefiniti per un account di archiviazione sono i seguenti:

-   Servizio BLOB: http://*accountarchiviazionepersonale*.blob.core.windows.net

-   Servizio tabelle: http://*accountarchiviazionepersonale*.table.core.windows.net

-   Servizio di accodamento: http://*accountarchiviazionepersonale*.queue.core.windows.net

-   Servizio file: http://*accountarchiviazionepersonale*.file.core.windows.net

L'URL per accedere a un oggetto in un account di archiviazione viene formato aggiungendo la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, il formato di un indirizzo BLOB è simile al seguente: http://*accountarchiviazionepersonale*.blob.core.windows.net/*contenitorepersonale*/*BLOBpersonale*.

## Sicurezza dell'account di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono usate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio.

È consigliabile non condividere le chiavi di accesso dell'account di archiviazione con altri utenti. Se si sospetta che l'account sia stato compromesso, è possibile rigenerare le chiavi di accesso dal portale. Selezionare l'account di archiviazione e scegliere **Gestisci chiavi di accesso**.

Per permettere l'accesso alle risorse di archiviazione senza fornire le chiavi di accesso, è possibile usare una *firma di accesso condiviso*. Una firma di accesso condiviso fornisce l'accesso a una risorsa nell'account per un intervallo di tempo definito e con le autorizzazioni specificate. Per altre informazioni, vedere l'[esercitazione sulla firma di accesso condiviso][esercitazione sulla firma di accesso condiviso].

## Metriche e registrazione per l'account di archiviazione

-   **Metriche minime e dettagliate** Nelle impostazioni di monitoraggio per l'account di archiviazione è possibile configurare metriche minime o dettagliate. Le *metriche minime* consentono di raccogliere metriche sui dati come ingresso/uscita, disponibilità, latenza e percentuali di successo aggregate per i servizi BLOB, tabelle e di accodamento. Le *metriche dettagliate* consentono di raccogliere i dettagli a livello delle operazioni oltre ad aggregati a livello del servizio per le stesse metriche. Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione. Per l'elenco completo delle metriche disponibili, vedere [Schema di tabella della metrica di Analisi di archiviazione][Schema di tabella della metrica di Analisi di archiviazione]. Per altre informazioni sul monitoraggio dell'archiviazione, vedere [Informazioni sulle metriche di Analisi archiviazione][Informazioni sulle metriche di Analisi archiviazione].

-   **Registrazione** La registrazione è una funzionalità configurabile degli account di archiviazione che consente la registrazione delle richieste di lettura, scrittura ed eliminazione di BLOB, tabelle e code. È possibile usare il portale di gestione di Azure per configurare la registrazione, ma per visualizzare i log, in quanto questi sono archiviati nel contenitore $logs dell'account di archiviazione. Per altre informazioni, vedere le informazioni generali fornite in [Analisi archiviazione][Analisi archiviazione].

## Gruppi di affinità per la condivisione di percorso tra l'Archiviazione di Azure e altri servizi

Un *gruppo di affinità* è un raggruppamento geografico dei servizi e delle VM di Azure con l'account di archiviazione di Azure. che consente di migliorare le prestazioni del servizio individuando i carichi di lavoro del computer nello stesso data center o in prossimità dei destinatari. Non sono inoltre addebitate spese per l'uscita in caso di accesso ai dati di un account di archiviazione da parte di un altro servizio appartenente allo stesso gruppo di affinità.

  [esercitazione sulla firma di accesso condiviso]: ../storage-dotnet-shared-access-signature-part-1/
  [Schema di tabella della metrica di Analisi di archiviazione]: http://msdn.microsoft.com/it-it/library/windowsazure/hh343264.aspx
  [Informazioni sulle metriche di Analisi archiviazione]: http://msdn.microsoft.com/it-it/library/windowsazure/hh343258.aspx
  [Analisi archiviazione]: http://msdn.microsoft.com/it-it/library/windowsazure/hh343268.aspx
