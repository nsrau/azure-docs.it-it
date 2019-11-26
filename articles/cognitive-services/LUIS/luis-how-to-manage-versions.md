---
title: Gestisci versioni-LUIS
titleSuffix: Azure Cognitive Services
description: Le versioni consentono di compilare e pubblicare modelli diversi. Una procedura consigliata consiste nel clonare il modello attualmente attivo in una versione diversa dell'app prima di apportare modifiche al modello.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221903"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Usare le versioni per eseguire modifiche e test senza conseguenze per le app di staging o produzione

Le versioni consentono di compilare e pubblicare modelli diversi. Una procedura consigliata consiste nel clonare il modello attualmente attivo in una [versione](luis-concept-version.md) diversa dell'app prima di apportare modifiche al modello. 

Per usare le versioni, aprire l'app selezionando il relativo nome nella pagina **Mie app**, quindi selezionare **Gestisci** nella barra superiore e successivamente **Versioni** nella sezione di navigazione a sinistra. 

L'elenco delle versioni indica quali versioni sono pubblicate, dove sono pubblicate e quale versione è attualmente attiva. 

> [!div class="mx-imgBorder"]
> [![Sezione Gestisci, pagina versioni](./media/luis-how-to-manage-versions/versions-import.png "Gestire una sezione e la pagina versioni")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Clonare una versione

1. Selezionare la versione che si vuole clonare, quindi selezionare **Clone** nella barra degli strumenti. 

2. Nella finestra di dialogo **Clone version** (Clona versione) digitare un nome per la nuova versione, ad esempio "0.2".

   ![Finestra di dialogo Clone Version (Clona versione)](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > L'ID della versione può essere composto solo da caratteri, cifre o '.' e non può superare i 10 caratteri.
 
   Verrà creata una nuova versione con il nome specificato, che verrà impostata come versione attiva.

## <a name="set-active-version"></a>Impostare la versione attiva

Selezionare una versione dall'elenco e quindi selezionare **Activate (attiva** ) dalla barra degli strumenti. 

> [!div class="mx-imgBorder"]
> [![Gestisci sezione, pagina versioni, Esegui un'azione versione](./media/luis-how-to-manage-versions/versions-other.png "Gestisci sezione, pagina versioni, Esegui un'azione versione")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importare la versione

È possibile importare un `.json` o una versione `.lu` dell'applicazione.

1. Selezionare **Importa** dalla barra degli strumenti e quindi selezionare il formato. 

2. Nel comando **Importa nuova versione** della finestra popup, immettere il nuovo nome della versione di dieci caratteri. È sufficiente impostare un ID versione se la versione nel file esiste già nell'app.

    ![Sezione relativa alla gestione della pagina delle versioni, importazione di una nuova versione](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Una volta importata una versione, la nuova versione diventa quella attiva.

### <a name="import-errors"></a>Errori di importazione

* Errori Tokenizer: se si verifica un **errore Tokenizer** durante l'importazione, si sta tentando di importare una versione che usa un [Tokenizer](luis-language-support.md#custom-tokenizer-versions) diverso da quello attualmente usato dall'app. Per risolvere questo problema, vedere la pagina relativa alla [migrazione tra le versioni Tokenizer](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Altre azioni

* Per **eliminare** una versione, selezionare una versione dall'elenco, quindi selezionare **Elimina** nella barra degli strumenti. Selezionare **OK**. 
* Per **eliminare** una versione, selezionare una versione dall'elenco, quindi selezionare **Rinomina** nella barra degli strumenti. Immettere un nuovo nome e selezionare **Fatto**. 
* Per **esportare** una versione, selezionare una versione dall'elenco, quindi selezionare **Esporta app** nella barra degli strumenti. Scegliere JSON per l'esportazione per il backup, scegliere **Esporta per contenitore** per [usare questa app in un contenitore Luis](luis-container-howto.md).  

