---
title: Configurare le impostazioni dello strumento di revisione di Content Moderator | Microsoft Docs
description: Configurare o ottenere team, tag, connettori, flussi di lavoro e credenziali.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372905"
---
# <a name="about-review-tool-settings"></a>Informazioni sulle impostazioni dello strumento di revisione #

Usando la scheda Impostazioni del dashboard dello strumento di revisione, è facile definire e modificare molti componenti.

![Impostazioni di revisione di Content Moderator](images/settings-1.png)

## <a name="team-and-subteams"></a>Team e sottoteam ## 

Da questa scheda è possibile gestire team e sottoteam. Si può avere un solo team, ma è possibile [creare più sottoteam](subteams.md) e inviare inviti ai futuri membri. Dopo aver inviato gli inviti, è possibile monitorarli, modificare le autorizzazioni per i membri del team e invitare altri utenti. Una volta che i membri del team hanno accettato l'invito, è possibile assegnarli a sottoteam diversi. È possibile assegnare ai membri del team il ruolo di amministratore o revisore: gli amministratori possono invitare altri utenti, mentre ai revisori non è consentito.

![Impostazioni team di Content Moderator](images/settings-2-team.png)

## <a name="tags"></a>Tag ##

Qui è possibile [definire tag personalizzati](tags.md) immettendo il codice breve, un nome e una descrizione per i tag. Dopo la creazione, i tag sono disponibili durante le revisioni. È possibile usare tag diversi per revisioni diverse, attivandone e disattivandone la visibilità.

![Impostazioni tag di Content Moderator](images/settings-3-tags.png)

## <a name="connectors"></a>Connettori ##

I flussi di lavoro aggiungono funzionalità usando connettori per comunicare con lo strumento di revisione. Lo strumento di revisione chiama le API di Content Moderator con il flusso di lavoro predefinito per la moderazione del contenuto. Quando ci si registra per lo strumento di revisione, viene automaticamente eseguito il provisioning delle credenziali per le API di Content Moderator. L'integrazione di altre API connettore è supportata, purché sia disponibile un connettore. Sono disponibili alcuni connettori predefiniti.

La [scheda Connettori](connectors.md) consente di gestire i connettori. È possibile aggiungere o eliminare connettori e individuare la chiave di sottoscrizione per un particolare connettore. Fare clic su Connetti per aggiungere i connettori ai flussi di lavoro personalizzati. 

![Impostazioni per i connettori di Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Flussi di lavoro ##

Gestire i flussi di lavoro dalla scheda Flussi di lavoro. È possibile testare i flussi di lavoro caricando un file di esempio. È anche possibile [definire flussi di lavoro personalizzati](workflows.md) per immagini e testo usando i connettori API disponibili nella scheda Connettori. 

![Impostazioni dei flussi di lavoro di Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenziali ##

Questa scheda consente di accedere rapidamente alla chiave di sottoscrizione di Content Moderator, necessaria per usare le API incluse con Content Moderator (Moderazione immagini, Moderazione testo, Gestione elenchi, Flusso di lavoro e Revisione).
 
![Credenziali di Content Moderator](images/settings-6-credentials.png)
