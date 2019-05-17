---
title: Creare un pool di host di anteprima di Desktop virtuale Windows con un modello di Azure Resource Manager - Azure
description: Come creare un pool di host in anteprima di Desktop virtuale Windows con un modello di Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 4df8b854a94374346eec6f77d761e65a04ef78a2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523972"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Creare un pool di host con un modello di Azure Resource Manager

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant dell'anteprima di Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

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

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

Per assegnare gli utenti al gruppo di applicazioni desktop, aprire una finestra di PowerShell ed eseguire questo cmdlet per accedere all'ambiente di Desktop virtuale Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Successivamente, aggiungere utenti al gruppo di applicazioni desktop con questo cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Il nome dell'entità utente deve corrispondere all'identità dell'utente in Azure Active Directory, ad esempio user1@contoso.com. Se si vogliono aggiungere più utenti, è necessario eseguire questo cmdlet per ognuno.

Dopo aver completato questi passaggi, gli utenti aggiunti al gruppo di applicazioni desktop possono accedere a Desktop virtuale Windows con i client di Desktop remoto supportati e vedere una risorsa per un desktop di sessione.

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).