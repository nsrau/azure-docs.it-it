---
title: Procedure consigliate - QnA Maker - Servizi cognitivi di Azure | Microsoft Docs
description: Usare queste procedure consigliate per migliorare la knowledge base e fornire risultati migliori per gli utenti finali dell'applicazione/chat bot.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 7a85ebbc3892a90e98e73a73425c1f8ec1de0b35
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378689"
---
# <a name="best-practices"></a>Procedure consigliate
Il [ciclo di vita di sviluppo della knowledge base](../Concepts/development-lifecycle-knowledge-base.md) fornisce informazioni sulla gestione della KB end-to-end. Usare queste procedure consigliate per migliorare la knowledge base e fornire risultati migliori per gli utenti finali dell'applicazione/chat bot.

## <a name="extraction"></a>Estrazione
QnA Maker migliora continuamente gli algoritmi di estrazione di domande e risposte dal contenuto, espandendo l'elenco di file e formati di pagina HTML supportati. Seguire le [linee guida](../Concepts/data-sources-supported.md) per l'estrazione in base al tipo di documento da cui si esegue l'estrazione. 

In generale, le pagine di domande frequenti devono essere autonome e non combinate con altre informazioni. I manuali di prodotti devono avere titoli chiari e preferibilmente una pagina di indice. 

## <a name="rankingmatching"></a>Classificazione/corrispondenza
Assicurarsi di usare al meglio le funzionalità di classificazione supportate da QnA Maker. In questo modo sarà possibile migliorare la probabilità che una specifica query utente riceva una risposta appropriata.

### <a name="add-alternate-questions"></a>Aggiungere domande alternative
Le [domande alternative](../How-To/edit-knowledge-base.md) aumentano la probabilità di una corrispondenza con una query utente. Le domande alternative sono utili quando ci sono più modi per porre la stessa domanda. Potrebbe trattarsi di modifiche nella struttura della frase (ad esempio, *"È disponibile il parcheggio?"* piuttosto che *"Avete un parcheggio?"*) o di modifiche nello stile della parola e legate all'uso di gergo (ad esempio, *"Ciao"* piuttosto che *"Salve"*, *"Ehi!"*).

### <a name="use-metadata-filters"></a>Usare filtri dei metadati
I [metadati](../How-To/edit-knowledge-base.md) aiutano a restringere i risultati di una query utente in base ai filtri. La risposta della knowledge base può essere diversa in base al tag di metadati, anche se la query è la stessa. Ad esempio, la domanda *"Dove si trova il parcheggio?"* può avere una risposta diversa se l'attributo Location del ramo relativo al ristorante è diverso, ovvero se i metadati sono *Location: Seattle* piuttosto che *Location: Redmond*.

### <a name="use-synonyms"></a>Usare sinonimi
Sebbene, in parte, sia disponibile il supporto per i sinonimi, usare [variazioni delle parole](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) per aggiungere sinonimi alle parole chiave che accettano un forma diversa (ad esempio: *acquistare* -> *comprare*  oppure *eBank* -> *e-Bank*. I sinonimi devono essere aggiunti a livello di servizio in QnA Maker e condivisi da tutte le knowledge base nel servizio.

### <a name="use-distinct-words-to-differentiate-questions"></a>Usare parole diverse per distinguere le domande
Gli algoritmi di corrispondenza e classificazione di QnA Maker che mettono in corrispondenza una query utente con una domanda nella knowledge base funzionano meglio se ogni domanda risponde a esigenze diverse. La ripetizione dello stesso set di parole tra domande riduce la probabilità di scelta della risposta corretta per una determinata query utente che usa tali parole.

## <a name="collaborate"></a>Collaborare
QnA Maker permette agli utenti di [collaborare](../How-to/collaborate-knowledge-base.md) a una knowledge base. Per accedere alle knowledge base, gli utenti necessitano dell'accesso al gruppo di risorse di QnA Maker. Alcune organizzazioni potrebbero voler assegnare all'esterno le attività di modifica e manutenzione della knowledge base, mantenendo comunque la possibilità di proteggere l'accesso alle risorse di Azure. Questo modello di editor-responsabile approvazione può essere ottenuto configurando due [servizi QnA Maker](../How-to/set-up-qnamaker-service-azure.md) identici in diverse sottoscrizioni e designandone uno per il ciclo di modifica-test. Una volta completati i test, il contenuto della knowledge base può essere trasferito con un processo di [importazione-esportazione](../Tutorials/migrate-knowledge-base.md) al servizio QnA Maker del responsabile approvazione, che infine pubblicherà la knowledge base e aggiornerà l'endpoint.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Modificare una knowledge base](../How-to/edit-knowledge-base.md)

