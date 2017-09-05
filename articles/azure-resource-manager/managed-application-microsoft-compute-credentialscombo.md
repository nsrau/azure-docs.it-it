---
title: Elemento CredentialsCombo dell'interfaccia utente dell'applicazione gestita di Azure | Microsoft Docs
description: Illustra l'elemento Microsoft.Compute.CredentialsCombo dell'interfaccia utente per le applicazioni gestite di Azure
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
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 254f383ee6f7cb9f7051fa135d85319a22c3c369
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017

---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Elemento Microsoft.Compute.CredentialsCombo dell'interfaccia utente
Si tratta di un gruppo di controlli con convalida predefinita per le chiavi pubbliche SSH e le password di Windows e Linux. Usare questo elemento quando si [crea un'applicazione Azure gestita](managed-application-publishing.md).

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>Schema
Se `osPlatform` è **Windows**, viene usato lo schema seguente:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Se `osPlatform` è **Linux**, viene usato lo schema seguente:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- È necessario specificare `osPlatform`, che può essere **Windows** o **Linux**.
- Se `constraints.required` è impostato su **true**, perché la convalida abbia esito positivo le caselle di testo della password o della chiave pubblica SSH devono contenere valori. Il valore predefinito è **true**.
- Se la proprietà `options.hideConfirmation` è impostata su **true**, la seconda casella di testo per la conferma della password dell'utente è nascosta. Il valore predefinito è **false**.
- Se `options.hidePassword` è impostato su **true**, l'opzione per l'uso dell'autenticazione della password è nascosta. È possibile usarla solo quando `osPlatform` è **Linux**. Il valore predefinito è **false**.
- La proprietà `customPasswordRegex` permette di implementare vincoli aggiuntivi sulle password consentite. La stringa in `customValidationMessage` viene visualizzata quando una password non supera la convalida personalizzata. Il valore predefinito per entrambe le proprietà è **null**.

## <a name="sample-output"></a>Output di esempio
Se `osPlatform` è **Windows** oppure l'utente ha specificato una password anziché una chiave pubblica SSH, è previsto l'output seguente:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Se l'utente ha specificato una chiave pubblica SSH, è previsto l'output seguente:
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](managed-application-overview.md).
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](managed-application-createuidefinition-elements.md).

