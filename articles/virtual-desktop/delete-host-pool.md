---
title: Elimina pool di host per desktop virtuali Windows-Azure
description: Come eliminare un pool host in un desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88007591"
---
# <a name="delete-a-host-pool"></a>Eliminare un pool di host

Tutti i pool host creati nel desktop virtuale di Windows sono allegati a host sessione e gruppi di app. Per eliminare un pool di host, è necessario eliminare i gruppi di app e gli host di sessione associati. L'eliminazione di un gruppo di app è piuttosto semplice, ma l'eliminazione di un host di sessione è più complessa. Quando si elimina un host sessione, è necessario assicurarsi che non abbia sessioni utente attive. Tutte le sessioni utente nell'host della sessione devono essere disconnesse per evitare che gli utenti perdano i dati.

## <a name="delete-a-host-pool-with-powershell"></a>Eliminare un pool di host con PowerShell

Per eliminare un pool di host tramite PowerShell, è prima necessario eliminare tutti i gruppi di app nel pool di host. Per eliminare tutti i gruppi di app, eseguire il cmdlet PowerShell seguente:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Eseguire quindi questo cmdlet per eliminare il pool host:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

Questo cmdlet rimuove tutte le sessioni utente esistenti nell'host sessione del pool host. Annulla inoltre la registrazione dell'host sessione dal pool host. Tutte le macchine virtuali (VM) correlate continueranno a esistere nella sottoscrizione.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Eliminare un pool host con il portale di Azure

Per eliminare un pool host nell'portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Cercare e selezionare **Desktop virtuale Windows**.

3. Selezionare **pool host** nel menu a sinistra della pagina, quindi selezionare il nome del pool host che si desidera eliminare.

4. Nel menu sul lato sinistro della pagina selezionare **gruppi di applicazioni**.

5. Selezionare tutti i gruppi di applicazioni nel pool host che si intende eliminare e quindi selezionare **Rimuovi**.

6. Dopo aver rimosso i gruppi di app, passare al menu sul lato sinistro della pagina e selezionare **Panoramica**.

7. Selezionare **Rimuovi**.

8. Se nel pool host che si sta eliminando sono presenti host di sessione, verrà visualizzato un messaggio che richiede l'autorizzazione a continuare. Selezionare **Sì**.

9. Il portale di Azure eliminerà tutti gli host della sessione ed eliminerà il pool host. Le macchine virtuali correlate all'host sessione non verranno eliminate e rimarranno nella sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare un pool di host, vedere questi articoli:

- [Creare un pool di host con il portale di Azure](create-host-pools-azure-marketplace.md)
- [Creare un pool di host con PowerShell](create-host-pools-powershell.md)

Per informazioni su come configurare le impostazioni del pool host, vedere questi articoli:

- [Personalizzare le proprietà Remote Desktop Protocol per un pool host](customize-rdp-properties.md)
- [Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows](configure-host-pool-load-balancing.md)
- [Configurare il tipo di assegnazione del pool di host di desktop personale](configure-host-pool-personal-desktop-assignment-type.md)