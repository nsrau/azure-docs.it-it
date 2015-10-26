<properties 
	pageTitle="Gestire l'accesso anonimo a contenitori e BLOB | Microsoft Azure" 
	description="Informazioni su come rendere disponibili per l'accesso anonimo contenitori e BLOB." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="micurd;tamram"/>

# Gestire l'accesso alle risorse di archiviazione di Azure

## Panoramica

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere alle risorse relative all’account. Se il servizio o l'applicazione deve rendere disponibili queste risorse per altri client senza condividere la chiave di accesso, è possibile usare le seguenti opzioni per consentire l'accesso:

- È possibile impostare le autorizzazioni di un contenitore per consentire l'accesso in lettura anonimo al contenitore e ai relativi BLOB. L’accesso anonimo di lettura è disponibile solo per i contenitori e i BLOB. 

- È possibile esporre una risorsa tramite una firma di accesso condiviso, che consente di delegare l'accesso limitato a un contenitore, un BLOB, una tabella o una coda specificando l'intervallo per il quale le risorse sono disponibili e le relative autorizzazioni di cui disporrà un client.

- È possibile usare criteri di accesso archiviati per gestire le firme di accesso condiviso per un contenitore o i relativi BLOB, una coda o una tabella, o per la condivisione di un file o dei suoi file. I criteri di accesso archiviati offrono un'ulteriore misura di controllo sulle firme di accesso condiviso e consentono di revocare le firme in modo semplice.

## Concedere le autorizzazioni agli utenti anonimi per contenitori e BLOB

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere a un contenitore e ai BLOB in esso contenuti. Se si desidera assegnare a utenti anonimi autorizzazioni di lettura per un contenitore e i relativi BLOB, è possibile impostare le autorizzazioni del contenitore per consentire l'accesso pubblico. Gli utenti anonimi possono leggere i BLOB presenti in un contenitore accessibile pubblicamente senza effettuare l'autenticazione della richiesta.

I contenitori forniscono le seguenti opzioni per la gestione dell'accesso al contenitore:

- **Accesso in lettura pubblico completo:** I dati BLOB e del contenitore possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

- **Accesso in lettura pubblico solo per i BLOB:** I dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

- **Nessun accesso in lettura pubblico:** I dati BLOB e del contenitore possono essere letti solo dal proprietario dell'account.

>[AZURE.NOTE]Se il servizio richiede un controllo più granulare delle risorse BLOB o se si desidera fornire autorizzazioni per operazioni diverse dalla lettura, è possibile usare una firma di accesso condiviso per rendere una risorsa accessibile agli utenti.

## Funzionalità disponibili per utenti anonimi

Nella tabella seguente sono riportate le operazioni che possono essere richiamate da utenti anonimi quando l'ACL di un contenitore è impostato per consentire l'accesso pubblico.

| Operazione REST | Autorizzazione con accesso in lettura pubblico completo | Autorizzazione con accesso in lettura pubblico solo per BLOB |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | Solo proprietario | Solo proprietario |
| Create Container | Solo proprietario | Solo proprietario |
| Get Container Properties | Tutti | Solo proprietario |
| Get Container Metadata | Tutti | Solo proprietario |
| Set Container Metadata | Solo proprietario | Solo proprietario |
| Get Container ACL | Solo proprietario | Solo proprietario |
| Set Container ACL | Solo proprietario | Solo proprietario |
| Delete Container | Solo proprietario | Solo proprietario |
| List Blobs | Tutti | Solo proprietario |
| Put Blob | Solo proprietario | Solo proprietario |
| Get Blob | Tutti | Tutti |
| Get Blob Properties | Tutti | Tutti |
| Set Blob Properties | Solo proprietario | Solo proprietario |
| Get Blob Metadata | Tutti | Tutti |
| Set Blob Metadata | Solo proprietario | Solo proprietario |
| Put Block | Solo proprietario | Solo proprietario |
| Get Block List (solo blocchi con commit) | Tutti | Tutti |
| Get Block List (solo blocchi senza commit o tutti i blocchi) | Solo proprietario | Solo proprietario |
| Put Block List | Solo proprietario | Solo proprietario |
| Delete Blob | Solo proprietario | Solo proprietario |
| Copy Blob | Solo proprietario | Solo proprietario |
| Snapshot Blob | Solo proprietario | Solo proprietario |
| Lease Blob | Solo proprietario | Solo proprietario |
| Put Page | Solo proprietario | Solo proprietario |
| Get Page Ranges | Tutti | Tutti |


## Vedere anche

- [Autenticazione per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Firme di accesso condiviso: informazioni sul modello di firma di accesso condiviso (SAS)](storage-dotnet-shared-access-signature-part-1.md)
- [Delega dell'accesso con una firma di accesso condiviso](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=Oct15_HO3-->