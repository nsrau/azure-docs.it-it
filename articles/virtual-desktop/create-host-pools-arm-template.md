---
title: Creare un pool di host di anteprima del desktop virtuale Windows con un modello di Azure Resource Manager-Azure
description: Come creare un pool host nell'anteprima di desktop virtuale di Windows con un modello di Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 27fe19c2b1f92b67e02b4e09d5fcd607759d8abd
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163749"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Creare un pool di host con un modello di Azure Resource Manager

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant dell'anteprima di Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

Seguire le istruzioni di questa sezione per creare un pool di host per un tenant di desktop virtuale Windows con un modello di Azure Resource Manager fornito da Microsoft. Questo articolo illustra come creare un pool host in un desktop virtuale di Windows, creare un gruppo di risorse con macchine virtuali in una sottoscrizione di Azure, aggiungere le VM al dominio di Active Directory e registrare le VM con desktop virtuale di Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Requisiti per eseguire il modello di Azure Resource Manager

Prima di eseguire il modello di Azure Resource Manager, assicurarsi di avere a conoscenza gli elementi seguenti:

- Dove l'origine dell'immagine che si vuole usare è. Si tratta di una raccolta di Azure o è personalizzata?
- Credenziali di aggiunta al dominio.
- Credenziali del desktop virtuale di Windows.

Quando si crea un pool di host per desktop virtuali Windows con il modello di Azure Resource Manager, è possibile creare una macchina virtuale dalla raccolta di Azure, da un'immagine gestita o da un'immagine non gestita. Per altre informazioni su come creare immagini di VM, vedere [preparare un disco rigido virtuale Windows o VHDX per il caricamento in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) e [creare un'immagine gestita di una macchina virtuale generalizzata in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Eseguire il modello di Azure Resource Manager per il provisioning di un nuovo pool di host

Per iniziare, passare a [questo URL di GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Distribuire il modello in Azure

Se si esegue la distribuzione in una sottoscrizione Enterprise, scorrere verso il basso e selezionare Distribuisci in Azure, quindi passare **a**compilare i parametri in base all'origine dell'immagine.

Se si esegue la distribuzione in una sottoscrizione Cloud Solution Provider, attenersi alla procedura seguente per eseguire la distribuzione in Azure:

1. Scorrere in basso e fare clic con il pulsante destro del mouse su **Distribuisci in Azure**, quindi scegliere **Copia collegamento**.
2. Aprire un editor di testo, come il Blocco note, e incollarvi il collegamento.
3. Subito dopo "https://portal.azure.com/" e prima dell'hashtag (#) immettere un simbolo di chiocciola (@) seguito dal nome di dominio del tenant. Di seguito è riportato un esempio del formato da usare: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Accedere al portale di Azure come utente con autorizzazioni di Amministratore/Collaboratore per la sottoscrizione di Cloud Solution Provider.
5. Incollare nella barra degli indirizzi il collegamento copiato nell'editor di testo.

Per informazioni aggiuntive sui parametri da immettere per lo scenario, vedere il [file Leggimi](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)del desktop virtuale di Windows. Il file Leggimi viene sempre aggiornato con le modifiche più recenti.

## <a name="assign-users-to-the-desktop-application-group"></a>Assegnare gli utenti al gruppo di applicazioni desktop

Al termine del modello di Azure Resource Manager GitHub, assegnare l'accesso utente prima di iniziare a testare i desktop completi della sessione nelle macchine virtuali.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) da usare nella sessione di PowerShell, se non è già stato fatto.

Per assegnare gli utenti al gruppo di applicazioni desktop, aprire una finestra di PowerShell ed eseguire questo cmdlet per accedere all'ambiente desktop virtuale di Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Successivamente, aggiungere gli utenti al gruppo di applicazioni desktop con questo cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Il nome dell'entità utente deve corrispondere all'identità dell'utente in Azure Active Directory, ad esempio user1@contoso.com. Se si vogliono aggiungere più utenti, è necessario eseguire questo cmdlet per ognuno.

Dopo aver completato questi passaggi, gli utenti aggiunti al gruppo di applicazioni desktop possono accedere a Desktop virtuale Windows con i client di Desktop remoto supportati e vedere una risorsa per un desktop di sessione.

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).