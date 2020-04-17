---
title: Creazione in blocco di utenti nel portale di Azure Active Directory Documenti Microsoft
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure AD in Azure Active DirectoryAdd users in bulk in the Azure AD admin center in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a8b9cb9701288d24534ab08940f6dbd4a698ad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532935"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Creare utenti in blocco in Azure Active Directory

Azure Active Directory (Azure AD) supporta le operazioni di creazione ed eliminazione di utenti in blocco, l'invito in blocco per gli ospiti e supporta il download di elenchi di utenti, gruppi e membri del gruppo.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per creare utenti nel portale di amministrazione in blocco, è necessario accedere come amministratore globale o amministratore utente.

## <a name="to-create-users-in-bulk"></a>Per creare utenti in blocco

1. [Accedere all'organizzazione](https://aad.portal.azure.com) di Azure AD con un account amministratore utente nell'organizzazione.
1. In Azure AD selezionare Creazione**in blocco** **utenti** > .
1. Nella pagina **Crea utente** in blocco selezionare **Scarica** per ricevere un file di proprietà utente con valori delimitati da virgole (CSV) validi e quindi aggiungere gli utenti aggiuntivi che si desidera creare.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera aggiungere](./media/users-bulk-add/upload-button.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si desidera creare. Gli unici valori obbligatori sono **Nome**, **Nome entità utente**, Password **iniziale** e **Blocca accesso (Sì/No)**. Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli URL degli utenti da creare](./media/users-bulk-add/add-csv-file.png)

1. Nella pagina Crea utente in **blocco,** in Carica il file CSV, individuare il file. Quando si seleziona il file e si fa clic su **Invia**, viene avviata la convalida del file CSV.
1. Dopo aver convalidato il contenuto del file, verrà visualizzato **File caricato correttamente**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure che importa i nuovi utenti.
1. Al termine dell'operazione di importazione, verrà visualizzata una notifica dello stato del processo dell'operazione in blocco.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **Risultati operazione in blocco.** Il file contiene il motivo di ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **Risultati dell'operazione** in blocco.

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

Successivamente, è possibile verificare che gli utenti creati esistano nell'organizzazione di Azure AD nel portale di Azure o tramite PowerShell.Next, you can check to see that the users you created exist in the Azure AD organization either in the Azure portal or by using PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verificare gli utenti nel portale di AzureVerify users in the Azure portal

1. [Accedere all'interfaccia](https://aad.portal.azure.com) di amministrazione di Azure AD con un account amministratore utente nell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare **Tutti gli utenti** e verificare che gli utenti creati siano elencati.

### <a name="verify-users-with-powershell"></a>Verificare gli utenti con PowerShellVerify users with PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Si dovrebbe vedere che gli utenti che hai creato sono elencati.

## <a name="bulk-import-service-limits"></a>Limiti del servizio di importazione in blocco

Ogni attività in blocco per creare utenti può essere eseguita per un massimo di un'ora. Ciò consente la creazione in blocco di almeno 50.000 utenti.

## <a name="next-steps"></a>Passaggi successivi

- [Eliminare in blocco gli utenti](users-bulk-delete.md)
- [Scarica l'elenco degli utenti](users-bulk-download.md)
- [Utenti di ripristino in blocco](users-bulk-restore.md)
