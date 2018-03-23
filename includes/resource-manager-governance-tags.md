---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/13/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 69c67f437d2f0b7bd6c1f5311eb5ba1d962d889a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
I tag vengono applicati alle risorse di Azure per organizzarle in modo logico in categorie. Ogni tag è costituito da un nome e un valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

Dopo aver applicato i tag, è possibile recuperare tutte le risorse nella sottoscrizione che hanno un nome e un valore di tag corrispondenti. I tag permettono di recuperare risorse correlate da gruppi di risorse diversi. Questo approccio può risultare utile per l'organizzazione delle risorse per la fatturazione o la gestione.

Ai tag si applicano le limitazioni seguenti:

* Ogni risorsa o gruppo di risorse può avere un massimo di 15 coppie nome-valore di tag. Questa limitazione si applica solo ai tag applicati direttamente al gruppo di risorse o alla risorsa. Un gruppo di risorse può contenere più risorse ognuna con 15 coppie nome-valore di tag. Se si devono associare più di 15 valori a una risorsa, usare una stringa JSON come valore di tag. La stringa JSON può contenere diversi valori applicati a un singolo nome di tag. Questo articolo illustra un esempio di assegnazione di una stringa JSON al tag.
* Il nome del tag è limitato a 512 caratteri e il valore del tag è limitato a 256 caratteri. Per gli account di archiviazione, il nome del tag è limitato a 128 caratteri e il valore a 256 caratteri.
* I tag applicati al gruppo di risorse non vengono ereditati dalle risorse in tale gruppo di risorse.
* Non è possibile applicare tag alle risorse classiche, ad esempio Servizi cloud.
* I nomi dei tag non possono contenere i caratteri seguenti: `<`, `>`, `%`, `&`, `\`, `?`, `/`
