---
title: Associare il numero ASN peer alla sottoscrizione di Azure tramite PowerShellAssociate peer ASN to Azure subscription using PowerShell
titleSuffix: Azure
description: Associare il numero ASN peer alla sottoscrizione di Azure tramite PowerShellAssociate peer ASN to Azure subscription using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908993"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Associare il numero ASN peer alla sottoscrizione di Azure tramite PowerShellAssociate peer ASN to Azure subscription using PowerShell

Prima di inviare una richiesta di peering, è necessario associare la sottoscrizione ASN ad Azure usando la procedura seguente.

Se si preferisce, è possibile completare questa guida utilizzando il [portale](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Creare PeerASN per associare l'ASN alla sottoscrizione di AzureCreate PeerASN to associate your ASN with Azure Subscription

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Eseguire la registrazione per il provider di risorse di peeringRegister for peering resource provider
Eseguire la registrazione per il provider di risorse di peering nella sottoscrizione usando il comando seguente. Se non si esegue questa operazione, le risorse di Azure necessarie per configurare il peering non sono accessibili.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

È possibile controllare lo stato di registrazione utilizzando i comandi seguenti:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Attendere *che RegistrationState* trasformi "Registrato" prima di procedere. Potrebbero essere decollati da 5 a 30 minuti dopo l'esecuzione del comando.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Aggiornare le informazioni peer associate a questa sottoscrizioneUpdate the peer information associated with this subscription

Di seguito è riportato un esempio per aggiornare le informazioni sul peer.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Nome corrisponde al nome della risorsa e può essere qualsiasi cosa tu scelga. Tuttavia, -peerName corrisponde al nome della società e deve essere il più vicino possibile al profilo PeeringDB. Si noti che il valore per -peerName supporta solo i caratteri a-z, A-z e lo spazio.

Una sottoscrizione può avere più ASN. Aggiornare le informazioni di peering per ogni numero ASN. Assicurarsi che "name" sia univoco per ogni numero ASN.

I peer devono avere un profilo completo e aggiornato in [PeeringDB](https://www.peeringdb.com). Utilizziamo queste informazioni durante la registrazione per convalidare i dettagli del peer, come le informazioni NOC, le informazioni di contatto tecniche e la loro presenza presso le strutture di peering, ecc.

Si noti che al posto di **"subscriptionId"** nell'output precedente, verrà visualizzato l'ID sottoscrizione effettivo.

## <a name="view-status-of-a-peerasn"></a>Visualizzare lo stato di un PeerASN

Verificare lo stato di convalida ASN utilizzando il comando seguente:

```powershell
Get-AzPeerAsn
```

Di seguito è riportata una risposta di esempio:Below is an example response:
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
> Attendi che ValidationState trasformi "Approved" prima di inviare una richiesta di peering. La approvazione potrebbe richiedere fino a 12 ore.

## <a name="modify-peerasn"></a>Modifica PeerAsn
È possibile modificare le informazioni di contatto NOC in qualsiasi momento.

Di seguito è riportato un esempio:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Elimina PeerAsn
L'eliminazione di un PeerASN non è attualmente supportata. Se è necessario eliminare PeerASN, contattare [Microsoft peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering diretto](howto-direct-powershell.md)
* [Convertire un peering diretto legacy in una risorsa di Azure](howto-legacy-direct-powershell.md)
* [Creare o modificare il peering di Exchange](howto-exchange-powershell.md)
* [Convertire un peering di Exchange legacy in una risorsa di Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, visita Le domande frequenti sul [peering Internet](faqs.md)
