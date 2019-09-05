---
title: CreateUiDefinition. JSON per l'esperienza di creazione di un'applicazione gestita di Azure | Microsoft Docs
description: Illustra come creare definizioni dell'interfaccia utente per le applicazioni gestite di Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 013e861bb93d76454f2f0fd9c36259197dd671b9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308657"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition. JSON per l'esperienza di creazione di un'applicazione gestita di Azure

Questo documento introduce i concetti di base del file **createUiDefinition. JSON** che portale di Azure usa per definire l'interfaccia utente durante la creazione di un'applicazione gestita.

Il modello è il seguente:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

CreateUiDefinition contiene sempre tre proprietà: 

* handler
* version
* parameters

Il gestore deve essere `Microsoft.Azure.CreateUIDef`sempre e la versione supportata più recente è. `0.1.2-preview`

Lo schema della proprietà parameters dipende dalla combinazione delle proprietà handler e version specificate. Per le applicazioni gestite sono supportate le proprietà `basics`, `steps` e `outputs`. Le proprietà basics e steps contengono [elementi](create-uidefinition-elements.md), ad esempio caselle di testo ed elenchi a discesa, da visualizzare nel portale di Azure. La proprietà outputs viene usata per il mapping dei valori di output degli elementi specificati ai parametri del modello di distribuzione Azure Resource Manager.

L'inclusione di `$schema` è consigliata ma facoltativa. Se specificato, il valore per la proprietà `version` deve corrispondere alla versione nell'URI di `$schema`.

È possibile usare un editor JSON per creare il createUiDefinition, quindi testarlo nella [sandbox createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) per visualizzarne l'anteprima. Per altre informazioni sulla sandbox, vedere [testare l'interfaccia del portale per le applicazioni gestite di Azure](test-createuidefinition.md).

## <a name="basics"></a>Generale

Le nozioni di base sono il primo passaggio generato quando il portale di Azure analizza il file. Oltre a visualizzare gli elementi specificati in `basics`, il portale inserisce elementi che consentono agli utenti di scegliere la sottoscrizione, il gruppo di risorse e la posizione della distribuzione. Quando possibile, gli elementi che eseguono query sui parametri a livello di distribuzione, ad esempio il nome di un cluster o le credenziali di amministratore, dovrebbero procedere in questo passaggio.

## <a name="steps"></a>Passaggi

La proprietà steps può contenere zero o più passaggi aggiuntivi da visualizzare dopo la proprietà basics, ognuna delle quali contiene uno o più elementi. Prendere in considerazione l'aggiunta di passaggi per ogni ruolo o livello dell'applicazione in fase di distribuzione. Ad esempio, aggiungere un passaggio per gli input del nodo master e un passaggio per i nodi del ruolo di lavoro in un cluster.

## <a name="outputs"></a>Output

Il portale di Azure usa la proprietà `outputs` per il mapping di elementi da `basics` e `steps` ai parametri del modello di distribuzione Azure Resource Manager. Le chiavi di questo dizionario sono i nomi dei parametri del modello e i valori sono le proprietà degli oggetti di output dagli elementi a cui si fa riferimento.

Per impostare il nome della risorsa applicazione gestita, è necessario includere un valore denominato `applicationResourceName` nella proprietà di output. Se non si imposta questo valore, l'applicazione assegna un GUID per il nome. È possibile includere una casella di testo nell'interfaccia utente che richiede un nome all'utente.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Tipi di risorsa

Per filtrare i percorsi disponibili solo per i percorsi che supportano i tipi di risorse da distribuire, fornire una matrice di tipi di risorse. Se si specifica più di un tipo di risorsa, vengono restituiti solo i percorsi che supportano tutti i tipi di risorsa. Questa proprietà è facoltativa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Funzioni

CreateUiDefinition fornisce [funzioni](create-uidefinition-functions.md) per l'uso di input e output degli elementi e funzionalità come le istruzioni condizionali. Queste funzioni sono simili nella sintassi e nella funzionalità per Azure Resource Manager funzioni di modello.

## <a name="next-steps"></a>Passaggi successivi

Il file createUiDefinition.json è contraddistinto da uno schema semplice ma supporta numerosi elementi e funzioni, illustrati in modo dettagliato nei documenti seguenti:

- [Elementi](create-uidefinition-elements.md)
- [Funzioni](create-uidefinition-functions.md)

Uno schema JSON corrente per createUiDefinition è disponibile qui: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Per un esempio di file di interfaccia utente, vedere [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
