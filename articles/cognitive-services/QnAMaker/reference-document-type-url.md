---
title: Tipi di URL supportati per Import-QnA Maker
description: Informazioni sul modo in cui vengono usati i tipi di URL per importare e creare set di QnA.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651828"
---
# <a name="urls-supported-for-importing-documents"></a>URL supportati per l'importazione di documenti

Informazioni sul modo in cui vengono usati i tipi di URL per importare e creare set di QnA.

## <a name="faq-urls"></a>Indirizzo Web di domande frequenti

QnA Maker può supportare tre diversi formati di pagine Web di domande frequenti:

* Pagine semplici di domande frequenti
* Pagina di domande frequenti con collegamenti
* Pagine di domande frequenti con una Home page degli argomenti

### <a name="plain-faq-pages"></a>Pagine semplici di domande frequenti

Si tratta del tipo più comune di pagina di domande frequenti, in cui le risposte seguono immediatamente le domande nella stessa pagina.

Di seguito è riportato un esempio di pagina semplice di domande frequenti:

![Esempio di pagina semplice di domande frequenti per una knowledge base](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Pagina di domande frequenti con collegamenti

In questo tipo di pagina di domande frequenti, le domande sono aggregate e collegate a risposte che si trovano in diverse sezioni della stessa pagina o in pagine differenti.

Di seguito è riportato un esempio di pagina di domande frequenti con collegamenti a sezioni che si trovano nella stessa pagina:

 ![Esempio di pagina di domande frequenti con collegamenti a sezioni per una knowledge base](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Pagina argomenti padre collegamenti a pagine di risposte figlio

Questo tipo di domande frequenti include una pagina di argomenti in cui ogni argomento è collegato a un set di domande e risposte corrispondente in una pagina diversa. QnA Maker esegue la ricerca per indicizzazione di tutte le pagine collegate per estrarre le domande corrispondenti & risposte.

Di seguito è riportato un esempio di una pagina di argomenti con collegamenti a sezioni di domande frequenti in pagine diverse.

 ![Esempio di pagina di domande frequenti con collegamenti diretti per una knowledge base](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>URL di supporto

QnA Maker è in grado di elaborare pagine web di supporto semi-strutturatee, ad esempio articoli web che descrivono come eseguire una determinata attività, come diagnosticare e risolvere un problema specifico e quali sono le procedure consigliate per un determinato processo. Il procedimento di estrazione funziona meglio su contenuti che presentano una struttura chiara con intestazioni gerarchiche.

> [!NOTE]
> L'estrazione per gli articoli di supporto è una nuova funzionalità, nelle sue prime fasi. Funziona al meglio con pagine semplici, ben strutturate e che non contengono intestazioni o piè di pagina complessi.

![QnA Maker supporta l'estrazione da pagine web semi-strutturate che presentano una struttura chiara con intestazioni gerarchiche.](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)