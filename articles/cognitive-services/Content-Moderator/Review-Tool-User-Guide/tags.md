---
title: Usare tag personalizzati per la moderazione dei contenuti - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator include tag predefiniti ed è possibile creare tag personalizzati per moderare contenuto specifico dell'azienda.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: c1a547f99995d25d19dafb03276306c50a544c9a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264717"
---
# <a name="create-and-use-moderation-tags"></a>Creare e usare i tag di moderazione

Oltre ai due tag predefiniti, **isadult** (**a**) e **isracy** (**r**), è possibile creare tag personalizzati per un'analisi più mirata. I tag personalizzati potranno quindi essere assegnati a immagini o testo da revisori umani.

## <a name="create-tags"></a>Creare tag

1.  Selezionare Tag nella scheda Impostazioni.

  ![Tag in Content Moderator](images/tags-1.png)

2.  Immettere un codice breve di due lettere per il tag.
3.  Immettere un nome per il tag. Usare un nome descrittivo e mantenerlo breve, Ad esempio, **isbullying**.
4.  Immettere una descrizione.
5.  Fare clic su Aggiungi.
6.  Al termine della creazione del tag, fare clic su Salva.

![Definizione di tag di moderazione del contenuto](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Usare tag personalizzati

I tag personalizzati vengono usati durante la revisione umana. I tag vengono visualizzati nell'anteprima e i revisori possono selezionarli con un clic.

![Uso di tag di moderazione del contenuto](images/tags-3-use.png)

È possibile disattivare tag diversi per revisioni diverse selezionando o deselezionando Visibile.
 
![Disabilitazione di tag di moderazione del contenuto](images/tags-4-disable.png)

Non è possibile eliminare i due tag predefiniti, **isadult** e **isracy**, ma è possibile eliminare i tag personalizzati definiti dall'utente. Fare clic sul Cestino accanto al tag da eliminare.

![Eliminazione di tag di moderazione del contenuto](images/tags-5-delete.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare i tag per la moderazione delle immagini, vedere [Rivedere le immagini moderate](Review-Moderated-Images.md).
