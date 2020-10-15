---
title: Associare l'ASN peer alla sottoscrizione di Azure tramite PowerShell
titleSuffix: Azure
description: Associare l'ASN peer alla sottoscrizione di Azure tramite PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aed079c467139ac5819951c5895ba753ee38ae2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89067740"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Associare l'ASN peer alla sottoscrizione di Azure tramite PowerShell

Prima di inviare una richiesta di peering, è necessario associare l'ASN alla sottoscrizione di Azure seguendo la procedura descritta.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Creare Peerasn sugli per associare l'ASN alla sottoscrizione di Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Eseguire la registrazione per il provider di risorse di peering
Eseguire la registrazione per il provider di risorse di peering nella sottoscrizione usando il comando seguente. Se non si esegue questa operazione, le risorse di Azure necessarie per configurare il peering non sono accessibili.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

È possibile controllare lo stato della registrazione usando i comandi seguenti:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Attendere che *RegistrationState* diventi "registrato" prima di procedere. Dopo l'esecuzione del comando potrebbero essere necessari da 5 a 30 minuti.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Aggiornare le informazioni del peer associate a questa sottoscrizione

Di seguito è riportato un esempio per aggiornare le informazioni del peer.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Name corrisponde al nome della risorsa e può essere qualsiasi elemento selezionato. Tuttavia,-peerName corrisponde al nome della società e deve essere il più vicino possibile al profilo PeeringDB. Si noti che il valore per-peerName supporta solo i caratteri a-z, A-Z e lo spazio.

Una sottoscrizione può avere più ASN. Aggiornare le informazioni di peering per ogni ASN. Verificare che "Name" sia univoco per ogni ASN.

I peer dovrebbero avere un profilo completo e aggiornato in [PeeringDB](https://www.peeringdb.com). Queste informazioni vengono utilizzate durante la registrazione per convalidare i dettagli del peer, ad esempio le informazioni relative ai NOC, le informazioni di contatto tecnico e la loro presenza presso le strutture di peering e così via.

Si noti che al posto di **{SubscriptionId}** nell'output precedente verrà visualizzato l'ID sottoscrizione effettivo.

## <a name="view-status-of-a-peerasn"></a>Visualizzare lo stato di un Peerasn sugli

Verificare lo stato di convalida ASN usando il comando seguente:

```powershell
Get-AzPeerAsn
```

Di seguito è riportato un esempio di risposta:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Attendere che lo stato di convalida sia "Approvato" prima di inviare una richiesta di peering. L'approvazione può richiedere fino a 12 ore.

## <a name="modify-peerasn"></a>Modificare l'oggetto PeerAsn
È possibile modificare le informazioni di contatto del NOC in qualsiasi momento.

Di seguito è riportato un esempio:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Eliminare un oggetto PeerAsn
L'eliminazione di un Peerasn sugli non è attualmente supportata. Se è necessario eliminare Peerasn sugli, contattare il [peering Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto](howto-direct-powershell.md)
* [Convertire un peering diretto legacy in una risorsa di Azure](howto-legacy-direct-powershell.md)
* [Creare o modificare il peering di Exchange](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md)
