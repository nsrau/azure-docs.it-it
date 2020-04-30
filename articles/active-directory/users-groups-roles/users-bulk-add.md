---
title: Creare utenti in blocco nel portale di Azure Active Directory | Microsoft Docs
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure AD in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03f6e3d6edde51598b1d148469aceb1ff3b3d636
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203403"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Creazione bulk di utenti in Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di creazione ed eliminazione di utenti in blocco e supporta il download di elenchi di utenti. Basta compilare il modello con valori delimitati da virgole (CSV) che è possibile scaricare dal portale di Azure AD.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per creare in blocco gli utenti nel portale di amministrazione, è necessario effettuare l'accesso come amministratore globale o Amministratore utenti.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV di caricamento bulk per semplificare la creazione di Azure AD utenti in blocco. Il modello CSV scaricato potrebbe essere simile a questo esempio:

![Foglio di calcolo per il caricamento e la chiamata che spiega lo scopo e i valori per ogni riga e colonna](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>Struttura del modello CSV

Le righe in un modello CSV scaricato sono le seguenti:

- **Numero di versione**: la prima riga contenente il numero di versione deve essere inclusa nel file CSV di caricamento.
- **Intestazioni di colonna** &lt;: il formato delle intestazioni di colonna è il *nome* &gt; dell'elemento [PropertyName &lt;] *obbligatorio o vuoto*&gt;. Ad esempio: `Name [displayName] Required`. Alcune versioni precedenti del modello potrebbero presentare variazioni minime.
- **Esempi di riga**: sono stati inclusi nel modello una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate oppure il caricamento non può essere elaborato.
- Le colonne obbligatorie sono elencate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Eventuali colonne aggiuntive aggiunte verranno ignorate e non elaborate.
- Si consiglia di scaricare la versione più recente del modello CSV il più spesso possibile.

## <a name="to-create-users-in-bulk"></a>Per creare utenti in blocco

1. [Accedere all'organizzazione Azure ad](https://aad.portal.azure.com) con un account amministratore dell'organizzazione.
1. In Azure ad selezionare **utenti** > **creazione bulk**.
1. Nella pagina **creazione bulk utente** selezionare **download** per ricevere un file con valori delimitati da virgole (CSV) di proprietà utente e quindi aggiungere Aggiungi utenti che si desidera creare.

   ![Selezionare un file CSV locale in cui elencare gli utenti che si desidera aggiungere](./media/users-bulk-add/upload-button.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente che si vuole creare. Gli unici valori obbligatori sono **nome**, **nome dell'entità utente**, **password iniziale** e **accesso a blocchi (sì/no)**. Salvare quindi il file.

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. Nella pagina **creazione bulk utente** , in caricare il file CSV, selezionare il file. Quando si seleziona il file e si fa clic su **Invia**, viene avviata la convalida del file CSV.
1. Una volta convalidato il contenuto del file, il **file verrà caricato correttamente**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Submit (Invia** ) per avviare l'operazione bulk di Azure che importa i nuovi utenti.
1. Al termine dell'operazione di importazione, verrà visualizzata una notifica dello stato del processo dell'operazione bulk.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **risultati operazione bulk** . Il file contiene il motivo per ogni errore.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste bulk in sospeso nella pagina **risultati operazione bulk** .

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

A questo punto, è possibile verificare che gli utenti creati siano presenti nell'organizzazione Azure AD nell'portale di Azure o tramite PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verificare gli utenti nella portale di Azure

1. [Accedere al centro di amministrazione di Azure ad](https://aad.portal.azure.com) con un account amministratore dell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra**selezionare **tutti gli utenti** e verificare che gli utenti creati siano elencati.

### <a name="verify-users-with-powershell"></a>Verificare gli utenti con PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Si noterà che gli utenti creati sono elencati.

## <a name="bulk-import-service-limits"></a>Limiti del servizio di importazione bulk

Ogni attività bulk per la creazione di utenti può essere eseguita per un massimo di un'ora. Questo consente la creazione bulk di almeno 50.000 utenti.

## <a name="next-steps"></a>Passaggi successivi

- [Eliminare utenti in blocco](users-bulk-delete.md)
- [Scarica l'elenco di utenti](users-bulk-download.md)
- [Ripristinare utenti in blocco](users-bulk-restore.md)
