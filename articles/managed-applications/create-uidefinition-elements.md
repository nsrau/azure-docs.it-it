---
title: Elementi di definizione per la creazione dell'interfaccia utente di Azure | Microsoft Docs
description: Descrive gli elementi da usare quando si creano definizioni dell'interfaccia utente per il portale di Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: 41a583a77f85bb1524112fa20d9098e18bc4f431
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587939"
---
# <a name="createuidefinition-elements"></a>Elementi di CreateUiDefinition
Questo articolo illustra lo schema e le proprietà per tutti gli elementi supportati di CreateUiDefinition. 

## <a name="schema"></a>SCHEMA

Lo schema per la maggior parte degli elementi è il seguente:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Proprietà | Obbligatorio | DESCRIZIONE |
| -------- | -------- | ----------- |
| name | Sì | Identificatore interno per fare riferimento a un'istanza specifica di un elemento. Il nome dell'elemento viene usato con maggiore frequenza in `outputs`, dove i valori di output degli elementi specificati vengono mappati ai parametri del modello. È anche possibile usarlo per associare il valore di output di un elemento al valore `defaultValue` di un altro elemento. |
| type | Sì | Controllo dell'interfaccia utente da sottoporre a rendering per l'elemento. Per un elenco di tipi supportati, vedere [Elementi](#elements). |
| label | Sì | Testo visualizzato dell'elemento. Alcuni tipi di elemento includono più etichette, quindi il valore può essere un oggetto contenente più stringhe. |
| defaultValue | No  | Valore predefinito dell'elemento. Alcuni tipi di elementi supportano valori predefiniti complessi, quindi il valore può essere un oggetto. |
| toolTip | No  | Testo da visualizzare nella descrizione comando dell'elemento. Analogamente a `label`, alcuni elementi supportano più stringhe di descrizione comando. I collegamenti inline possono essere incorporati tramite la sintassi di markdown.
| constraints | No  | Una o più proprietà usate per personalizzare il comportamento di convalida dell'elemento. Le proprietà supportate per constraints dipendono dal tipo di elemento. Alcuni tipi di elementi non supportano la personalizzazione del comportamento di convalida e quindi non includono alcuna proprietà constraints. |
| options | No  | Proprietà aggiuntive per la personalizzazione del comportamento dell'elemento. Analogamente a `constraints`, le proprietà supportate dipendono dal tipo di elemento. |
| visible | No  | Indica se l'elemento è visualizzato. Se `true`, l'elemento e gli elementi figlio applicabili vengono visualizzati. Il valore predefinito è `true`. Usare le [funzioni logiche](create-uidefinition-functions.md#logical-functions) per controllare dinamicamente il valore di questa proprietà.

## <a name="elements"></a>Elementi

La documentazione per ogni elemento include un esempio di interfaccia utente, uno schema, commenti sul comportamento dell'elemento, in genere relativi alla convalida e alla personalizzazione supportata, e infine output di esempio.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Passaggi successivi
Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
