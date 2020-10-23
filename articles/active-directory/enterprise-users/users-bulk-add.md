---
title: Creare utenti in blocco nel portale di Azure Active Directory | Microsoft Docs
description: Aggiungere utenti in blocco nell'interfaccia di amministrazione di Azure AD in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/11/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8e6ab8fd726eaba309f0949020139901dd3712f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375388"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Creare utenti in blocco in Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di creazione ed eliminazione di utenti in blocco e supporta il download di elenchi di utenti. Basta compilare il modello con valori delimitati da virgole (CSV) che è possibile scaricare dal portale di Azure AD.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per creare in blocco gli utenti nel portale di amministrazione, è necessario effettuare l'accesso come amministratore globale o amministratore utenti.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV di caricamento in blocco per creare correttamente in blocco gli utenti di Azure AD. Il modello CSV scaricato sarà simile a questo esempio:

![Foglio di calcolo per il caricamento con callout che illustrano lo scopo e i valori di ogni riga e colonna](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> Se si aggiunge una sola voce usando il modello CSV, è necessario mantenere la riga 3 e aggiungere la nuova voce alla riga 4.

### <a name="csv-template-structure"></a>Struttura del modello CSV

Un modello CSV scaricato contiene le righe descritte di seguito:

- **Numero di versione**: nel file CSV di caricamento deve essere inclusa la prima riga contenente il numero di versione.
- **Intestazioni di colonna**: il formato delle intestazioni di colonna è &lt;*Nome elemento*&gt; [NomeProprietà] &lt;*Required o vuoto*&gt;. Ad esempio: `Name [displayName] Required`. Alcune versioni precedenti del modello potrebbero presentare lievi variazioni.
- **Riga di esempi**: nel modello è stata inclusa una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate. In caso contrario, il caricamento non può essere elaborato.
- Le colonne obbligatorie sono riportate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Eventuali colonne aggiuntive aggiunte saranno ignorate e non verranno elaborate.
- È consigliabile scaricare la versione più recente del modello CSV il più spesso possibile.
- Assicurarsi di controllare che non sia presente uno spazio vuoto non previsto prima/dopo qualsiasi campo. Per il **nome dell'entità utente**, con tale spazio vuoto si verificherà un errore di importazione.

## <a name="to-create-users-in-bulk"></a>Per creare utenti in blocco

1. [Accedere all'organizzazione Azure AD](https://aad.portal.azure.com) con un account che sia un amministratore utenti nell'organizzazione.
1. In Azure AD selezionare **Utenti** > **Creazione in blocco**.
1. Nella pagina **Crea utenti in blocco** selezionare **Scarica** per ricevere un file con valori delimitati da virgole (CSV) di proprietà utente, quindi aggiungere gli utenti che si vuole creare.

   ![Selezionare un file CSV locale in cui elencare gli utenti da aggiungere](./media/users-bulk-add/upload-button.png)

1. Aprire il file CSV e aggiungere una riga per ogni utente da creare. Gli unici valori obbligatori sono **Nome**, **Nome dell'entità utente**, **Password iniziale** e **Blocca l'accesso (Sì/No)** . Salvare quindi il file.

   [![Il file CSV contiene i nomi e gli ID degli utenti da creare](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. Nella pagina **Crea utenti in blocco** individuare il file in Caricare il file CSV. Quando si seleziona il file e si fa clic su **Invia** viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Quando il file supera la convalida, selezionare **Invia** per avviare l'operazione in blocco di Azure per l'importazione dei nuovi utenti.
1. Al termine dell'operazione di importazione, verrà visualizzata una notifica relativa allo stato processo dell'operazione in blocco.

Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina **Risultati dell'operazione in blocco**. Il file contiene il motivo di ogni errore. L'invio del file deve corrispondere al modello usato e includere i nomi esatti della colonna.

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato di tutte le richieste in sospeso di operazioni in blocco nella pagina **Risultati dell'operazione in blocco**.

   [![Verificare lo stato della creazione nella pagina Risultati dell'operazione in blocco](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

Quindi, è possibile verificare che gli utenti creati siano presenti nell'organizzazione Azure AD usando il portale di Azure o PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verificare gli utenti nel portale di Azure

1. [Accedere all'interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account di amministratore utenti nell'organizzazione.
1. Nel riquadro di spostamento selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Utenti**.
1. In **Mostra** selezionare **Tutti gli utenti** e verificare che gli utenti creati siano elencati.

### <a name="verify-users-with-powershell"></a>Verificare gli utenti con PowerShell

Eseguire il comando seguente:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Gli utenti creati dovrebbero essere elencati.

## <a name="bulk-import-service-limits"></a>Limiti del servizio di importazione in blocco

Ogni attività in blocco per la creazione di utenti può essere eseguita per al massimo un'ora. Questo consente la creazione in blocco di almeno 50.000 utenti.

## <a name="next-steps"></a>Passaggi successivi

- [Eliminare utenti in blocco](users-bulk-delete.md)
- [Scaricare l'elenco degli utenti](users-bulk-download.md)
- [Ripristinare utenti in blocco](users-bulk-restore.md)
