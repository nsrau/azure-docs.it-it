---
title: Gestire le impostazioni dell'account di archiviazione nel portale di Azure - Archiviazione di Azure | Microsoft Docs
description: Informazioni su come gestire le impostazioni dell'account di archiviazione nel portale di Azure, inclusi la configurazione delle impostazioni del controllo di accesso, la rigenerazione delle chiavi di accesso dell'account, il cambiamento del livello di accesso o la modifica del tipo di replica usata dall'account. Viene anche illustrato come eliminare un account di archiviazione nel portale.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 3c01cc870b20c8256b215eb700548e6cd69ad0d5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749008"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Gestire le impostazioni dell'account di archiviazione nel portale di Azure

Nel [portale di Azure](https://portal.azure.com) sono disponibili svariate impostazioni per l'account di archiviazione. Questo articolo descrive alcune delle impostazioni e usarle.

## <a name="access-control"></a>Controllo di accesso

Archiviazione di Azure supporta l'autorizzazione con Azure Active Directory per l'archiviazione BLOB e l'archiviazione di accodamento tramite il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni sull'autorizzazione con Azure AD, vedere [autorizzare l'accesso a BLOB e code di Azure usando Azure Active Directory](storage-auth-aad.md).

Le impostazioni di **Controllo di accesso** nel portale di Azure consentono di assegnare in modo semplice i ruoli Controllo degli accessi in base al ruolo a utenti, gruppi, entità servizio e identità gestite. Per ulteriori informazioni sull'assegnazione di ruoli RBAC, vedere [gestire i diritti di accesso ai dati BLOB e di Accodamento con RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Tag

Archiviazione di Azure supporta i tag di Azure Resource Manager per l'organizzazione delle risorse di Azure con una tassonomia personalizzata. È possibile applicare i tag agli account di archiviazione per poterli raggruppare in modo logico nella sottoscrizione.

Per gli account di archiviazione, un nome di tag è limitato a 128 caratteri e un valore a 256 caratteri.

Per altre informazioni, vedere [Usare tag per organizzare le risorse di Azure](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Chiavi di accesso

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso dell'account di archiviazione a 512 bit. Queste chiavi possono essere usate per autorizzare l'accesso all'account di archiviazione tramite chiave condivisa. È possibile ruotare e rigenerare le chiavi senza interrompere le applicazioni e Microsoft consiglia di farlo regolarmente.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Visualizzare le chiavi dell'account e la stringa di connessione

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso

Microsoft consiglia di rigenerare periodicamente le chiavi di accesso per garantire la sicurezza dell'account di archiviazione. Vengono assegnate due chiavi di accesso che è quindi possibile ruotare. Ruotando le chiavi, l'applicazione può mantenere l'accesso ad Archiviazione di Azure per tutta la durata del processo. 

> [!WARNING]
> La rigenerazione delle chiavi di accesso può influire sulle applicazioni o sui servizi di Azure che dipendono dalla chiave dell'account di archiviazione. I client che usano la chiave dell'account per accedere all'account di archiviazione devono essere aggiornati per usare la nuova chiave, inclusi servizi multimediali, applicazioni cloud, desktop e per dispositivi mobili e applicazioni di interfaccia utente grafica per Archiviazione di Azure, ad esempio [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

Per ruotare le chiavi dell'account di archiviazione, seguire questa procedura:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per usare la chiave secondaria.
2. Rigenerare la chiave di accesso primaria per l'account di archiviazione. Nel pannello **Chiavi di accesso** del portale di Azure fare clic su **Rigenerare Key1** e quindi su **Sì** per confermare che si vuole generare una nuova chiave.
3. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
4. Rigenerare la chiave di accesso secondaria nello stesso modo.

## <a name="account-configuration"></a>Configurazione dell'account

Dopo aver creato un account di archiviazione, è possibile modificarne la configurazione. È ad esempio possibile modificare il modo in cui i dati vengono replicati o il livello di accesso dell'account da frequente a sporadico. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione, quindi in **Impostazioni** trovare e fare clic su **Configurazione** per visualizzare e/o modificare la configurazione dell'account.

La modifica della configurazione dell'account di archiviazione può comportare costi aggiuntivi. Per informazioni più dettagliate, vedere la pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione

Per rimuovere un account di archiviazione che non si usa più, passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com)e fare clic su **Elimina**. Se si elimina un account di archiviazione, viene eliminato l'intero account, inclusi tutti i dati in esso contenuti.

> [!WARNING]
> Non è possibile ripristinare un account di archiviazione eliminato, né recuperare gli elementi che conteneva prima dell'eliminazione. Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Lo stesso vale per tutte le risorse nell'account: dopo aver eliminato un BLOB, una tabella, una coda o un file, non è più possibile recuperarlo.
> 

Se si prova a eliminare un account di archiviazione associato a una macchina virtuale di Azure, potrebbe essere visualizzato un errore che informa che l'account di archiviazione è ancora in uso. Per risolvere questo errore, vedere l'articolo su come [risolvere gli errori durante l'eliminazione degli account di archiviazione](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md)
- [Creare un account di archiviazione](storage-quickstart-create-account.md)
