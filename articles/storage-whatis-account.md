<properties urlDisplayName="What is a Storage Account" pageTitle="Informazioni sull'account di archiviazione | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />





#Informazioni sull'account di archiviazione

In Archiviazione di Azure sono inclusi tre servizi: archiviazione BLOB, archiviazione tabelle e archiviazione delle code. Questi servizi sono disponibili in tutti gli account di archiviazione. Un account di archiviazione offre uno spazio dei nomi univoco per l'utilizzo di BLOB, code e tabelle.

Per informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/it-it/library/dn249410.aspx).

Tutte le informazioni relative all'account di archiviazione, inclusa la data di creazione, sono disponibili nel portale di gestione, nella pagina **Dashboard** per **Archiviazione**.

I costi di archiviazione sono basati su quattro fattori: capacità di archiviazione, schema di replica, transazioni di archiviazione e uscita dei dati. La capacità di archiviazione fa riferimento alla quantità di unità di servizio dell'account di archiviazione in uso per l'archiviazione dei dati. Il costo dell'archiviazione dei dati è determinato dalla quantità di dati archiviata e dalla modalità di replica dei dati. Le transazioni fanno riferimento a tutte le operazioni di lettura e scrittura in Archiviazione di Azure. L'uscita dati fa riferimento ai dati trasferiti all'esterno di un'area di Azure. Quando un'applicazione non in esecuzione nella stessa area geografica accede ai dati nell'account di archiviazione, indipendentemente dal fatto che l'applicazione sia un servizio cloud o un altro tipo di applicazione, verrà addebitato il trasferimento dei dati in uscita. Per i servizi di Azure è possibile raggruppare dati e servizi nello stesso data center per ridurre o evitare del tutto gli addebiti per il trasferimento dei dati in uscita.  

Nella pagina [Dettagli prezzi di archiviazione](http://www.windowsazure.com/it-it/pricing/details/#storage) vengono fornite informazioni dettagliate sui prezzi per la capacità, la replica e le transazioni relative all'archiviazione. Nella pagina [Dettagli prezzi dei trasferimenti di dati](http://www.windowsazure.com/it-it/pricing/details/data-transfers/) vengono fornite informazioni dettagliate sui prezzi per il trasferimento dei dati in uscita.

##Concetti sull'account di archiviazione

### Opzioni di replica dell'account di archiviazione

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


### Endpoint dell'account di archiviazione 

Gli *endpoint* di un account di archiviazione rappresentano il livello più elevato dello spazio dei nomi per l'accesso a BLOB, tabelle o code. I formati degli endpoint predefiniti per un account di archiviazione sono i seguenti: 

- Servizio BLOB: http://*mystorageaccount*.blob.core.windows.net

- Servizio tabelle: http://*mystorageaccount*.table.core.windows.net

- Servizio di accodamento: http://*mystorageaccount*.queue.core.windows.net

- Servizio file: http://*mystorageaccount*.file.core.windows.net

L'URL per accedere a un oggetto in un account di archiviazione viene formato aggiungendo la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, il formato di un indirizzo BLOB è simile al seguente: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

### Sicurezza dell'account di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso alle risorse di archiviazione da 512 bit, che vengono usate per l'autenticazione quando si accede all'account di archiviazione. Fornendo due chiavi di accesso alle risorse di archiviazione, Azure consente di rigenerare le chiavi senza interruzioni per il servizio di archiviazione o l'accesso a tale servizio.

È consigliabile non condividere le chiavi di accesso dell'account di archiviazione con altri utenti. Se si sospetta che l'account sia stato compromesso, è possibile rigenerare le chiavi di accesso dal portale. Selezionare l'account di archiviazione e scegliere **Gestisci chiavi di accesso**.

Per permettere l'accesso alle risorse di archiviazione senza fornire le chiavi di accesso, è possibile usare una *firma di accesso condiviso*. Una firma di accesso condiviso fornisce l'accesso a una risorsa nell'account per un intervallo di tempo definito e con le autorizzazioni specificate. Per altre informazioni, vedere l'[esercitazione sulla firma di accesso condiviso](../storage-dotnet-shared-access-signature-part-1/).

### Metriche e registrazione per l'account di archiviazione

- **Metriche minime e dettagliate**   Nelle impostazioni di monitoraggio per l'account di archiviazione è possibile configurare metriche minime o dettagliate. Le *metriche minime* consentono di raccogliere metriche sui dati come ingresso/uscita, disponibilità, latenza e percentuali di successo aggregate per i servizi BLOB, tabelle e di accodamento. Le *metriche dettagliate* consentono di raccogliere i dettagli a livello delle operazioni oltre ad aggregati a livello del servizio per le stesse metriche. Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione. Per l'elenco completo delle metriche disponibili, vedere [Schema di tabella della metrica di Analisi di archiviazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh343264.aspx). Per altre informazioni sul monitoraggio dell'archiviazione, vedere [Informazioni sulle metriche di Analisi archiviazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh343258.aspx).

- **Registrazione**   La registrazione è una funzionalità configurabile degli account di archiviazione che consente la registrazione delle richieste di lettura, scrittura ed eliminazione di BLOB, tabelle e code. È possibile usare il portale di gestione di Azure per configurare la registrazione, ma per visualizzare i log, in quanto questi sono archiviati nel contenitore $logs dell'account di archiviazione. Per altre informazioni, vedere le informazioni generali fornite in [Analisi archiviazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh343268.aspx).

### Gruppi di affinità per la condivisione di percorso tra l'Archiviazione di Azure e altri servizi

Un *gruppo di affinità* è un raggruppamento geografico dei servizi e delle VM di Azure con l'account di archiviazione di Azure. che consente di migliorare le prestazioni del servizio individuando i carichi di lavoro del computer nello stesso data center o in prossimità dei destinatari. Non sono inoltre addebitate spese per l'uscita in caso di accesso ai dati di un account di archiviazione da parte di un altro servizio appartenente allo stesso gruppo di affinità.

