---
title: Creare un pool di host di anteprima di Desktop virtuale Windows con un modello di Azure Resource Manager - Azure
description: Come creare un pool di host in anteprima di Desktop virtuale Windows con un modello di Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 262ec35a8a177652dff12bccb3b5435cb5856d81
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401424"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Creare un pool di host con un modello di Azure Resource Manager

I pool di host sono una raccolta di uno o più macchine virtuali identiche all'interno di ambienti di tenant di anteprima di Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app che gli utenti possono interagire con come in un desktop fisico.

Seguire le istruzioni della sezione per creare un pool di host per un tenant di Desktop virtuale Windows con un modello di Azure Resource Manager fornito da Microsoft. Questo articolo illustrerà come creare un pool di host di Desktop virtuale Windows, creare un gruppo di risorse con le macchine virtuali in una sottoscrizione di Azure, aggiungere tali macchine virtuali al dominio di Active Directory e registrare le macchine virtuali con Windows Desktop virtuale.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Cosa occorre per eseguire il modello di Azure Resource Manager

Assicurarsi di conoscere le operazioni seguenti prima di eseguire il modello di Azure Resource Manager:

- Dove è l'origine dell'immagine da usare. Si tratta dalla raccolta di Azure o si tratta di personalizzato?
- Le credenziali di aggiunta al dominio.
- Le credenziali di Desktop virtuale Windows.

Quando si crea un pool di host di Desktop virtuale Windows con il modello di Azure Resource Manager, è possibile creare una macchina virtuale dalla raccolta di Azure, un'immagine gestita o un'immagine non gestita. Per altre informazioni su come creare immagini di macchine Virtuali, vedere [preparare un disco rigido virtuale Windows o vhdx prima del caricamento in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) e [creare un'immagine gestita di una macchina virtuale generalizzata in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Eseguire il modello di Azure Resource Manager per il provisioning di un nuovo pool di host

Per iniziare, passare a [questo URL di GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Distribuire il modello in Azure

Se si distribuisce in una sottoscrizione Enterprise, scorrere verso il basso e selezionare **Distribuisci in Azure**, quindi ignorare, compilare ahead i parametri in base l'origine dell'immagine.

Se si distribuisce in una sottoscrizione di Cloud Solution Provider, seguire questi passaggi per distribuire in Azure:

1. Scorrere verso il basso e fare doppio clic su **Deploy to Azure**, quindi selezionare **Copia collegamento percorso**.
2. Aprire un editor di testo come blocco note e incollare il collegamento non esiste.
3. Subito dopo "https://portal.azure.com/" e prima l'hashtag (#) immettere un simbolo di chiocciola (@) seguiti dal nome di dominio del tenant. Ecco un esempio del formato è necessario utilizzare: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Accedere al portale di Azure come utente con autorizzazioni di amministratore o collaboratore nella sottoscrizione di Cloud Solution Provider.
5. Incollare il collegamento copiato nell'editor di testo nella barra degli indirizzi.

Per indicazioni sui quali parametri è necessario immettere per il proprio scenario, vedere il Desktop virtuale Windows [file Leggimi](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). Il file Leggimi viene sempre aggiornato con le modifiche più recenti.

## <a name="assign-users-to-the-desktop-application-group"></a>Assegnare gli utenti al gruppo di applicazioni desktop

Dopo aver completato il modello di GitHub Azure Resource Manager, assegnare loro l'accesso prima di avviare il desktop di sessione completa nelle macchine virtuali di test.

Prima di tutto [scaricare e importare il modulo Windows PowerShell di Desktop virtuale](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell se non è già presente.

Per assegnare gli utenti al gruppo di applicazioni desktop, aprire una finestra di PowerShell ed eseguire questo cmdlet per accedere all'ambiente di Desktop virtuale Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Successivamente, impostare il contesto per il gruppo di tenant specificato nel modello di Azure Resource Manager con questo cmdlet:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

Successivamente, aggiungere utenti al gruppo di applicazioni desktop con questo cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

L'UPN dell'utente deve corrispondere all'identità dell'utente in Azure Active Directory (ad esempio, user1@contoso.com). Se si desidera aggiungere più utenti, è necessario eseguire questo cmdlet per ogni utente.

Dopo aver completato questi passaggi, gli utenti aggiunti al gruppo di applicazioni desktop possono accedere al Desktop virtuale Windows con client di Desktop remoto supportati e visualizzare una risorsa per un desktop di sessione.
