---
title: Eliminare in blocco gli utenti nel portale di Azure Active Directory | Microsoft Docs
description: Eliminare gli utenti in blocco nell'interfaccia di amministrazione di Azure in Azure Active Directory
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
ms.openlocfilehash: 3fc393279aaa6b293c2eb29099be45385ad08d9a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731501"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Eliminare in blocco gli utenti in Azure Active Directory

Usando il portale di Azure Active Directory (Azure AD), è possibile rimuovere un numero elevato di membri a un gruppo usando un file con valori delimitati da virgole (CSV) per eliminare in blocco gli utenti.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV per consentire di eliminare correttamente Azure AD utenti in blocco. Il modello CSV scaricato sarà simile a questo esempio:

![Foglio di calcolo per il caricamento con callout che illustrano lo scopo e i valori di ogni riga e colonna](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>Struttura del modello CSV

Un modello CSV scaricato contiene le righe descritte di seguito:

- **Numero di versione**: nel file CSV di caricamento deve essere inclusa la prima riga contenente il numero di versione.
- **Intestazioni di colonna**: il formato delle intestazioni di colonna è &lt;*Nome elemento*&gt; [NomeProprietà] &lt;*Required o vuoto*&gt;. Ad esempio: `User name [userPrincipalName] Required`. Alcune versioni precedenti del modello potrebbero presentare lievi variazioni.
- **Riga di esempi**: nel modello è stata inclusa una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate. In caso contrario, il caricamento non può essere elaborato.
- Le colonne obbligatorie sono riportate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Eventuali colonne aggiuntive aggiunte saranno ignorate e non verranno elaborate.
- È consigliabile scaricare la versione più recente del modello CSV il più spesso possibile.

## <a name="to-bulk-delete-users"></a>Per eliminare in blocco gli utenti

1. [Accedere all'organizzazione Azure AD](https://aad.portal.azure.com) con un account che sia un amministratore utenti nell'organizzazione.
1. In Azure ad selezionare **Users**  >  **Bulk Delete**.
1. Nella pagina **utente di eliminazione in blocco** selezionare **Scarica** per ricevere un file CSV valido di proprietà utente.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera eliminare](./media/users-bulk-delete/bulk-delete.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si desidera eliminare. L'unico valore obbligatorio è il **nome dell'entità utente**. Salvare quindi il file.

   ![Il file CSV contiene i nomi e gli ID degli utenti da eliminare](./media/users-bulk-delete/delete-csv-file.png)

1. Nella pagina **utente di eliminazione in blocco** , in **caricare il file CSV**, selezionare il file. Quando si seleziona il file e si fa clic su Invia, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare l'operazione bulk di Azure che elimina gli utenti.
1. Al termine dell'operazione di eliminazione, viene visualizzata una notifica che l'operazione bulk è riuscita.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **Risultati dell'operazione in blocco**. Il file contiene il motivo di ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste in sospeso di operazioni in blocco nella pagina **Risultati dell'operazione in blocco**.

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

A questo punto, è possibile verificare che gli utenti eliminati esistano nell'organizzazione Azure AD nell'portale di Azure o tramite PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verificare gli utenti eliminati nella portale di Azure

1. Accedere al portale di Azure con un account di amministratore utenti nell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare solo **tutti gli utenti** e verificare che gli utenti eliminati non siano più elencati.

### <a name="verify-deleted-users-with-powershell"></a>Verificare gli utenti eliminati con PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Verificare che gli utenti eliminati non siano più elencati.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti in blocco](users-bulk-add.md)
- [Scaricare l'elenco degli utenti](users-bulk-download.md)
- [Ripristinare utenti in blocco](users-bulk-restore.md)
