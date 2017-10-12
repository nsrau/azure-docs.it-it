---
title: Elemento FileUpload dell'interfaccia utente di Applicazione gestita di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Common.FileUpload dell'interfaccia utente per le applicazioni gestite di Azure
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.openlocfilehash: bd5bae5678e2ec12fd1ddd2a81e63acca08da2c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoftcommonfileupload-ui-element"></a>Elemento Microsoft.Common.FileUpload dell'interfaccia utente
Controllo che consente a un utente di specificare uno o più file da caricare. Usare questo elemento quando si [crea un'applicazione Azure gestita](managed-application-publishing.md).

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- `constraints.accept` specifica i tipi di file visualizzati nella finestra di dialogo del browser relativa ai file. Per informazioni sui valori consentiti, vedere la [specifica HTML5](http://www.w3.org/TR/html5/forms.html#attr-input-accept). Il valore predefinito è **null**.
- Se la proprietà `options.multiple` è impostata su **true**, l'utente è autorizzato a selezionare più di un file nella finestra di dialogo del browser relativa ai file. Il valore predefinito è **false**.
- Questo elemento supporta il caricamento dei file in due modalità in base al valore di `options.uploadMode`. Se il valore **file** è specificato, l'output include i contenuti del file sotto forma di BLOB. Se il valore **url** è specificato, il file viene caricato in una posizione temporanea e l'output contiene l'URL del BLOB. I BLOB temporanei verranno eliminati dopo 24 ore. Il valore predefinito è **file**.
- Il valore di `options.openMode` determina la modalità di lettura del file. Se si prevede che il file sia in testo normale, specificare **text**. In caso contrario, specificare **binary**. Il valore predefinito è **text**.
- Se la proprietà `options.uploadMode` è impostata su **file** e `options.openMode` su **binary**, l'output avrà una codifica Base64.
- `options.encoding` specifica la codifica da usare per la lettura del file. Il valore predefinito è **UTF-8** e viene usato solo quando la proprietà `options.openMode` è impostata su **text**.

## <a name="sample-output"></a>Output di esempio
Se options.multiple è false e options.uploadMode è file, l'output include i contenuti del file sotto forma di stringa JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Se options.multiple è true e options.uploadMode è file, l'output include i contenuti del file sotto forma di matrice JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Se options.multiple è false e options.uploadMode è url, l'output include un URL sotto forma di stringa JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Se options.multiple è true e options.uploadMode è url, l'output include un elenco di URL sotto forma di matrice JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Durante il test di CreateUiDefinition, alcuni browser, ad esempio Google Chrome, troncano gli URL generati dall'elemento Microsoft.Common.FileUpload nella console del browser. Potrebbe essere necessario fare clic con il pulsante destro del mouse sui singoli collegamenti per copiare gli URL completi.


## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](managed-application-overview.md).
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](managed-application-createuidefinition-elements.md).
