---
title: Montare la condivisione file di Azure in una macchina virtuale aggiunta AD DS
description: Informazioni su come montare una condivisione file nei computer Active Directory Domain Services aggiunti in locale.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 40d372eb5569f3a4079acda3ab1e43b3e86cc113
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999599"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>Parte 4: montare una condivisione file da una macchina virtuale aggiunta a un dominio

Prima di iniziare questo articolo, assicurarsi di completare l'articolo precedente, [configurare le autorizzazioni a livello di directory e di file su SMB](storage-files-identity-ad-ds-configure-permissions.md).

Il processo descritto in questo articolo Verifica che le autorizzazioni di accesso e condivisione file siano configurate correttamente e che sia possibile accedere a una condivisione file di Azure da una macchina virtuale aggiunta a un dominio. L'assegnazione del ruolo RBAC a livello di condivisione può richiedere del tempo. 

Accedere al client usando le credenziali a cui sono state concesse le autorizzazioni, come illustrato nella figura seguente.

![Screenshot che mostra la schermata di accesso ad Azure AD per l'autenticazione utente](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Montaggio dei prerequisiti

Prima di poter montare la condivisione file, assicurarsi di aver superato i prerequisiti seguenti:

- Se si sta montando la condivisione file da un client che ha montato in precedenza la condivisione file con la chiave dell'account di archiviazione, verificare di aver disconnesso la condivisione, rimosso le credenziali permanenti della chiave dell'account di archiviazione e che attualmente usi le credenziali di servizi di dominio Active Directory per l'autenticazione.
- Il client deve avere una linea di visibilità per i servizi di dominio Active Directory. Se il computer o la macchina virtuale è fuori rete gestita da servizi di dominio Active Directory, sarà necessario abilitare la VPN per raggiungere servizi di dominio Active Directory per l'autenticazione.

Sostituire i valori segnaposto con i propri valori, quindi usare il comando seguente per montare la condivisione file di Azure:

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se si verificano problemi di montaggio con le credenziali di Active Directory Domain Services, vedere [non è possibile montare file di Azure con le credenziali di ad per le](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) linee guida.

Se il montaggio della condivisione file è stato completato correttamente, è stata abilitata e configurata l'autenticazione di servizi di dominio Active Directory locale per le condivisioni file di Azure.

## <a name="next-steps"></a>Passaggi successivi

Se l'identità creata in servizi di dominio Active Directory per rappresentare l'account di archiviazione si trova in un dominio o in un'unità organizzativa che impone la rotazione della password, passare all'articolo successivo per istruzioni sull'aggiornamento della password:

[Aggiornare la password dell'identità dell'account di archiviazione in servizi di dominio Active Directory](storage-files-identity-ad-ds-update-password.md)
