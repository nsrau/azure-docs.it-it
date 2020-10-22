---
title: Ripristinare in blocco gli utenti eliminati nel portale di Azure Active Directory | Microsoft Docs
description: Ripristinare in blocco gli utenti eliminati nell'interfaccia di amministrazione di Azure AD in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4031356c3a2ff51f6f3da8b53dd0dcc9fd5a426
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375340"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Ripristinare in blocco gli utenti eliminati in Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di ripristino in blocco degli utenti, oltre al download di elenchi di utenti, gruppi e membri di gruppi.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV per ripristinare correttamente gli utenti di Azure AD in blocco. Il modello CSV scaricato sarà simile a questo esempio:

![Foglio di calcolo per il caricamento con callout che illustrano lo scopo e i valori di ogni riga e colonna](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Struttura del modello CSV

Un modello CSV scaricato contiene le righe descritte di seguito:

- **Numero di versione**: nel file CSV di caricamento deve essere inclusa la prima riga contenente il numero di versione.
- **Intestazioni di colonna**: il formato delle intestazioni di colonna è &lt;*Nome elemento*&gt; [NomeProprietà] &lt;*Required o vuoto*&gt;. Ad esempio: `Object ID [objectId] Required`. Alcune versioni precedenti del modello potrebbero presentare lievi variazioni.
- **Riga di esempi**: nel modello è stata inclusa una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate. In caso contrario, il caricamento non può essere elaborato.
- Le colonne obbligatorie sono riportate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Eventuali colonne aggiuntive aggiunte saranno ignorate e non verranno elaborate.
- È consigliabile scaricare la versione più recente del modello CSV il più spesso possibile.

## <a name="to-bulk-restore-users"></a>Per ripristinare gli utenti in blocco

1. [Accedere all'organizzazione di Azure AD](https://aad.portal.azure.com) con un account di amministratore utenti nell'organizzazione.
1. In Azure AD selezionare **Utenti** > **Eliminati**.
1. Nella pagina **Utenti eliminati** selezionare **Ripristino in blocco** per caricare un file CSV valido di proprietà degli utenti da ripristinare.

    ![Selezionare il comando di Ripristino in blocco nella pagina Utenti eliminati](./media/users-bulk-restore/bulk-restore.png)

1. Aprire il modello CSV e aggiungere una riga per ogni utente da ripristinare. L'unico valore obbligatorio è **ObjectID**. Salvare quindi il file.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Selezionare un file CSV locale in cui elencare gli utenti da aggiungere":::

1. Nella pagina **Ripristino in blocco** individuare il file in **Caricare il file CSV**. Quando si seleziona il file e si fa clic su **Invia** viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure per il ripristino degli utenti.
1. Al termine dell'operazione di ripristino in blocco, verrà visualizzata una notifica per indicare che è stata completata correttamente.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **Risultati dell'operazione in blocco**. Il file contiene il motivo di ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste in sospeso di operazioni in blocco nella pagina **Risultati dell'operazione in blocco**.

[![Verificare lo stato nella pagina Risultati operazioni bulk.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

Quindi, è possibile verificare che gli utenti ripristinati siano presenti nell'organizzazione di Azure AD nel portale di Azure o tramite PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Visualizzare gli utenti ripristinati nel portale di Azure

1. [Accedere all'interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account di amministratore utenti nell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra** selezionare **Tutti gli utenti** e verificare che gli utenti ripristinati siano presenti nell'elenco.

### <a name="view-users-with-powershell"></a>Visualizzare gli utenti con PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Gli utenti ripristinati dovrebbero essere presenti nell'elenco.

## <a name="next-steps"></a>Passaggi successivi

- [Importazione di utenti in blocco](users-bulk-add.md)
- [Eliminazione di utenti in blocco](users-bulk-delete.md)
- [Scaricare l'elenco degli utenti](users-bulk-download.md)
