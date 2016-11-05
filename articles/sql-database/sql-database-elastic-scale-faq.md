---
title: Domande frequenti su Scalabilità elastica del database SQL di Azure | Microsoft Docs
description: Domande frequenti sulla scalabilità elastica del database SQL di Azure.
services: sql-database
documentationcenter: ''
manager: jhubbard
author: ddove
editor: ''

ms.service: sql-database
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2016
ms.author: ddove

---
# Domande frequenti sugli strumenti di database elastici
#### In presenza di un tenant singolo per partizione senza chiave di partizionamento orizzontale, in che modo è possibile popolare la chiave di partizionamento orizzontale per le informazioni sullo schema?
L'oggetto di informazioni sullo schema viene usato solo in scenari di divisione e unione. Se un'applicazione è intrinsecamente single-tenant, non richiede lo strumento di divisione e unione e quindi non è necessario popolare l'oggetto di informazioni sullo schema.

#### Quando è stato eseguito il provisioning di un database e si dispone già di un Gestore mappe partizioni, come è possibile registrare il nuovo database come partizione?
Vedere **[Aggiungere una partizione a un'applicazione usando la libreria client del database elastico](sql-database-elastic-scale-add-a-shard.md)**.

#### Quanto costano gli strumenti di database elastici?
L'uso della libreria di database elastico è gratuito. È previsto un costo solo per i database SQL di Azure che vengono usati per le partizioni e il Gestore mappe partizioni, nonché per i ruoli di lavoro/Web di cui viene eseguito il provisioning per lo strumento di divisione e unione.

#### Perché le credenziali personali non funzionano quando si aggiunge una partizione da un server diverso?
Non usare credenziali in formato "ID utente=nomeutente@nomeserver", ma usare semplicemente "ID utente = nomeutente". Verificare inoltre che il “nome utente” di accesso disponga di autorizzazioni sulla partizione.

#### È necessario creare un Gestore mappe partizioni e popolare le partizioni ogni volta che si avviano le applicazioni?
No. La creazione del Gestore mappe partizioni, ad esempio **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**, è un'operazione che si esegue una volta sola. L'applicazione deve usare la chiamata **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** al momento dell'avvio dell'applicazione. È supportata una sola chiamata di questo tipo per dominio di applicazione.

#### In che modo è possibile ottenere risposte alle domande sugli strumenti di database elastici?
È possibile partecipare al [forum su Database SQL di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### Quando si ottiene una connessione di database usando una chiave di partizionamento orizzontale, è possibile eseguire query sui dati per altre chiavi di partizionamento orizzontale sulla stessa partizione. Si tratta di un comportamento previsto da progettazione?
Le API di Scalabilità elastica offrono una connessione al database corretto per la propria chiave di partizionamento orizzontale, ma non forniscono filtri per le chiavi di partizionamento orizzontale. Aggiungere la clausola **WHERE** alla query per limitare l'ambito alla chiave di partizionamento orizzontale fornita, se necessario.

#### È possibile usare un'edizione del database di Azure diversa per ogni partizione nel set di partizioni?
Sì, una partizione è un database a sé, per cui è possibile che una partizione sia un'edizione Premium mentre un'altra è un'edizione Standard. Inoltre, l'edizione della partizione può essere aumentata o ridotta più volte durante il ciclo di vita della partizione.

#### Lo strumento di divisione e unione esegue il provisioning di un database o lo elimina durante un'operazione di divisione o unione?
No. Per le operazioni di **divisione** è necessario che il database di destinazione sia dotato dello schema appropriato e sia registrato nel Gestore mappe partizioni. Per le operazioni di **unione** è necessario eliminare la partizione dal Gestore mappe partizioni e quindi eliminare il database.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!---HONumber=AcomDC_0601_2016-->