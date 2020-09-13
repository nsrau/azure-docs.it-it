---
title: Eseguire la migrazione manuale da desktop virtuale Windows (classico)-Azure
description: Come eseguire la migrazione manuale da desktop virtuale Windows (classico) a desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74527f57340f850b60dd00dcd054992c423a49c7
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90039047"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Eseguire la migrazione manuale da desktop virtuale di Windows (versione classica)

Desktop virtuale Windows (classico) crea l'ambiente del servizio con i cmdlet di PowerShell, le API REST e gli oggetti servizio. Un "oggetto" in un ambiente del servizio desktop virtuale Windows è un elemento creato da desktop virtuale di Windows. Gli oggetti servizio includono tenant, pool host, gruppi di applicazioni e host di sessione.

Tuttavia, desktop virtuale Windows (classico) non è integrato con Azure. Senza l'integrazione con Azure, tutti gli oggetti creati non vengono gestiti automaticamente dal portale di Azure perché non sono connessi alla sottoscrizione di Azure.

Il recente aggiornamento principale del desktop virtuale di Windows segna un cambiamento nel servizio rispetto all'integrazione completa di Azure. Gli oggetti creati nel desktop virtuale di Windows vengono gestiti automaticamente dal portale di Azure.

In questo articolo verrà illustrato il motivo per cui è consigliabile eseguire la migrazione alla versione più recente di desktop virtuale di Windows. Successivamente, verrà illustrato come eseguire manualmente la migrazione da desktop virtuale Windows (classico) all'ultimo aggiornamento del desktop virtuale di Windows.

## <a name="why-migrate"></a>Perché eseguire la migrazione?

Gli aggiornamenti principali possono risultare inconvenienti, in particolare quelli che è necessario eseguire manualmente. Tuttavia, esistono alcuni motivi per cui non è possibile eseguire automaticamente la migrazione:

- Gli oggetti del servizio esistenti eseguiti con la versione classica non hanno alcuna rappresentazione in Azure. Il loro ambito non si estende oltre il servizio desktop virtuale di Windows.
- Con l'aggiornamento più recente, l'ID dell'applicazione del servizio è stato modificato per rimuovere il consenso per le app come per desktop virtuale Windows (classico). Non sarà possibile creare nuovi oggetti di Azure con desktop virtuale di Windows a meno che non siano autenticati con il nuovo ID applicazione.

Nonostante il problema, la migrazione dalla versione classica è ancora importante. Di seguito sono riportate le operazioni che è possibile eseguire dopo la migrazione:

- Gestire il desktop virtuale di Windows tramite il portale di Azure.
- Assegnare i gruppi di utenti Azure Active Directory (AD) ai gruppi di applicazioni.
- Utilizzare la funzionalità Log Analytics migliorata per risolvere i problemi relativi alla distribuzione.
- Usare i controlli degli accessi in base al ruolo nativi di Azure per gestire l'accesso amministrativo.

## <a name="when-should-i-migrate"></a>Quando è necessario eseguire la migrazione?

Quando si chiede se è necessario eseguire la migrazione, è necessario tenere conto anche della situazione attuale e futura della distribuzione.

In particolare, esistono alcuni scenari in cui è consigliabile eseguire manualmente la migrazione:

- Si dispone di una configurazione del pool host di test con un numero ridotto di utenti.
- Si dispone di una configurazione del pool host di produzione con un numero ridotto di utenti, ma si prevede di passare fino a centinaia di utenti.
- Si dispone di una semplice configurazione che può essere facilmente replicata. Ad esempio, se le VM usano un'immagine della raccolta.

> [!IMPORTANT]
> Se si usa una configurazione avanzata che richiede molto tempo per stabilizzarsi o che ha un numero elevato di utenti, non è consigliabile eseguire la migrazione manuale.

## <a name="prepare-for-migration"></a>Preparare la migrazione

Prima di iniziare, è necessario assicurarsi che l'ambiente sia pronto per la migrazione.

Ecco gli elementi necessari per avviare il processo di migrazione:

- Una sottoscrizione di Azure in cui verranno creati nuovi oggetti servizio di Azure.
- Assicurarsi di essere assegnati ai ruoli seguenti:
    
    - Collaboratore
    - Amministratore accessi utente
    
    Il ruolo Collaboratore consente di creare oggetti di Azure nella sottoscrizione e il ruolo amministratore accesso utenti consente di assegnare gli utenti ai gruppi di applicazioni.

## <a name="how-to-migrate-manually"></a>Come eseguire la migrazione manuale

A questo punto, dopo aver preparato il processo di migrazione, è possibile eseguire effettivamente la migrazione.

Per eseguire la migrazione manuale da desktop virtuale Windows (classico) a desktop virtuale Windows:

1. Seguire le istruzioni riportate in [creare un pool di host con il portale di Azure](create-host-pools-azure-marketplace.md) per creare tutti gli oggetti di alto livello con la portale di Azure.
2. Se si vuole importare le macchine virtuali già in uso, seguire le istruzioni in [registrare le macchine virtuali nel pool di host di desktop virtuali Windows](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) per registrarle manualmente nel nuovo pool di host creato nel passaggio 1.
3. Creare nuovi gruppi di app RemoteApp.
4. Pubblicare utenti o gruppi di utenti nei nuovi gruppi di app desktop e RemoteApp.
5. Aggiornare i criteri di accesso condizionale per consentire i nuovi oggetti seguendo le istruzioni riportate in [configurare la funzionalità di autenticazione](set-up-mfa.md)a più fattori.

Per evitare tempi di inattività, è necessario innanzitutto registrare gli host di sessione esistenti nei pool di host Azure Resource Manager integrati in piccoli gruppi alla volta. Successivamente, è possibile riportare gli utenti nei nuovi gruppi di app integrati Azure Resource Manager.

## <a name="next-steps"></a>Passaggi successivi

Una volta eseguita la migrazione, è possibile ottenere informazioni sul funzionamento del desktop virtuale di Windows consultando [le esercitazioni](create-host-pools-azure-marketplace.md). Per informazioni sulle funzionalità di gestione avanzate, [espandere un pool di host esistente](expand-existing-host-pool.md) e [personalizzare le proprietà RDP](customize-rdp-properties.md).

Per ulteriori informazioni sugli oggetti servizio, vedere [ambiente desktop virtuale di Windows](environment-setup.md).
