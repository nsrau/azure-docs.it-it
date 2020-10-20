---
author: baanders
description: Panoramica del file di inclusione per i passaggi nell'installazione dei dispositivi gemelli di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205490"
---
>[!NOTE]
>Queste operazioni devono essere completate da un utente con la possibilità di gestire sia le risorse che l'accesso degli utenti nella sottoscrizione di Azure. Sebbene alcuni passaggi possano essere completati con autorizzazioni inferiori, sarà necessario collaborare con tali autorizzazioni per configurare completamente un'istanza utilizzabile. Per altre informazioni, vedere la sezione [*prerequisiti: autorizzazioni necessarie*](#prerequisites-permission-requirements) di seguito.

La configurazione completa per una nuova istanza di Azure Digital Twins è costituita da due parti:
1. **Creazione dell'istanza**
2. **Impostazione delle autorizzazioni di accesso utente**: gli utenti di Azure devono avere il ruolo *proprietario (anteprima) di Azure Digital gemelli* nell'istanza di Azure Digital Twins per poterlo gestire e i relativi dati. In questo passaggio, come proprietario/amministratore della sottoscrizione di Azure, questo ruolo verrà assegnato alla persona che gestirà l'istanza di Azure Digital gemelli. Potrebbe trattarsi di un utente o di un altro utente nell'organizzazione.