---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149952"
---
I record Sender Policy Framework (SPF) vengono usati per specificare i server di posta elettronica che possono inviare messaggi per conto di un nome di dominio. La configurazione corretta dei record SPF è importante per impedire ai destinatari di contrassegnare i messaggi di posta elettronica come posta indesiderata.

I documenti RFC di DNS in origine hanno introdotto un nuovo tipo di record SPF per supportare questo scenario. Per supportare server dei nomi meno recenti, hanno anche consentito l'uso del tipo di record TXT per specificare record SPF. Questa ambiguità ha prodotto una certa confusione, che è stata risolta dal documento [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Il documento dichiara che i record SPF devono essere creati con il tipo di record TXT. Stabilisce inoltre che il tipo di record SPF è deprecato.

**I record SPF sono supportati da DNS di Azure e devono essere creati con il tipo di record TXT**. Il tipo di record SPF obsoleto non è supportato. Quando si [importa un file di zona DNS](../articles/dns/dns-import-export.md), qualsiasi record SPF che usa il tipo di record SPF viene convertito in tipo di record TXT.
