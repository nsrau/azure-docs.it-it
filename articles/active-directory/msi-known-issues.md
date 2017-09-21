---
title: "Problemi noti di Identità del servizio gestito per Azure Active Directory"
description: "Problemi noti di Identità del servizio gestito per Azure Active Directory."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 09/14/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 6fb8317f33ec8c36af8553466665fb2088c49527
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Problemi noti di Identità del servizio gestito per Azure Active Directory

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Il pannello di configurazione non viene visualizzato nel portale di Azure

Se il pannello di configurazione della macchina virtuale non viene visualizzato nella macchina virtuale vuol dire che l'Identità del servizio gestito non è stata ancora abilitata nel portale della propria area geografica.  Ricontrollare in seguito.  È anche possibile abilitare l'Identità del servizio gestito per la macchina virtuale tramite [PowerShell](msi-qs-configure-powershell-windows-vm.md) o l'[interfaccia della riga di comando di Azure](msi-qs-configure-cli-windows-vm.md).

## <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Impossibile assegnare l'accesso alle macchine virtuali nel pannello Controllo di accesso (IAM)

Se la **macchina virtuale** non viene visualizzata nel portale di Azure come opzione per **Assegna accesso a** in **Controllo di accesso (IAM)** > **Aggiungi autorizzazioni**, vuol dire che l'Identità del servizio gestito non è stata ancora abilitata nel portale della propria area geografica. Ricontrollare in seguito.  È comunque possibile selezionare l'Identità del servizio gestito per l'assegnazione di ruolo eseguendo una ricerca dell'entità servizio dell'Identità del servizio gestito.  Immettere il nome della macchina virtuale nel campo **Seleziona** per visualizzare l'entità servizio nei risultati della ricerca.

## <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>La macchina virtuale non viene avviata dopo essere stata spostata dal gruppo di risorse o dalla sottoscrizione

Se si sposta una macchina virtuale in esecuzione, questa continuerà a eseguire durante lo spostamento. Tuttavia, dopo lo spostamento, se la macchina virtuale viene arrestata e riavviata, non si avvierà. Questo problema si verifica perché la macchina virtuale non aggiorna il riferimento all'Identità del servizio gestito e continua a puntare a questa nel gruppo di risorse precedente.

**Soluzione alternativa** 
 
Attivare un aggiornamento nella macchina virtuale così da poter ottenere i valori corretti per l'Identità del servizio gestito. È possibile apportare una modifica alla proprietà della macchina virtuale per aggiornare il riferimento all'identità del servizio gestito. Ad esempio, è possibile impostare un nuovo valore di tag nella macchina virtuale con il comando seguente:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Questo comando imposta un nuovo tag "fixVM" con valore 1 nella macchina virtuale. 
 
Impostando questa proprietà, la macchina virtuale si aggiorna inserendo l'URI della risorsa dell'Identità del servizio gestito corretto. La macchina virtuale dovrebbe a questo punto avviarsi. 
 
Dopo aver avviato la macchina virtuale, il tag può essere rimosso tramite il seguente comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>L'Identità del servizio gestito funziona con Active Directory Authentication Library (ADAL) o con Microsoft Authentication Library (MSAL)?

No, l'Identità del servizio gestito non è ancora integrata con ADAL o MSAL.

