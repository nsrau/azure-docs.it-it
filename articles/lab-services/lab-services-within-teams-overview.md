---
title: Azure Lab Services in Microsoft Teams
description: Viene fornita una panoramica dell'utilizzo di Azure Lab Services in Microsoft teams.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433928"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Azure Lab Services in Microsoft Teams

Azure Lab Services possono essere utilizzate all'interno di Microsoft teams tramite l'app **Azure Lab Services** teams. Qualsiasi proprietario del team con accesso proprietario/collaboratore/autore agli account Lab sarà in grado di creare Labs ed effettuare il provisioning di macchine virtuali a tutti gli utenti del team.

In questo articolo vengono illustrati i vantaggi derivanti dall'utilizzo di Azure Lab Services all'interno di team e vengono forniti collegamenti ad altri articoli per istruzioni su come creare e gestire i Lab all'interno dei team. 

> [!NOTE]
>**Azure Lab Services** L'app teams può essere aggiunta solo a un team e non può essere aggiunta a singole chat o chat di gruppo.

## <a name="benefits"></a>Vantaggi

Azure Lab Services integrazione con Microsoft teams consentirà agli educatori di configurare un ambiente di classe e fornire ambienti lab virtuali all'interno del team (classe): 

* Gli educatori possono configurare i Lab in modo che gli studenti possano accedere alle proprie macchine virtuali da team, senza uscire dai team e passare al [sito web Azure Lab Services](https://labs.azure.com).
* Single Sign-on (SSO) da team a Azure Lab Services.
* I proprietari del team e del Lab non devono mantenere i registri delle classi in due sistemi diversi: l'elenco degli utenti del Lab viene popolato automaticamente dall'appartenenza al team e una sincronizzazione viene eseguita ogni 24 ore automaticamente. 
* Dopo la pubblicazione iniziale della VM modello, la capacità del Lab (ovvero il numero di macchine virtuali nel Lab) viene regolata automaticamente in base all'aggiunta o all'eliminazione di utenti dall'appartenenza al team. 
* I proprietari del team e del Lab visualizzano solo i Lab correlati al team e gli studenti visualizzano solo le macchine virtuali di cui è stato effettuato il provisioning per il team specifico. 
* Gli utenti verranno registrati automaticamente nel Lab e le macchine virtuali verranno assegnate automaticamente al primo accesso dopo la pubblicazione del Lab. Gli educatori non devono inviare inviti e gli studenti non devono effettuare la registrazione separatamente per il Lab.  

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Introduzione e creazione di un Lab nei team](how-to-get-started-create-lab-within-teams.md)
- [Gestire gli elenchi degli utenti del Lab nei team](how-to-manage-user-lists-within-teams.md)
- [Gestisci il pool di macchine virtuali del Lab in team](how-to-manage-vm-pool-within-teams.md)
- [Creare e gestire pianificazioni Lab all'interno di Team](how-to-create-schedules-within-teams.md)
- [Accedere a una macchina virtuale all'interno di teams-visualizzazione studente](how-to-access-vm-for-students-within-teams.md)
