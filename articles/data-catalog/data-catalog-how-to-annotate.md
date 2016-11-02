<properties
   pageTitle="Come annotare le origini dati | Microsoft Azure"
   description="Articolo sulle procedure di annotazione di asset di dati nel catalogo di dati di Azure, compresi esperti, tag, descrizioni e nomi descrittivi."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/> 
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/> 


# Come annotare le origini dati

## Introduzione
**Microsoft Azure Data Catalog** è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per origini dati aziendali. In altre parole, Data Catalog permette agli utenti di individuare, comprendere e usare le origini dati e consente alle organizzazioni di ottenere maggior valore dai dati esistenti. Quando un'origine dati viene registrata in Data Catalog, i relativi metadati vengono copiati e indicizzati dal servizio, ma non è tutto. Data Catalog consente agli utenti di specificare propri metadati descrittivi, ad esempio descrizioni e tag, per integrare i metadati estratti dall'origine dati e rendere l'origine dati più comprensibile a un maggior numero di persone.

## Annotazione e crowdsourcing
Oggi tutti hanno un parere. E questo è positivo. Data Catalog riconosce che utenti diversi hanno prospettive diverse sulle origini dati aziendali e che ognuna di queste prospettive può essere utile. Si consideri lo scenario seguente:

* L'amministratore di sistema conosce il contratto di servizio per il server o servizi che ospitano l'origine dati.
* L'amministratore del database conosce la pianificazione del backup per ogni database e le finestre di elaborazione ETL consentite.
* Il proprietario del sistema conosce il processo di richiesta di accesso all'origine dati utilizzato dagli utenti.
* L'amministratore dei dati conosce il modo in cui asset e attributi nell’origine dati si relazionano al modello di dati aziendali.
* L'analista sa come i dati vengono utilizzati nel contesto dei processi aziendali che supporta.

Ognuna di queste prospettive è utile e Data Catalog usa un approccio crowdsourcing ai metadati che consente di acquisirle tutte e usarle per offrire un quadro completo delle origini dati registrate. Usando il portale di Data Catalog, ogni utente può aggiungere e modificare le proprie annotazioni e ha al tempo stesso la possibilità di visualizzare le annotazioni di altri utenti.

## Diversi tipi di annotazioni
Data Catalog supporta i tipi di annotazione seguenti:

| Annotazione | Note |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome descrittivo | È possibile fornire nomi descrittivi a livello di asset di dati, per rendere le risorse di dati più facilmente comprensibili. I nomi descrittivi sono particolarmente utili quando il nome dell'oggetto sottostante è di difficile interpretazione, abbreviato o non significativo per gli utenti. |
| Descrizione | È possibile fornire descrizioni a livello asset di dati e attributo / colonna. Le descrizioni sono annotazioni di testo breve in formato libero che descrivono la prospettiva dell’utente sull’asset dati o sul relativo utilizzo. |
| Tag (tag utente) | I tag possono essere forniti a livello asset di dati e attributo / colonna. I tag utente sono etichette definite dall'utente che possono essere usate per definire categorie di asset di dati o attributi. |
| Tag (tag glossario) | I tag possono essere forniti a livello asset di dati e attributo / colonna. I tag di glossario sono termini di glossario definiti a livello centrale che possono essere usati per definire categorie di asset di dati o attributi tramite una tassonomia aziendale comune. Per altre informazioni, vedere [Come configurare il glossario aziendale per l'assegnazione di tag regolamentata](data-catalog-how-to-business-glossary.md). |
| Esperti | Gli esperti possono essere forniti a livello di asset di dati. Gli esperti identificano utenti o gruppi con prospettive da esperti sui dati e possono fungere come punto di contatto per gli utenti che individuano le origini dei dati registrati e hanno domande le cui risposte non vengono fornite dalle annotazioni esistenti. |
| Richiedere l'accesso | Le informazioni di richiesta di accesso possono essere fornite a livello di asset di dati. Queste informazioni sono per gli utenti che individuano un'origine dati per cui non dispongono di autorizzazioni di accesso. Gli utenti possono immettere l'indirizzo di posta elettronica dell'utente o del gruppo che concede l'accesso, l'URL del processo o lo strumento a cui gli utenti devono accedere, oppure immettere lo stesso processo come testo. |
| Documentazione | La documentazione può essere fornita a livello di asset di dati. La documentazione degli asset è costituita da informazioni in formato RTF che possono includere collegamenti e immagini e fornire informazioni aggiuntive rispetto a descrizioni e tag. |


## Asset con più annotazioni
Selezionando più asset di dati nel portale di Data Catalog, gli utenti possono annotare tutti gli asset selezionati in un'unica operazione. Le annotazioni verranno applicate a tutti gli asset selezionati, rendendo più semplice selezionare e fornire una descrizione coerente e set di tag e esperti per asset di dati correlati.

> [AZURE.NOTE] I tag e gli esperti possono essere specificati anche durante la registrazione degli asset di dati con il tool di registrazione delle origini dati di Data Catalog.

Quando si selezionano più tabelle e viste, nel portale di Data Catalog verranno visualizzate solo le colonne comuni a tutti gli asset di dati selezionati. In questo modo gli utenti possono fornire tag e descrizioni per tutte le colonne con lo stesso nome per tutti gli asset selezionati.

## Individuazione e annotazioni
Così come i metadati estratti dall'origine dati durante la registrazione vengono aggiunti all'indice di ricerca di Data Catalog, anche i metadati forniti dagli utenti vengono indicizzati. Ciò significa che le annotazioni non facilitano soltanto la comprensione dei dati individuati dagli utenti ma che esse rendono anche più semplice individuare gli asset di dati con annotazioni tramite ricerche effettuate utilizzando termini conosciuti dagli utenti stessi.

## Riepilogo
La registrazione di un'origine dati in Data Catalog rende individuabili tali dati copiando i metadati strutturali e descrittivi dall'origine dati al servizio Catalog. Dopo la registrazione di un'origine dati, gli utenti possono specificare annotazioni per facilitare l'individuazione e la comprensione dal portale di Data Catalog.

## Vedere anche
- Per informazioni dettagliate sull'annotazione delle origini dati, vedere l'esercitazione [Introduzione ad Azure Data Catalog](data-catalog-get-started.md).

<!---HONumber=AcomDC_0921_2016-->