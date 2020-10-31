---
title: Elemento KeyVaultCertificateSelector dell'interfaccia utente
description: Descrive l'elemento Microsoft. KeyVaultCertificateSelector dell'interfaccia utente per portale di Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101222"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Elemento Microsoft. KeyVaultCertificateSelector dell'interfaccia utente

Controllo per la selezione di un certificato di Key Vault.

## <a name="ui-sample"></a>Esempio di interfaccia utente

All'utente viene visualizzata l'opzione per selezionare un certificato disponibile.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft. Vault. KeyVaultCertificateSelector":::

Dopo aver selezionato **Seleziona un certificato** , l'utente può specificare un insieme di credenziali delle chiavi e un certificato dall'insieme di credenziali delle chiavi.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft. Vault. KeyVaultCertificateSelector":::

Il controllo viene aggiornato per visualizzare l'insieme di credenziali delle chiavi selezionato e il nome del certificato.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft. Vault. KeyVaultCertificateSelector":::

## <a name="schema"></a>SCHEMA

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Output di esempio

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
