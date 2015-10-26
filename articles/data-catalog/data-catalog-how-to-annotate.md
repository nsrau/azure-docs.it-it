<properties
   pageTitle="Come annotare le origini dati"
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
   ms.date="08/17/2015"
   ms.author="maroche"/>


# Come annotare le origini dati

## Introduzione
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per origini dati aziendali. In altre parole, il **Catalogo dati di Azure** consente agli utenti di individuare, comprendere e usare origini dati e aiuta le organizzazioni a ottenere maggior valore dai dati esistenti. Quando un'origine dati è stata registrata con **Catalogo dati Azure**, i relativi metadati vengono copiati e indicizzati dal servizio, ma non è tutto. **Catalogo dati azure** consente agli utenti di fornire i propri metadati descrittivi, ad esempio descrizioni e tag – per integrare i metadati estratti dall'origine dati e rendere più comprensibile l'origine dati a più persone.

## Annotazione e crowdsourcing
Oggi tutti hanno un parere. E questo è positivo. **Catalogo dati azure** riconosce che utenti diversi hanno prospettive diverse sulle origini dati aziendali e ognuna di queste prospettive può essere utile. Si consideri lo scenario seguente:

* L'amministratore di sistema conosce il contratto di servizio per il server o servizi che ospitano l'origine dati.
* L'amministratore del database conosce la pianificazione del backup per ogni database e le finestre di elaborazione ETL consentite.
* Il proprietario del sistema conosce il processo di richiesta di accesso all'origine dati utilizzato dagli utenti.
* L'amministratore dei dati conosce il modo in cui asset e attributi nell’origine dati si relazionano al modello di dati aziendali.
* L'analista sa come i dati vengono utilizzati nel contesto dei processi aziendali che supporta.

Ognuna di queste prospettive è utile, e **Catalogo dati Azure** utilizza un approccio di crowdsourcing per i metadati che consentono a ciascuno di essi di essere acquisito e utilizzato per fornire un quadro completo delle origini dati registrati. Utilizzando il portale **Catalogo dati Azure**, ogni utente può aggiungere e modificare le proprie annotazioni, avendo inoltre la possibilità di visualizzare annotazioni fornite da altri utenti.

## Diversi tipi di annotazioni
Durante l’anteprima **Catalogo dati Azure**, sono supportati i seguenti tipi di annotazioni:

| Annotazione | Note |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome descrittivo | È possibile fornire nomi descrittivi a livello di asset di dati, per rendere le risorse di dati più facilmente comprensibili. I nomi descrittivi sono particolarmente utili quando il nome dell'oggetto sottostante è di difficile interpretazione, abbreviato o non significativo per gli utenti. |
| Descrizione | È possibile fornire descrizioni a livello asset di dati e attributo / colonna. Le descrizioni sono annotazioni di testo breve in formato libero che descrivono la prospettiva dell’utente sull’asset dati o sul relativo utilizzo. |
| Tag | I tag possono essere forniti a livello asset di dati e attributo / colonna. I tag sono etichette definite dall'utente che possono essere utilizzate per classificare asset di dati o attributi. |
| Esperti | Gli esperti possono essere forniti a livello di asset di dati. Gli esperti identificano utenti o gruppi con prospettive da esperti sui dati e possono fungere come punto di contatto per gli utenti che individuano le origini dei dati registrati e hanno domande le cui risposte non vengono fornite dalle annotazioni esistenti. |
| Richiedere l'accesso | Le informazioni di richiesta di accesso possono essere fornite a livello di asset di dati. Queste informazioni sono per gli utenti che individuano un'origine dati per cui non dispongono di autorizzazioni di accesso. Gli utenti possono immettere l'indirizzo di posta elettronica dell'utente o del gruppo che concede l'accesso, l'URL del processo o lo strumento a cui gli utenti devono accedere, oppure immettere lo stesso processo come testo. |

## Asset con più annotazioni
Quando si selezionano asset con più dati nel portale **Catalogo dati Azure**, gli utenti possono annotare tutti gli asset selezionati in un'unica operazione. Le annotazioni verranno applicate a tutti gli asset selezionati, rendendo più semplice selezionare e fornire una descrizione coerente e set di tag e esperti per asset di dati correlati.

> [AZURE.NOTE]I tag e gli esperti possono anche essere forniti durante la registrazione degli asset di dati utilizzando lo strumento di registrazione origine dati di **Catalogo dati Azure**.

Quando si selezionano più tabelle e viste, verranno visualizzate solo le colonne comuni a tutti gli asset di dati selezionati nel portale **Catalogo dati Azure**. In questo modo gli utenti possono fornire tag e descrizioni per tutte le colonne con lo stesso nome per tutti gli asset selezionati.

## Individuazione e annotazioni
I metadati estratti dai dati di origine durante la registrazione e i metadati forniti dagli utenti vengono aggiunti all’indice di ricerca del **Catalogo dei dati di Azure**. Ciò significa che le annotazioni non facilitano soltanto la comprensione dei dati individuati dagli utenti ma che esse rendono anche più semplice individuare gli asset di dati con annotazioni tramite ricerche effettuate utilizzando termini conosciuti dagli utenti stessi.

## Riepilogo
La registrazione di un'origine dati con il **Catalogo dati di Azure** rende più semplice individuare e comprendere l'origine dati, copiando i metadati strutturali e descrittivi dall'origine dati nel servizio Catalogo. Una volta registrata un'origine dati, gli utenti possono fornire le annotazioni affinché venga individuata e compresa in maniera più semplice dal portale **Catalogo dati Azure**.

<!---HONumber=Oct15_HO3-->