---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 099bca7483100da1a4ee2f8f10057c416ad145b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841563"
---
I tag vengono applicati alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

Dopo aver applicato i tag, è possibile recuperare tutte le risorse nella sottoscrizione che hanno un nome e un valore di tag corrispondenti. I tag permettono di recuperare risorse correlate da gruppi di risorse diversi. Questo approccio può risultare utile per l'organizzazione delle risorse per la fatturazione o la gestione.

Per la tassonomia è consigliabile prendere in considerazione una strategia di assegnazione di tag self-service ai metadati, in aggiunta a una strategia di assegnazione di tag automatica per ridurre il carico di lavoro degli utenti e aumentare il livello di precisione.

Ai tag si applicano le limitazioni seguenti:

* Non tutti i tipi di risorse supportano i tag. Per determinare se è possibile applicare un tag a un tipo di risorsa, vedere [Supporto dei tag per le risorse di Azure](../articles/azure-resource-manager/tag-support.md).
* Ogni risorsa o gruppo di risorse può avere un massimo di 50 coppie nome/valore di tag. Attualmente, gli account di archiviazione supportano solo 15 tag consentiti, ma tale limite verrà generato fino a 50 in una versione futura. Se si desidera applicare ulteriori tag rispetto al numero massimo consentito, usare una stringa JSON per il valore del tag. La stringa JSON può contenere diversi valori applicati a un singolo nome di tag. Un gruppo di risorse può contenere più risorse ognuna con 50 coppie nome/valore di tag.
* Il nome del tag è limitato a 512 caratteri e il valore del tag è limitato a 256 caratteri. Per gli account di archiviazione, il nome del tag è limitato a 128 caratteri e il valore a 256 caratteri.
* Virtuali non generalizzate non supportano i tag.
* I tag applicati al gruppo di risorse non vengono ereditati dalle risorse in tale gruppo di risorse.
* Non è possibile applicare tag alle risorse classiche, ad esempio Servizi cloud.
* I nomi dei tag non possono contenere i caratteri seguenti: `<`, `>`, `%`, `&`, `\`, `?`, `/`
