---
title: Informazioni sulla collaborazione dell'app LUIS - Azure | Microsoft Docs
description: Le app LUIS richiedono un singolo proprietario e collaboratori facoltativi.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 684507b58e5ac766e9b42d3aa53cb0867a131a93
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222673"
---
# <a name="collaborating"></a>Collaborazione

LUIS consente la collaborazione per consentire a un gruppo di utenti di creare un'app.

## <a name="luis-account"></a>Account LUIS
Un account LUIS è associato a un singolo account [Microsoft Live](https://login.live.com/). A ogni account LUIS viene assegnata una [chiave di creazione](luis-concept-keys.md#authoring-key) da usare per creare tutte le app LUIS a cui l'account ha accesso. 

Un account LUIS può disporre di più app LUIS.

Per altre informazioni sugli account utente Active Directory vedere [Tenant di Active Directory di Azure](luis-how-to-account-settings.md#azure-active-directory-tenant-user). 

## <a name="luis-app-owner"></a>Proprietario dell'app LUIS
L'account che crea un'app è il proprietario. Ogni app ha un singolo proprietario. Il proprietario è elencato nelle **[impostazioni](luis-how-to-collaborate.md)** dell'app. È l'account che può eliminare l'app. È anche l'account che riceve la posta elettronica quando la quota dell'endpoint raggiunge il 75% del limite mensile. 

## <a name="transfer-ownership"></a>Trasferire la proprietà
LUIS non consente il trasferimento della proprietà, tuttavia qualsiasi collaboratore può esportare l'app e crearne un'altra importandola. Ricordare che la nuova app dispone di un ID app diverso. La nuova app deve essere sottoposta a training e pubblicata e deve essere utilizzato il nuovo endpoint.

## <a name="luis-app-collaborators"></a>Collaboratori dell'app LUIS
Il proprietario di un'app può aggiungere collaboratori alla stessa. Il proprietario deve aggiungere l'indirizzo di posta elettronica del collaboratore nelle **[impostazioni](luis-how-to-collaborate.md)** dell'app. Il collaboratore dispone dell'accesso completo all'app. Se il collaboratore elimina l'app, viene rimossa dall'account del collaboratore, ma rimane nell'account del proprietario. 

Se si desidera condividere più app con i collaboratori, a ogni app è necessario aggiungere l'indirizzo di posta elettronica del collaboratore. 

## <a name="managing-multiple-authors"></a>Gestione di più autori
Al momento il sito Web [LUIS](luis-reference-regions.md#luis-website) non offre la creazione a livello di transazione. È possibile consentire agli autori di lavorare su versioni indipendenti da una versione base. Le sezioni seguenti descrivono due diversi metodi.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gestione di più versioni all'interno della stessa app
Eseguire innanzitutto la [clonazione](luis-how-to-manage-versions.md#clone-a-version) da una versione base per ogni autore. 

Ogni autore apporta modifiche alla propria versione dell'app. Dopo che ogni autore è soddisfatto del proprio modello, esportare le nuove versioni in file JSON.  

Le app esportate sono file in formato JSON confrontabili a livello di modifiche. Combinare i file per creare un singolo file JSON della nuova versione. Modificare la proprietà **versionId** nel file JSON affinché indichi la nuova versione unita. Importare la versione nell'app originale. 

Questo metodo consente di disporre di una versione attiva, di una versione di staging e di una versione pubblicata. È possibile confrontare i risultati nel riquadro di test interattivo tra le tre versioni.

### <a name="manage-multiple-versions-as-apps"></a>Gestione di più versioni come app
[Esportare](luis-how-to-manage-versions.md#export-version) la versione base. Ogni autore importa la versione. L'utente che importa l'app è il proprietario della versione. Dopo aver modificato l'app, esportare la versione. 

Le app esportate sono file in formato JSON confrontabili con l'esportazione base a livello di modifiche. Combinare i file per creare un singolo file JSON della nuova versione. Modificare la proprietà **versionId** nel file JSON affinché indichi la nuova versione unita. Importare la versione nell'app originale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti di [controllo delle versioni](luis-concept-version.md). 

Vedere [Impostazioni dell'app](luis-how-to-collaborate.md) per informazioni sulla gestione dei collaboratori nell'app LUIS.

Vedere [Aggiungere l'indirizzo di posta elettronica all'elenco degli accessi](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) alle API di creazione.