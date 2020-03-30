---
title: Host Desktop virtuale di Windows pool di Azure Resource Manager - Azure
description: Come creare un pool host in Desktop virtuale di Windows con un modello di Azure Resource Manager.How to create a host pool in Windows Virtual Desktop with an Azure Resource Manager template.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292334"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Creare un pool di host con un modello di Azure Resource Manager

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti tenant di Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

Seguire le istruzioni di questa sezione per creare un pool host per un tenant di Windows Virtual Desktop con un modello di Azure Resource Manager fornito da Microsoft.Follow this section's instructions to create a host pool for a Windows Virtual Desktop tenant with an Azure Resource Manager template provided by Microsoft. Questo articolo illustra come creare un pool host in Windows Virtual Desktop, creare un gruppo di risorse con macchine virtuali in una sottoscrizione di Azure, aggiungere tali macchine virtuali al dominio di Active Directory e registrare le macchine virtuali con Desktop virtuale di Windows.This article will tell you how to create a host pool in Windows Virtual Desktop, create a resource group with VMs in an Azure subscription, join those VMs to the AD domain, and register the VMs with Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Requisiti per eseguire il modello di Azure Resource Manager

Prima di eseguire il modello di Azure Resource Manager, assicurarsi di conoscere le operazioni seguenti:Make sure you know the following things before running the Azure Resource Manager template:

- Dove si trova l'origine dell'immagine che si desidera utilizzare. È da Raccolta di Azure o è personalizzato?
- Credenziali di aggiunta al dominio.
- Credenziali di Desktop virtuale di Windows.

Quando si crea un pool host Di Windows Virtual Desktop con il modello azure Resource Manager, è possibile creare una macchina virtuale dalla raccolta di Azure, un'immagine gestita o un'immagine non gestita. Per altre informazioni su come creare immagini di macchine virtuali, vedere Preparare un disco rigido virtuale di Windows o un disco rigido virtuale per il caricamento in Azure e [Creare un'immagine gestita di una macchina virtuale generalizzata in Azure.To](../virtual-machines/windows/capture-image-resource.md)learn more about how to create VM images, see [Prepare a Windows VHD or VHDX to upload to Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) and Create a managed image of a generalized VM in Azure.

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Eseguire il modello di Azure Resource Manager per il provisioning di un nuovo pool hostRun the Azure Resource Manager template for provisioning a new host pool

Per iniziare, vai a [questo URL GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Distribuire il modello in Azure

Se si esegue la distribuzione in una sottoscrizione Enterprise, scorrere verso il basso e selezionare **Distribuisci**in Azure , quindi passare in avanti compilare i parametri in base all'origine dell'immagine.

Se si esegue la distribuzione in una sottoscrizione di Cloud Solution Provider, seguire questi passaggi per eseguire la distribuzione in Azure:If you're deploying in a Cloud Solution Provider subscription, follow these steps to deploy to Azure:

1. Scorrere in basso e fare clic con il pulsante destro del mouse su **Distribuisci in Azure**, quindi scegliere **Copia collegamento**.
2. Aprire un editor di testo, come il Blocco note, e incollarvi il collegamento.
3. Subito dopohttps://portal.azure.com/" " e prima dell'hashtag (-) inserisci un simbolo di chiocciola (-) seguito dal nome di dominio del tenant. Di seguito è riportato un esempio `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`del formato da utilizzare: .
4. Accedere al portale di Azure come utente con autorizzazioni di Amministratore/Collaboratore per la sottoscrizione di Cloud Solution Provider.
5. Incollare nella barra degli indirizzi il collegamento copiato nell'editor di testo.

Per indicazioni sui parametri da immettere per lo scenario, vedere il [file Leggimi](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)di Windows Virtual Desktop . Il file Leggimi viene sempre aggiornato con le ultime modifiche.

## <a name="assign-users-to-the-desktop-application-group"></a>Assegnare utenti al gruppo di applicazioni desktopAssign users to the desktop application group

Al termine del modello GitHub Azure Resource Manager, assegnare l'accesso utente prima di iniziare a testare i desktop della sessione completa nelle macchine virtuali.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto.

Per assegnare gli utenti al gruppo di applicazioni desktop, aprire una finestra di PowerShell ed eseguire questo cmdlet per accedere all'ambiente Desktop virtuale di Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Successivamente, aggiungere gli utenti al gruppo di applicazioni desktop con questo cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

L'UPN dell'utente deve corrispondere all'identità dell'utente user1@contoso.comin Azure Active Directory, ad esempio . Se si vogliono aggiungere più utenti, è necessario eseguire questo cmdlet per ognuno.

Dopo aver completato questi passaggi, gli utenti aggiunti al gruppo di applicazioni desktop possono accedere a Desktop virtuale Windows con i client di Desktop remoto supportati e vedere una risorsa per un desktop di sessione.

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](../security-center/security-center-just-in-time.md).