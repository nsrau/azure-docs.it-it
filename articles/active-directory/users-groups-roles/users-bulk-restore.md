---
title: Ripristinare in blocco gli utenti eliminati nel portale di Azure Active Directory | Microsoft Docs
description: Ripristinare in blocco gli utenti eliminati nell'interfaccia di amministrazione di Azure AD in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11f35c7615135f5aa6c63d5d05898d139df61d0d
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203301"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Ripristino bulk degli utenti eliminati in Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di ripristino utente bulk e supporta il download di elenchi di utenti, gruppi e membri del gruppo.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV per ripristinare correttamente Azure AD utenti in blocco. Il modello CSV scaricato potrebbe essere simile a questo esempio:

![Foglio di calcolo per il caricamento e la chiamata che spiega lo scopo e i valori per ogni riga e colonna](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Struttura del modello CSV

Le righe in un modello CSV scaricato sono le seguenti:

- **Numero di versione**: la prima riga contenente il numero di versione deve essere inclusa nel file CSV di caricamento.
- **Intestazioni di colonna** &lt;: il formato delle intestazioni di colonna è il *nome* &gt; dell'elemento [PropertyName &lt;] *obbligatorio o vuoto*&gt;. Ad esempio: `Object ID [objectId] Required`. Alcune versioni precedenti del modello potrebbero presentare variazioni minime.
- **Esempi di riga**: sono stati inclusi nel modello una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate oppure il caricamento non può essere elaborato.
- Le colonne obbligatorie sono elencate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Eventuali colonne aggiuntive aggiunte verranno ignorate e non elaborate.
- Si consiglia di scaricare la versione più recente del modello CSV il più spesso possibile.

## <a name="to-bulk-restore-users"></a>Per eseguire il ripristino bulk degli utenti

1. [Accedere all'organizzazione Azure ad](https://aad.portal.azure.com) con un account che sia un amministratore utente nell'organizzazione Azure ad.
1. In Azure ad selezionare **utenti** > **eliminati**.
1. Nella pagina **utenti eliminati** selezionare **ripristino Bulk** per caricare un file CSV valido di proprietà degli utenti da ripristinare.

   ![Selezionare il comando di ripristino Bulk nella pagina utenti eliminati](./media/users-bulk-restore/bulk-restore.png)

1. Aprire il modello CSV e aggiungere una riga per ogni utente che si desidera ripristinare. L'unico valore richiesto è **ObjectID**. Salvare quindi il file.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera aggiungere](./media/users-bulk-restore/upload-button.png)

1. Nella pagina **ripristino Bulk** , in **caricare il file CSV**, selezionare il file. Quando si seleziona il file e si fa clic su **Invia**, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare l'operazione di Azure bulk per il ripristino degli utenti.
1. Al termine dell'operazione di ripristino, verrà visualizzata una notifica di esito positivo dell'operazione bulk.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **risultati operazione bulk** . Il file contiene il motivo per ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk** .

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

A questo punto, è possibile verificare che gli utenti ripristinati esistano nell'organizzazione del Azure AD nell'portale di Azure o tramite PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Visualizzare gli utenti ripristinati nella portale di Azure

1. [Accedere al centro di amministrazione di Azure ad](https://aad.portal.azure.com) con un account amministratore dell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare **tutti gli utenti** e verificare che gli utenti ripristinati siano elencati.

### <a name="view-users-with-powershell"></a>Visualizzare gli utenti con PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Si noterà che gli utenti ripristinati sono elencati.

## <a name="next-steps"></a>Passaggi successivi

- [Importazione bulk di utenti](users-bulk-add.md)
- [Eliminare utenti in blocco](users-bulk-delete.md)
- [Scarica l'elenco di utenti](users-bulk-download.md)
